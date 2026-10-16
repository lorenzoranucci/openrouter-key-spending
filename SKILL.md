---
name: openrouter-key-spending
description: Reports OpenRouter API key spending (standard vs BYOK), the effective key limit, and workspace budget utilization by calling GET /api/v1/auth/key. Use when the user asks about OpenRouter usage, spending, remaining limit, key budget, or workspace budget.
---

# OpenRouter Key Spending

## Step 1 — Call the API

Run this exact command (do NOT print or log the key value):

```sh
curl -sS --fail-with-body --max-time 30 \
  -H "Authorization: Bearer $OPENROUTER_API_KEY" \
  https://openrouter.ai/api/v1/auth/key
```

If `$OPENROUTER_API_KEY` is not set in the environment, tell the user and stop.

## Step 2 — Report

Output ONLY the three sections below, in this exact order, in English. Include the overall workspace budget, but exclude member budgets and workspace default key budgets.

### 1. Effective Key Limit

A bulleted summary based on `data.effective_budget` (fallback to `data.limit`, `data.limit_remaining`, `data.limit_reset`):

- **Limit:** total limit amount and reset interval (e.g. `$100.00 / weekly`)
- **Spent:** `spend_usd` and percentage of the limit used
- **Remaining:** `remaining_usd`
- **BYOK included in limit:** Yes/No (`include_byok`, or `include_byok_in_limit`)
- **Next reset:** `resets_at` converted to UTC and the user's local time (if the local timezone is known)

### 2. Workspace Limit

Select the entry in `data.budgets` whose `scope` is exactly `workspace`. Do NOT use `workspace_default_member` or `workspace_default_key`.

- **Limit:** `limit_usd` and `reset_interval`
- **Period:** the current budget period identified by `reset_interval` (e.g. Current month for monthly)
- **Spent:** `spend_usd` and utilization calculated as `100 * spend_usd / limit_usd`
- **Remaining:** `remaining_usd`
- **BYOK included in limit:** Yes/No from `include_byok`
- **Next reset:** `resets_at` converted to UTC and the user's local time (if known)

Use the workspace entry's own spending and reset interval. For example, a monthly workspace budget must show current-month workspace utilization even if the key limit resets weekly. Do NOT divide key spending by the workspace limit or compare spending from a different period. The response does not provide workspace spending for each row of the API Key Spending table; do not invent it.

If no exact `workspace` entry exists, report "Workspace budget unavailable in the API response." If the limit is null, report "No configured limit" and utilization as "N/A". If the limit is zero, report utilization as "N/A" rather than dividing by zero. If required values are missing, report them as "Unavailable"; do not treat missing values as zero.

### 3. API Key Spending

A markdown table with rows for Today, Current week, Current month, and All-time (cumulative):

| Period | Standard (OpenRouter) | BYOK | Total |
|---|---:|---:|---:|
| Today | `usage_daily` | `byok_usage_daily` | sum |
| Current week | `usage_weekly` | `byok_usage_weekly` | sum |
| Current month | `usage_monthly` | `byok_usage_monthly` | sum |
| All-time (cumulative) | `usage` | `byok_usage` | sum |

## Formatting rules

- All currency amounts in USD, formatted as `$X.XXXX`.
- Percentages with two decimals (e.g. `9.47%`).
- Read all fields from under the `data` object of the JSON response.
- Bold the Total column values.
