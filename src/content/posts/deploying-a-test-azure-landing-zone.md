---
title: "Deploying a Test Azure Landing Zone"
date: 2026-07-15
description: "Notes from standing up a full platform landing zone in a sandbox tenant: no pipeline, no EA billing account, just a local Bicep deployment to learn how the pieces fit together."
draft: true
---

## Introduction
This is a walk through of deployment of an Azure Platform Landing Zone in my personal test environment. 

## Target Use Case
Typically a new DevOps Engineer needs to deploy a greenfield azure landing zone. 
Usually, a standard gold plated landing zone following Microsoft Reference Architecture has some baseline components usually stiched together into provide an "accelerator" to speed up deployment and time to market.

## What a platform landing zone actually is

When someone thinks about an Azure Landing Zone, they think about what they need to run their workloads in Azure. Subscriptions, security logs and vnets. However, it is important to abstract the concepts of
- Platform Landing Zone
- Application Landing Zone

A **platform landing zone** is the centralized layer. Management groups, policy, hub networking, identity, security tooling that every application landing zone sits on. 

It's not a workload environment. It's the scaffolding. 
- management group hierarchy 
- policies inherited down from the root 
- hub VNet with firewall and Bastion 
- logging and role assignments that make everything governable.

## Links to popular Azure Platform Landing Zone Accelerators

You don't need to write this from scratch. Nobody does. There are a handful of accelerators out there that already package the Microsoft reference architecture into deployable code:

- **[Azure/ALZ-Bicep](https://github.com/Azure/ALZ-Bicep)**: Microsoft's own reference implementation. Modular, well-documented, the closest thing to a "vanilla" ALZ in Bicep.
- **[Azure/terraform-azurerm-alz](https://github.com/Azure/terraform-azurerm-alz)**: same conceptual architecture, Terraform flavour, if that's your IaC of choice.
- **[Azure/terraform-azurerm-lz-vending](https://github.com/Azure/terraform-azurerm-lz-vending)**: the subscription vending half, for onboarding application landing zones once the platform is up.
- **Bicep Registry AVM pattern modules** (`avm/ptn/lz/*`): Azure Verified Modules are steadily absorbing ALZ patterns as first-class, versioned registry modules.
- **Insight's internal accelerator** (`azure-landing-zones-bicep`): the one this post is actually based on. Built on the same reference architecture, with an added local dev harness, PSRule validation, and Insight-specific policy/RBAC conventions layered on top.

Most consultancies land on one of these, then fork or wrap it with their own naming conventions, policies, and pipeline. The underlying shape (management groups, hub networking, policy, identity) is basically the same everywhere. The differences are in the packaging.

## What you need to deploy in a typical greenfield Azure Platform Landing Zone

A "gold plated" greenfield platform landing zone is really a handful of layers stacked in dependency order:

- **Management group hierarchy**: the tree everything else attaches policy and RBAC to (`Platform`, `Landing Zones`, `Sandbox`, `Decommissioned`, etc., under your tenant root).
- **Custom role definitions**: if the built-in Azure RBAC roles don't fit your operating model.
- **Hub networking**: a hub VNet, Azure Firewall, Bastion, and (optionally) a VPN or ExpressRoute gateway for on-prem connectivity.
- **Platform management**: Log Analytics workspace, Azure Monitor, diagnostic settings wired up centrally so every subscription forwards logs to one place.
- **Platform identity**: Entra ID groups, PIM-eligible roles (licensing permitting), conditional access baselines.
- **Policy**: the ALZ default policy initiative assignments, plus any custom definitions and exemptions your organisation needs.
- **Role assignments**: RBAC tying identities to management groups/subscriptions.
- **DNS**: private DNS zones for private endpoints, conditional forwarders if you're hybrid.

Everything downstream (application landing zones, workloads) sits on top of this and inherits policy and connectivity from it. Get this layer wrong and you're fixing it at the management group level for every subscription underneath, which is exactly the kind of thing you want to get right once in a test environment before it's real.

## How to get them

For the Insight accelerator specifically:

```bash
git clone https://github.com/Insight-Services-APAC/azure-landing-zones-bicep
cd azure-landing-zones-bicep
```

If you're using Microsoft's own ALZ-Bicep instead, same idea: clone it, read the `docs/` folder for the module list, and check the accompanying wiki for the parameter files you'll need to fill in. Either way, don't deploy straight from `main`. Fork it or clone it into your own repo so you can commit your sandbox-specific parameter values without ever touching upstream.

## How to deploy to your test environment

Covered the mechanics of this earlier in this post, the short version:

1. Get Owner at the tenant root (via a Global Admin enabling "Access management for Azure resources," or an existing root-scope Owner granting it to you).
2. Clone the repo and populate a private, gitignored config file with your tenant ID, management group ID, subscription ID, and region.
3. Run `Confirm-LandingZonePrerequisites.ps1` (or the equivalent check script) first: it validates your PowerShell, Az module, Bicep, and CLI versions, and confirms you actually have the root-scope permissions before you waste time on a deployment that's going to fail halfway through.
4. Deploy stage by stage with `whatif: true` first, then flip it off once you're confident: management groups, then hub connectivity, then platform management, then platform identity, then policy assignments, then role assignments.

Stage by stage the first time round is worth the extra ten minutes: it's how you actually see the dependency chain rather than just deploying a wall of resources and hoping.

## What to omit to save costs

A sandbox doesn't need every bell and whistle the reference architecture ships with. Things I'd skip or downsize for a personal test tenant:

- **Azure Firewall Premium**: the Standard SKU is enough to prove the pattern; Premium's TLS inspection and IDPS add cost you don't need for learning.
- **VPN/ExpressRoute gateways**: unless you're specifically testing hybrid connectivity, skip these. They bill hourly whether you use them or not.
- **Defender for Cloud paid plans**: the free tier (CSPM foundational) is enough to see policy and posture data; the paid per-resource plans add up fast across a full landing zone.
- **Multiple Bastion instances / Standard SKU Bastion**: Basic SKU or just Basic Bastion in the hub is plenty for a lab.
- **Sentinel**: genuinely useful, genuinely billed per GB ingested. Turn it on only if you're specifically testing the SIEM integration, and turn off ingestion again once you're done.
- **Multiple regions**: deploy to one region. Multi-region is a scale concern, not a "do I understand the pattern" concern.

Run `Get-AzurePricingforSolution.ps1` (or the Azure Pricing Calculator manually) against your parameter file before you deploy: it'll price out exactly what you're about to spin up, in your currency and region, so there are no surprises.

## How to tear it down

Management group-scoped deployments don't clean up after themselves the way a resource group does: there's no single `az group delete` that removes everything. Tear-down needs to happen in reverse dependency order:

1. Remove policy assignments and any custom definitions/exemptions first: you can't delete a management group with active assignments attached.
2. Remove RBAC role assignments.
3. Delete resource groups containing hub networking, monitoring, and identity resources (`az group delete --name <rg> --yes --no-wait` works fine for this, run in parallel).
4. Delete any subscriptions you vended for testing (or move them back out of the management group hierarchy if they're existing subscriptions you don't actually own).
5. Delete the management groups themselves, working from the leaves up to (but not including) the tenant root.
6. Double check Log Analytics workspaces and Key Vaults: both soft-delete by default, and will keep quietly costing you (or at least cluttering your tenant) unless purged explicitly.

Worth scripting this once as a `Deploy-Local.ps1 -TearDown` equivalent rather than doing it by hand every time: you'll be spinning this sandbox up and down more than once while you're learning it.

## How to automate it so it runs from GitHub (not your personal PC)

Running it locally is great for learning the sequencing. It's the wrong place for it to live long-term: nobody wants their sandbox landing zone tied to whether their laptop is switched on. Once you're comfortable with the flow, move it to GitHub Actions:

1. **Set up OIDC, not a service principal secret.** Use a script like `Set-GitHubOIDCAppRegistration.ps1` to create an Entra app registration with a federated credential trusting your specific GitHub repo/branch. No client secret to rotate or leak.
2. **Mirror the local stages as workflow jobs.** The accelerator already ships `.github/workflows/deploy.yml` as a reusable `workflow_call`: each stage (management groups, firewall, policy, RBAC) becomes its own job or workflow, called in dependency order, the same order you ran manually.
3. **Use GitHub Environments for approval gates.** Map each deployment stage to a GitHub Environment with required reviewers, so a `whatif` plan posts as a PR check and a human approves before the real deployment runs: same governance model as an ADO pipeline, without needing ADO.
4. **Keep sandbox-specific parameters out of the repo history.** Store your tenant/subscription IDs as repository or environment secrets/variables, not committed `.bicepparam` values, even in a personal sandbox: it's good practice to carry into a real client repo later.

Once this is wired up, "redeploy my sandbox landing zone" becomes a `workflow_dispatch` button, not a laptop with the right PowerShell modules installed. That's the point where it stops being a one-off learning exercise and starts being infrastructure you can actually rely on to test changes against.