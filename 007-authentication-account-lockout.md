# Lab 007 — Authentication & Account Lockout

**Incident:** INC-007  
**Type:** Simulated incident  
**Status:** Account access restored; security follow-up may remain  
**Focus:** Authentication, MFA, account lockout, incident response

## Scenario

Michael could not sign in to his company computer and received repeated incorrect-credential errors. He had last signed in successfully on Friday. Coworkers were unaffected.

Michael reported receiving an unexpected MFA notification on Sunday evening, which he denied.

## Investigation

The account dashboard showed:

```text
Account Status: Locked
Failed Login Attempts: 8
Account Lockout Time: 8:57 AM
MFA Status: Enabled
```

Authentication logs showed:
- An unfamiliar external IP attempted authentication Sunday at 11:42 PM.
- An MFA notification was sent and denied by Michael at 11:43 PM.
- Additional unsuccessful attempts occurred Monday morning.
- The account locked at 8:57 AM.
- No successful sign-ins from the unfamiliar IP, successful suspicious MFA approvals, password changes, or unauthorized account modifications were found.

## Response and recovery

The activity was treated as attempted unauthorized access, not a confirmed account compromise. The incident was escalated for security review.

Following company procedure:
1. Michael's identity was verified through an approved process.
2. His password was reset using the official company process.
3. Suspicious sessions/tokens were revoked where applicable.
4. MFA enrollment and registered methods were checked.
5. The administrator unlocked the account after the security checks.

Michael successfully signed in and confirmed access to his computer and company email.

## Lessons learned

- Authentication verifies identity; authorization determines permitted access.
- A lockout is a protective control, not proof that an account was compromised.
- An unexpected MFA prompt should be treated as a security signal and denied if unrecognized.
- Preserve evidence and escalate suspicious activity.
- Restoring account access and completing the security investigation are separate tasks.
- Do not ask users to disclose passwords or MFA codes.
