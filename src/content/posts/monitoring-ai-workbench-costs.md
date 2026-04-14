---
title: "How I Monitor My AI Workbench Costs"
date: 2026-04-14
description: "A scheduled GitHub Action queries OCI, Anthropic, and OpenRouter each month and commits a cost report back to a private vault. Here's the pattern."
draft: true
---

Running a personal AI workbench means paying for things that don't send you invoices in obvious ways. A cloud VM here, API credits there, a subscription on top. By the time you notice, you've had a surprise bill.

I solved this with a Python script and a scheduled GitHub Action. It runs on the first of each month, queries every service I use, and commits a markdown cost report to my private vault. No dashboard to log into. No email to ignore. Just a file in git.

## What gets queried

**Cloud compute (OCI):** I run a development VM on Oracle Cloud's Always Free tier. Free tier has limits, and exceeding them costs real money. The OCI Python SDK can query actual spend via the Usage API — grouped by service and SKU so you can see exactly which resource caused an overage.

**LLM API usage (Anthropic, OpenRouter):** Pay-per-use APIs that accumulate quietly. OpenRouter exposes lifetime usage and credit balance via a single authenticated endpoint. Anthropic's billing isn't available through standard API keys — that one's a manual console check, noted in the report.

**Fixed subscriptions:** Claude Pro is a flat monthly fee. The script hardcodes it. Manual update if the price changes — but at least it shows up in the same report alongside the variable costs.

## The mechanism

```python
def month_range(months_ago=0):
    today = datetime.date.today().replace(day=1)
    first = today - datetime.timedelta(days=30 * months_ago)
    first = first.replace(day=1)
    if first.month == 12:
        last = first.replace(year=first.year + 1, month=1)
    else:
        last = first.replace(month=first.month + 1)
    return first, last
```

Keys are read from a private vault repo via regex — no hardcoded secrets in the script. The script finds the longest match when there are multiple candidates (avoiding truncated display keys).

The output is a markdown table prepended to a running report file:

```markdown
| Service                  | Cost    | Currency | Notes                          |
|--------------------------|---------|----------|-------------------------------|
| Claude Pro subscription  | $35.00  | AUD      | Fixed monthly                  |
| OpenRouter               | $0.14   | USD      | Balance remaining: unlimited   |
| Oracle Cloud (OCI)       | $0.00   | AUD      | Compute only — workstation VM  |
| Anthropic API            | manual  | USD      | Check console.anthropic.com    |
```

## The GitHub Action

The script runs on a schedule — first of each month at 6am AEST — and can be triggered manually any time via `workflow_dispatch`. It checks out the private vault, sets up OCI credentials from a repository secret, runs the script, and commits the result back.

```yaml
on:
  schedule:
    - cron: '0 20 1 * *'  # 1st of month, 06:00 AEST
  workflow_dispatch:

permissions:
  contents: write
```

The OCI PEM key is the one secret that can't live in a file — it goes in GitHub Actions secrets. Everything else is already in the vault.

## What this catches

The point isn't precision accounting. It's pattern detection. A month where OCI shows anything above zero means something left the free tier. A jump in OpenRouter spend means a project is using more than expected. The report takes 30 seconds to read and that's enough.

It also catches the thing I actually got burned by: a second VM I'd forgotten about, running for weeks, billing against the shared Always Free compute quota. The cost query named the resource. I terminated it the same day.

## The reclamation problem

Oracle reclaims Always Free VMs with less than 20% average CPU utilisation over 7 days. The monitoring agent reports hourly means, so a keep-alive cron that fires every 3 hours only affects ~33% of hourly slots — not enough to lift the 7-day average above the threshold.

The fix: run the keep-alive every hour.

```
0 * * * *  timeout 300 bash -c 'while true; do echo "scale=5000; 4*a(1)" | bc -l; done' &>/dev/null
```

Five minutes of CPU work per hour. Enough to show activity without burning meaningful resources. You can verify it's working by querying the monitoring API a day later and checking that the hourly spikes are consistent.

## The pattern

Private vault + scheduled CI + commit-back is a useful pattern for any lightweight operational monitoring that doesn't warrant a full observability stack. Cost reports, health checks, certificate expiry — anything you want to know about monthly but don't want to build a dashboard for.

The vault stays private. The Action runs on GitHub's infrastructure. The report accumulates in git history. No external services, no subscriptions, no dashboards to maintain.
