# Little Snitch Rules

Custom Little Snitch 6 rule groups for tightening outbound network control on macOS.

This repository provides a deny-focused rule group for **high-risk executables** such as shells, scripting runtimes, and common download tools, paired with a separate allow-exceptions group for known-good developer workflows. The goal is to reduce the attack surface for malware, spyware, opportunistic exfiltration, and unwanted outbound activity by default. Little Snitch remote rule groups use the `.lsrules` JSON format and can be subscribed to directly from a secure HTTPS URL.

## Included Rule Groups

### `high-risk-executables-deny.lsrules`

Blocks outbound internet access for selected executables unless you create a more specific allow-rule.

Covered paths:

- `/bin/sh`
- `/bin/bash`
- `/bin/zsh`
- `/usr/bin/osascript`
- `/usr/bin/python3`
- `/usr/bin/perl`
- `/usr/bin/ruby`
- `/usr/bin/curl`
- `/usr/bin/ssh`

Optional Homebrew rules are included but disabled by default:

- `/opt/homebrew/bin/curl`
- `/opt/homebrew/bin/wget`
- `/opt/homebrew/bin/python3`

### `dev-safe-exceptions.lsrules`

Narrow allow-rules for known-good developer workflows. Designed to be used alongside the deny group above — subscribe to both and Little Snitch will apply the most specific matching rule.

Covered exceptions:

| Process | Allowed destinations |
| --- | --- |
| `/usr/bin/ssh` | `github.com` |
| `/usr/bin/curl`, `/opt/homebrew/bin/curl` | GitHub domains (API, raw, objects, codeload) |
| `/usr/bin/curl`, `/opt/homebrew/bin/curl` | `formulae.brew.sh` |
| `/usr/bin/python3`, `/opt/homebrew/bin/python3` | `pypi.org`, `files.pythonhosted.org` |
| `/bin/sh`, `/bin/bash`, `/bin/zsh` | `registry.npmjs.org` |

## Subscribe in Little Snitch

In **Little Snitch Configuration**, subscribe to both rule groups:

| Rule group | Raw URL |
| --- | --- |
| High-risk deny | `https://raw.githubusercontent.com/HendrikReh/little_snitch/main/high-risk-executables-deny.lsrules` |
| Dev-safe exceptions | `https://raw.githubusercontent.com/HendrikReh/little_snitch/main/dev-safe-exceptions.lsrules` |

Steps:

1. Open **Rule Groups**
2. Click **+**
3. Choose **Remote Rule Group…**
4. Paste the raw URL
5. Click **Add**
6. Repeat for the second group

## Update Model

When you change a `.lsrules` file in this repository and push a commit, subscribed Macs fetch the updated version automatically based on the configured update interval for the remote rule group.

## Rule Design Philosophy

This ruleset is intentionally strict:

- deny broad outbound access for high-risk executables
- add **narrow allow-rules** only for destinations you explicitly trust
- prefer destination-specific exceptions over weakening the base deny policy

This is meant for users who want a more defensive outbound posture on macOS, especially on developer machines where shells, interpreters, and command-line download tools are frequently present.

## Important Notes

This ruleset can break normal workflows, including:

- `ssh` connections
- shell scripts that download dependencies
- Python scripts calling APIs
- `curl`-based install or update flows
- AppleScript automations that reach internet services

Start carefully. A good approach is to subscribe to both rule groups, observe blocked connections, and then add specific allow-rules for other known-good destinations.

## File Format

Little Snitch rule groups are JSON files with top-level fields such as:

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

For the official file format and supported fields, refer to [Objective Development’s Little Snitch documentation](https://obdev.at/products/littlesnitch/index.html).

## License

MIT
