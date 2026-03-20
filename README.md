# Little Snitch Rules

Opinionated Little Snitch 6 remote rule groups for tightening outbound network control on macOS.

The repository is built around a simple model:

- start with a deny-first rule group for high-risk executables
- layer on narrow allow-rule groups for workflows you explicitly trust
- keep exceptions separated so you can subscribe only to what you need

These rule groups are intended for people who want a stricter outbound posture on developer machines, where shells, interpreters, and download tools are often the easiest path to unwanted network activity.

## Rule Groups

| File | Purpose | Typical use |
| --- | --- | --- |
| `high-risk-executables-deny.lsrules` | Baseline deny rules for shells, interpreters, and common CLI tools | Subscribe first |
| `dev-safe-exceptions.lsrules` | Narrow developer exceptions for GitHub, PyPI, npm, and Homebrew metadata | Add when you need common package and source control workflows |
| `openai-ai-tools-exceptions.lsrules` | Narrow exceptions for OpenAI and ChatGPT connectivity | Add only if local tools or scripts need OpenAI access |

## Quick Start

1. Subscribe to `high-risk-executables-deny.lsrules`.
2. Add `dev-safe-exceptions.lsrules` if you want common developer traffic to work without creating your own rules first.
3. Add `openai-ai-tools-exceptions.lsrules` only if you want local scripts, shells, or automation to reach OpenAI or ChatGPT services.
4. Watch what gets blocked and add destination-specific allow-rules instead of weakening the baseline deny policy.

Little Snitch applies the most specific matching rule, so narrow destination-specific allow-rules can coexist with broad deny rules for the same process.

## Subscribe in Little Snitch

In **Little Snitch Configuration**:

1. Open **Rule Groups**.
2. Click **+**.
3. Choose **Remote Rule Group...**.
4. Paste one of the raw URLs below.
5. Click **Add**.
6. Repeat for the other rule groups you want.

| Rule group | Raw URL |
| --- | --- |
| High-risk deny | `https://raw.githubusercontent.com/HendrikReh/little_snitch/main/high-risk-executables-deny.lsrules` |
| Dev-safe exceptions | `https://raw.githubusercontent.com/HendrikReh/little_snitch/main/dev-safe-exceptions.lsrules` |
| OpenAI AI tools exceptions | `https://raw.githubusercontent.com/HendrikReh/little_snitch/main/openai-ai-tools-exceptions.lsrules` |

## What Gets Blocked

### `high-risk-executables-deny.lsrules`

This is the baseline ruleset. It denies outbound internet access for these processes:

- `/bin/sh`
- `/bin/bash`
- `/bin/zsh`
- `/usr/bin/osascript`
- `/usr/bin/python3`
- `/usr/bin/perl`
- `/usr/bin/ruby`
- `/usr/bin/curl`
- `/usr/bin/ssh`

It also includes these Apple Silicon Homebrew paths as disabled rules:

- `/opt/homebrew/bin/curl`
- `/opt/homebrew/bin/wget`
- `/opt/homebrew/bin/python3`

## Included Exceptions

### `dev-safe-exceptions.lsrules`

This group keeps the baseline deny rules in place and adds narrow exceptions for common development workflows.

| Process | Allowed destinations |
| --- | --- |
| `/usr/bin/ssh` | `github.com` |
| `/usr/bin/curl`, `/opt/homebrew/bin/curl` | `github.com`, `api.github.com`, `raw.githubusercontent.com`, `objects.githubusercontent.com`, `codeload.github.com` |
| `/usr/bin/curl`, `/opt/homebrew/bin/curl` | `formulae.brew.sh` |
| `/usr/bin/python3`, `/opt/homebrew/bin/python3` | `pypi.org`, `files.pythonhosted.org` |
| `/bin/sh`, `/bin/bash`, `/bin/zsh` | `registry.npmjs.org` |

### `openai-ai-tools-exceptions.lsrules`

This group is meant for workflows that use OpenAI or ChatGPT from local tools, scripts, or automation while keeping the baseline deny rules active.

| Process | Allowed destinations |
| --- | --- |
| `/usr/bin/python3`, `/opt/homebrew/bin/python3` | `openai.com`, `chatgpt.com`, `auth.openai.com`, `oaistatic.com`, `oaiusercontent.com` |
| `/usr/bin/curl`, `/opt/homebrew/bin/curl` | `openai.com`, `chatgpt.com`, `auth.openai.com`, `oaistatic.com`, `oaiusercontent.com` |
| `/bin/sh`, `/bin/bash`, `/bin/zsh` | `openai.com`, `chatgpt.com` |
| `/usr/bin/osascript` | `openai.com`, `chatgpt.com` |

## Recommended Combinations

| Goal | Subscribe to |
| --- | --- |
| Strict baseline only | `high-risk-executables-deny.lsrules` |
| Safer developer machine | `high-risk-executables-deny.lsrules` + `dev-safe-exceptions.lsrules` |
| Developer machine with OpenAI tooling | `high-risk-executables-deny.lsrules` + `dev-safe-exceptions.lsrules` + `openai-ai-tools-exceptions.lsrules` |

## Customization Notes

- Keep the deny rules broad and the allow-rules narrow.
- Prefer host or domain specific exceptions over `remote: any`.
- If you use Intel Homebrew, adjust paths under `/usr/local/bin/...`; the bundled Homebrew rules target Apple Silicon paths under `/opt/homebrew/bin/...`.
- The disabled Homebrew deny rules are present so you can enable or duplicate them instead of starting from scratch.
- Domain requirements can change over time, especially for cloud and AI tooling, so review exceptions periodically.

## Operational Notes

Changing a `.lsrules` file in this repository and pushing a commit updates the remote file that subscribed Macs fetch on their next refresh cycle, based on the update interval configured in Little Snitch.

This ruleset can break normal workflows, including:

- `ssh` connections
- shell scripts that download dependencies
- Python scripts calling external APIs
- `curl`-based install or update flows
- AppleScript automations that reach internet services

Start with the baseline deny group, add the smallest exception groups you need, then observe denied connections before creating any new allows.

## File Format

Little Snitch remote rule groups are JSON files with top-level fields such as:

- `name`
- `description`
- `rules`

Rules can define attributes such as:

- `process`
- `action`
- `remote`
- `remote-domains`
- `remote-hosts`
- `remote-addresses`
- `disabled`

For official product and rule-group documentation, see [Objective Development's Little Snitch site](https://obdev.at/products/littlesnitch/index.html).

## License

MIT
