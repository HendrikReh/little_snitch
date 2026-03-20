# Little Snitch Rules

Custom Little Snitch 6 rule groups for tightening outbound network control on macOS.

This repository currently provides a deny-focused rule group for **high-risk executables** such as shells, scripting runtimes, and common download tools. The goal is to reduce the attack surface for malware, spyware, opportunistic exfiltration, and unwanted outbound activity by default. Little Snitch remote rule groups use the `.lsrules` JSON format and can be subscribed to directly from a secure HTTPS URL.  [oai_citation:1‡GitHub](https://github.com/leohidalgo/little-snitch---rule-groups?utm_source=chatgpt.com)

## Included Rule Groups

### High-Risk Executables Deny
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

## Subscribe in Little Snitch

Use the raw file URL:

`https://raw.githubusercontent.com/YOUR_USERNAME/YOUR_REPO/main/high-risk-executables-deny.lsrules`

In **Little Snitch Configuration**:

1. Open **Rule Groups**
2. Click **+**
3. Choose **Remote Rule Group…**
4. Paste the raw URL
5. Click **Add**

Little Snitch’s documentation recommends using a secure HTTPS URL with a valid certificate for remote rule groups, and GitHub raw URLs are a common way to host them.  [oai_citation:2‡GitHub](https://github.com/jkamenik/little-snitch-rules?utm_source=chatgpt.com)

## Update Model

When you change the `.lsrules` file in this repository and push a commit, subscribed Macs can fetch the updated version automatically based on the configured update interval for the remote rule group. Little Snitch supports this subscription model specifically for remote rule groups.  [oai_citation:3‡GitHub](https://github.com/leohidalgo/little-snitch---rule-groups?utm_source=chatgpt.com)

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

Start carefully. A good approach is to subscribe to the ruleset, observe blocked connections, and then add specific allow-rules for known-good destinations. Existing Little Snitch rule repositories also present rule subscriptions as building blocks rather than “install and forget” policies.  [oai_citation:4‡GitHub](https://github.com/jkamenik/little-snitch-rules?utm_source=chatgpt.com)

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

For the official file format and supported fields, refer to the Little Snitch documentation:

- Little Snitch rule groups and subscriptions
- `.lsrules` file format documentation

See Objective Development’s official documentation for the authoritative schema and behavior.  [oai_citation:5‡GitHub](https://github.com/leohidalgo/little-snitch---rule-groups?utm_source=chatgpt.com)

## Example Raw URL

Replace the placeholders below with your actual GitHub account and repository name:

`https://raw.githubusercontent.com/YOUR_USERNAME/YOUR_REPO/main/high-risk-executables-deny.lsrules`

## License

Add a license if you plan to share this repository publicly.