# IT Support Development Labs

A hands-on learning portfolio documenting simulated IT support incidents, troubleshooting decisions, diagnostic commands, findings, and lessons learned.

> **Portfolio note:** These are educational simulations completed as part of an independent skills-development program. They are not records of paid employment, real client incidents, or production-system changes.

## Purpose

This repository demonstrates a structured approach to entry-level IT support:

- Gather symptoms and establish business impact.
- Form and test hypotheses rather than jumping to conclusions.
- Use diagnostic tools and interpret their output.
- Compare affected and known-good systems where appropriate.
- Respect authorization, security, and least-privilege boundaries.
- Escalate with clear evidence when a task exceeds support permissions.
- Verify the user's outcome and document accurately.

## Lab Index

| Lab | Topic | Core skills |
|---|---|---|
| [001 — Incident Triage](labs/001-incident-triage.md) | Shared-folder access failure | Scope, prioritization, DNS diagnosis, verification |
| [002 — Troubleshooting & Escalation](labs/002-troubleshooting-escalation.md) | Intermittent connectivity | Ping tests, fault-domain reasoning, workaround, escalation |
| [003 — Account Access & Permissions](labs/003-account-access-permissions.md) | Shared-folder access denied after role change | Authentication vs. authorization, groups, ACLs, least privilege |
| [004 — Network Troubleshooting](labs/004-network-troubleshooting.md) | Internal portal works; public sites fail | IP connectivity, DNS testing, security-policy investigation |
| [005 — DHCP & IP Configuration](labs/005-dhcp-ip-configuration.md) | Wi-Fi connected, no usable network configuration | APIPA, DHCP lease renewal, gateway, adapter troubleshooting |
| [006 — DNS Troubleshooting](labs/006-dns-troubleshooting.md) | External websites fail while internal portal works | `ping`, `nslookup`, DNS filtering, endpoint security logs |
| [007 — Authentication & Account Lockout](labs/007-authentication-account-lockout.md) | Suspicious sign-in attempts and locked account | MFA, authentication logs, incident response, safe recovery |
| [008 — Windows Performance & Storage](labs/008-windows-performance-storage.md) | Slow workstation and intermittent freezes | Task Manager, Resource Monitor, disk latency, hardware escalation |

## Standard Troubleshooting Workflow

1. **Triage:** Identify who is affected, what is unavailable, urgency, and business impact.
2. **Clarify:** Ask targeted questions and establish what changed.
3. **Baseline:** Compare with a working device or known-good configuration when useful.
4. **Test:** Choose diagnostics that distinguish between competing explanations.
5. **Interpret:** Separate symptoms, evidence, likely causes, and confirmed causes.
6. **Act safely:** Follow policy, obtain approval, and stay within assigned permissions.
7. **Verify:** Confirm the original user-facing problem is resolved.
8. **Document:** Record actions, results, outstanding risks, and escalation ownership.

## Documentation Standards

Each lab contains:
- Scenario and reported symptoms
- Initial triage and hypothesis
- Investigation and evidence
- Commands or tools used
- Resolution or escalation
- Verification
- Lessons learned

Commands are included as learning references. Run commands only on systems you own or are explicitly authorized to administer. Do not publish passwords, MFA codes, private IP details from real environments, customer data, or confidential company information.

## Skills in Progress

This is a developing portfolio. Future labs can add:
- Windows services and application dependencies
- Event Viewer investigation
- Printer troubleshooting
- User onboarding and offboarding
- Remote support and professional ticket communication
- Basic PowerShell diagnostics

## About

Independent IT support skills-development project. Labs are simulated and designed to practice practical troubleshooting, documentation, and escalation.
