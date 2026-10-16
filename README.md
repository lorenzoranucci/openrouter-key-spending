# openrouter-key-spending

An agent skill that reports OpenRouter API key spending (standard vs BYOK), the effective key limit, and workspace budget utilization, using `GET https://openrouter.ai/api/v1/auth/key`.

## Install

```sh
npx skills add lorenzoranucci/openrouter-key-spending -g
```

Or manually for OpenCode:

```sh
git clone https://github.com/lorenzoranucci/openrouter-key-spending.git ~/.config/opencode/skills/openrouter-key-spending
```

## Requirements

`OPENROUTER_API_KEY` must be set in the environment where the agent runs commands. The skill never stores or prints the key.

## Usage

Mention `@openrouter-key-spending` in chat, or ask: "Show my OpenRouter spending and remaining budget".

## Update

```sh
npx skills update
```
