# Lab 003 — Account Access & Permissions

**Incident:** INC-003  
**Type:** Simulated incident  
**Status:** Resolved in simulation  
**Focus:** Authentication, authorization, groups, ACLs, least privilege

## Scenario

Daniel moved from Customer Support to Operations. He could sign in and access other company resources, but received an access-denied message when opening a shared folder.

## Initial assessment

- Classified as a limited, single-user access issue (P3 in the exercise).
- Suspected an authorization/permissions issue because sign-in succeeded.
- Clarified the resource, required access, recent role change, and business approval.

## Investigation

1. Distinguished **authentication** (proving identity) from **authorization** (determining permitted actions).
2. Compared relevant group memberships and folder access controls with the user's new role.
3. Found Daniel was missing the `Operations` and `Operations-Reports` groups.
4. Confirmed with his manager that read/write access was required.
5. Followed the organization's approval and administrator process rather than granting access independently.

## Resolution

The authorized administrator added Daniel to the approved groups. Daniel then tested access, including opening and saving a file.

## Verification

Read and write access were verified against the manager-approved requirement. The ticket was closed only after the required actions succeeded.

## Lessons learned

- Successful login does not mean a user is authorized for every resource.
- Role-based access control (RBAC) commonly uses groups to assign access based on job responsibilities.
- Follow least privilege: grant only the access approved and required.
- If reading works but saving fails, investigate effective permissions and file/application restrictions rather than assuming the issue is resolved.
- Ticket notes must reflect the final outcome, not an earlier intermediate failure.
