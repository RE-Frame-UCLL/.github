# Security policy

## Reporting a vulnerability

Please do not report security problems in a public issue, discussion or pull request.

- In a public repo, use **Report a vulnerability** on the repo's **Security** tab
  (GitHub private vulnerability reporting).
- In an internal repo, contact a member of the
  [core-maintainers](https://github.com/orgs/RE-Frame-UCLL/teams/core-maintainers)
  team directly.

Tell us what is affected (package and version), how to reproduce it and what an
attacker could do with it. We confirm that we received your report and keep you
informed until it is fixed.

## Supported versions

Security fixes go into the latest release of the current major version of each
package. Upgrade with `npm update` to receive them.

## How we fix

- A fix is released as a new patch version. Published versions are never overwritten
  or deleted; an affected version is marked with `npm deprecate` and a reason.
- We publish an advisory on the repo once a fix is available.

This policy is completed before core becomes public.
