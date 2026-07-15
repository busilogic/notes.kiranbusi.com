---
title: "Deploying a Test Azure Landing Zone"
date: 2026-07-15
description: "Notes from standing up a full platform landing zone in a sandbox tenant — no pipeline, no EA billing account, just a local Bicep deployment to learn how the pieces fit together."
draft: true
---

I've reviewed a lot of Azure landing zones as a consultant, but reviewing someone else's architecture and actually deploying one yourself are different levels of understanding. So I set out to stand up a full platform landing zone in a sandbox tenant, using our internal Bicep accelerator, with no pipeline and no Enterprise Agreement billing account in the way. Just me, PowerShell, and a tenant where I have Owner at the root.

This is the reference version of what I did — partly so I don't forget, partly so future-me has a starting point next time.

## Why local, not pipeline

The accelerator is built to run through an Azure DevOps pipeline in a real engagement. But a pipeline is the wrong tool for learning — it hides the sequencing, the parameter flow, and the "why did that fail" moment behind YAML and service connections. The repo ships a `.local/` folder specifically for this: a PowerShell test harness (`Deploy-Local.ps1`) that runs the exact same Bicep orchestration files, in the exact same dependency order, straight from VS Code.

Running locally is sandbox-only by design. You'd never point this at a customer tenant — it's there so an individual engineer can iterate fast without needing pipeline access.

## What a platform landing zone actually is

Worth pinning down before diving into steps: a **platform landing zone** is the centralized layer — management groups, policy, hub networking, identity, security tooling — that every application landing zone sits underneath. It's not a workload environment. It's the scaffolding: the management group hierarchy, the policies inherited down from the root, the hub VNet with firewall and Bastion, the logging and role assignments that make everything else governable.

Deploying it yourself is the fastest way to understand *why* each layer exists, because you hit the dependency ordering problem immediately — you cannot create policy assignments before the management groups they attach to exist, and you cannot peer a spoke to a hub that hasn't been deployed yet.

## The setup

Prerequisites: PowerShell 7+, the Az PowerShell module, Bicep CLI, and Owner at the tenant root management group. If you're not already there, a Global Administrator needs to enable **"Access management for Azure resources"** in Microsoft Entra ID first (or have an existing root-scope Owner grant it) — you can't bootstrap root Owner for yourself out of nothing. Once that's enabled:

```bash
az login
az role assignment create \
  --assignee $(az account show --query user.name -o tsv) \
  --role Owner \
  --scope /
```

Clone the repo, and set up a private, gitignored config file — this is the one place your tenant ID, management group ID, and subscription ID live, and it never gets committed:

```bash
git clone <the-alz-bicep-repo>
cd azure-landing-zones-bicep
cp .local/config/deploy-local.private.json.example .local/config/deploy-local.private.json
```

The config drives everything: your name/email (used for tagging), the target tenant/MG/subscription, the region, and — critically — a `whatif` flag. Leave that `true` for the first pass. Every deployment becomes a dry run until you're confident enough to flip it.

## Deploying it, one stage at a time

The harness will run everything in one shot (`-Step AllSteps`), but the first time through, I deployed stage by stage so I could actually watch what each layer produced:

```powershell
Connect-AzAccount

./.local/Deploy-Local.ps1 -Step ManagementGroups
./.local/Deploy-Local.ps1 -Step "platform/platformConnectivity-hub"
./.local/Deploy-Local.ps1 -Step "platform/platformManagement"
./.local/Deploy-Local.ps1 -Step "platform/platformIdentity"
./.local/Deploy-Local.ps1 -Step "policy/alzDefaultPolicyAssignments"
./.local/Deploy-Local.ps1 -Step "roles/roleAssignments"
```

The ordering matters more than it looks. Later stages consume outputs from earlier ones (things like the hub VNet resource ID, or a management group's full resource ID) via a generated `published-vars.private.json` file — which only gets populated with real values once a stage actually deploys, so `whatif` dry-runs won't chain into downstream stages the same way. Run a real deployment for a stage before relying on its outputs downstream, and expect a config hash mismatch or missing-parameter error if you run stages out of order — annoyingly, also the best way to *feel* the dependency graph rather than just read about it in a diagram.

Role assignments running last in this sequence is deliberate for this harness, not a universal rule — if a policy assignment relies on a managed identity for remediation (`deployIfNotExists`/`modify` effects), that identity needs its RBAC in place before remediation will actually succeed.

By the end of this sequence you have: a management group hierarchy, a hub VNet with firewall rules, a Log Analytics workspace and management resources, a Microsoft Entra identity layer, default ALZ policy assignments inherited down the hierarchy, and RBAC role assignments.

Two caveats worth flagging rather than glossing over: PIM eligible role assignments need Microsoft Entra ID P2 (or a bundle like Microsoft 365 E5) — without that licence, test with standard RBAC and skip the PIM-eligibility path. And "no EA" doesn't mean "no cost": Azure Firewall, Bastion, public IPs, and Log Analytics ingestion/retention all bill independently of any workload, so budget for a modest ongoing spend even in a sandbox with nothing deployed on top.

## What you can't fully test without EA or MCA billing

The one real gap: **vending brand-new subscriptions**. The Microsoft Subscription Alias API needs an Enterprise Agreement *or* Microsoft Customer Agreement billing scope — a free/trial subscription can't vend additional subscriptions on its own. Everything else — the entire platform landing zone, plus onboarding an *existing* subscription as an application landing zone through the companion vending-machine repo — works with `subscriptionAliasEnabled: false` pointed at a subscription you already have.

That substitution is enough to exercise management-group placement, inherited policy, hub-spoke peering, RBAC, tags, and budgets on that subscription — as long as the identity doing the deployment also holds the right permissions on both the subscription and the hub (budgets specifically need Cost Management write access). It's "end-to-end onboarding of an existing subscription," not literally everything the accelerator can do.

## What I actually learned

Reading the conceptual architecture diagram (hub-spoke, or Virtual WAN if you go that route) makes the shape of a landing zone obvious. Deploying it makes the *ordering* obvious — and ordering is where real engagements go wrong. Policy assignments failing because a management group isn't fully propagated yet. A spoke peering attempt firing before the hub's outputs exist. None of that shows up on a whiteboard.

If you're doing this yourself: start with `whatif: true`, deploy stage by stage the first time, and don't skip reading the generated `published-vars` file between steps — it's the clearest picture you'll get of how a platform landing zone actually wires itself together.
