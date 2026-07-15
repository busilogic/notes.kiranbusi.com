---
title: "Deploying a Test Azure Landing Zone"
date: 2026-07-15
description: "Notes from standing up a full platform landing zone in a sandbox tenant: no pipeline, no EA billing account, just a local Bicep deployment to learn how the pieces fit together."
draft: true
---

## Introduction
This is a walk through of deployment of an Azure Platform Landing Zone in my personal test environment. 

## Target Use Case
- Deploy a greenfield azure platform landing zone to a client environment.
- For testing, a DevOps engineer will need to deploy a greenfield azure landing zone to play and explore.


## Azure Platform Landing Zone Overview

When someone thinks about an Azure Landing Zone, they think about what they need to run their workloads in Azure. 

Subscriptions, security logs and vnets. However, it is important to abstract the concepts into 2 layers.

1. Platform Landing Zone
2. Application Landing Zone

A **platform landing zone** is the centralized layer. Management groups, policy, hub networking, identity, security tooling that every application landing zone needs. 

It's not a workload environment. It's the scaffolding.
- management group hierarchy 
- policies inherited down from the root 
- hub VNet with firewall and Bastion 
- logging and role assignments that make everything governable.

## Examples of popular Azure Platform Landing Zone Accelerators

Usually you don't write the code from scratch. No one does. There are a handful of accelerators out there that already package the Microsoft reference architecture into deployable code.

- **[Azure/ALZ-Bicep](https://github.com/Azure/ALZ-Bicep)**: Microsoft's own reference implementation. Modular, well-documented, the closest thing to a "vanilla" ALZ in Bicep.
- **[Azure/terraform-azurerm-alz](https://github.com/Azure/terraform-azurerm-alz)**: same conceptual architecture, Terraform flavour, if that's your IaC of choice.

Fork or wrap one of these repos with your own naming conventions, policies, and pipeline. 

## Greenfield Azure Platform Landing Zone

A "gold plated" greenfield platform landing zone is really a handful of layers stacked in dependency order:

- **An intermediate root management group**: created directly under Tenant Root Group, not attached to it. This is the actual top of your ALZ hierarchy, everything below is genuine ALZ structure. Tenant Root Group itself stays close to untouched.
- **Management group hierarchy**: the tree everything else attaches policy and RBAC to (`Platform`, `Landing Zones`, `Sandbox`, `Decommissioned`, etc., under that intermediate root, not under your true tenant root).
- **Custom role definitions**: if the built-in Azure RBAC roles don't fit your operating model.
- **Hub networking**: a hub VNet, Azure Firewall, Bastion, and (optionally) a VPN or ExpressRoute gateway for on-prem connectivity.
- **Platform management**: Log Analytics workspace, Azure Monitor, diagnostic settings wired up centrally so every subscription forwards logs to one place.
- **Platform identity**: Entra ID groups, PIM-eligible roles (licensing permitting), conditional access baselines.
- **Policy**: the ALZ default policy initiative assignments, plus any custom definitions and exemptions your organisation needs.
- **Role assignments**: RBAC tying identities to management groups/subscriptions.
- **DNS**: private DNS zones for private endpoints, conditional forwarders if you're hybrid.

Everything downstream (application landing zones, workloads) sits on top of this and inherits policy and connectivity from it. 

### Why an intermediate root group, not Tenant Root Group

Tenant Root Group is permanent, you can't rename or delete it, and every subscription in the tenant sits under it implicitly, including things that have nothing to do with your landing zone. Assigning policy directly at Tenant Root Group means it applies tenant-wide, forever, to everything, including subscriptions you haven't created yet. If an assignment turns out wrong, you can't un-scope it the way you can by just deleting a child management group.

Microsoft's own ALZ conceptual architecture creates an intermediate root management group (named after the org, e.g. `contoso`, or for a sandbox, something like `sandbox`) directly under Tenant Root Group, and that's where `Platform`, `Landing Zones`, `Sandbox`, `Decommissioned` etc. actually attach. For a test tenant this also means your entire hierarchy is one management group you can delete to tear everything down, without ever having touched the real tenant root.

![Management group hierarchy showing an intermediate root group between Tenant Root Group and the ALZ structure](/images/alz-mg-hierarchy.svg)


## What to omit to save costs

A sandbox doesn't need every bell and whistle the reference architecture ships with. Things I'm skipping for a personal test deployment to my test tenant:

- **Azure Firewall Premium**: the Standard SKU is enough to prove the pattern.
- **VPN/ExpressRoute gateways**: unless you're specifically testing hybrid (billing is hourly whether you use them or not).
- **Defender for Cloud paid plans**: the free tier (CSPM foundational) is enough to see policy and posture data.
- **Standard SKU Bastion**: basic is plenty for a lab.
- **Sentinel**: billed per GB ingested. Turn it on if you're specifically testing the SIEM integration
- **Multiple regions**: deploy to one region. 

Run `Get-AzurePricingforSolution.ps1` (or the Azure Pricing Calculator manually) against your parameter file before you deploy: it'll price out exactly what you're about to spin up, in your currency and region, so there are no surprises.


## How to deploy to your test environment

1. Get Owner at the tenant root (via a Global Admin enabling "Access management for Azure resources," or an existing root-scope Owner granting it to you).

2. Create an intermediate root management group directly under Tenant Root Group (this is your actual ALZ root, keep Tenant Root Group itself untouched).

3. Clone the repo and populate a private, gitignored config file with your tenant ID, the intermediate root management group ID (not Tenant Root Group), subscription ID, and region.

4. Run `Confirm-LandingZonePrerequisites.ps1` (or the equivalent check script) first: it validates your PowerShell, Az module, Bicep, and CLI versions, and confirms you actually have the root-scope permissions before you waste time on a deployment that's going to fail halfway through.

5. Deploy stage by stage with `whatif: true` first, then flip it off once you're confident: management groups, then hub connectivity, then platform management, then platform identity, then policy assignments, then role assignments.


## How to tear it down

Management group-scoped deployments don't clean up after themselves the way a resource group does: there's no single `az group delete` that removes everything. Tear-down needs to happen in reverse dependency order:

1. Remove policy assignments first. You can't delete a management group with active assignments attached.
2. Remove RBAC role assignments.
3. Delete resource groups containing hub networking, monitoring, and identity resources

   `az group delete --name <rg> --yes --no-wait` works fine for this
4. Delete any subscriptions you vended for testing
5. Delete the management groups themselves, working from the leaves up. DONT DELETE ROOT TENANT. 
6. Double check Log Analytics workspaces and Key Vaults: both soft-delete by default, and will keep quietly costing you (or at least cluttering your tenant) unless purged explicitly.


## How to automate it so it runs from GitHub (not your personal PC)

Once it's running locally move it to GitHub Actions by:

1. **Set up OIDC, not a service principal secret.** Use a script like `Set-GitHubOIDCAppRegistration.ps1` to create an Entra app registration with a federated credential trusting your specific GitHub repo/branch. No client secret to rotate or leak.
2. **Mirror the local stages as workflow jobs.** The accelerator should ship `.github/workflows/deploy.yml` as a reusable `workflow_call`: each stage (management groups, firewall, policy, RBAC) becomes its own job or workflow, called in dependency order, the same order you ran manually.
3. **Use GitHub Environments for approval gates.** Map each deployment stage to a GitHub Environment with required reviewers, so a `whatif` plan posts as a PR check and a human approves before the real deployment runs: same governance model as an ADO pipeline, without needing ADO.
4. **Keep sandbox-specific parameters out of the repo history.** Store your tenant/subscription IDs as repository or environment secrets/variables, not committed `.bicepparam` values, even in a personal sandbox: it's good practice to carry into a real client repo later.
