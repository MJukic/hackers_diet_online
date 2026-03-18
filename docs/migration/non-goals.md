# Desktop v1 Non-Goals

This document captures explicit exclusions for the first desktop release to prevent scope drift while achieving functional parity for core tracking workflows.

## Product scope non-goals
- No CGI/web-server deployment model in v1.
- No browser-session cookie mechanics; authentication is local to the desktop app.
- No remote multi-user hosting/service operation in v1.
- No dependency on always-on internet connectivity for core logging and analytics.

## Platform and integration non-goals
- No cloud sync provider integration (e.g., Dropbox/Drive/iCloud) in v1.
- No mobile companion apps in v1.
- No plugin/extension API in v1.

## Feature non-goals (deferred)
- Public user directory browsing parity with web-hosted service.
- Built-in feedback message relay to a central operator endpoint.
- Advanced coaching/recommendation systems beyond existing trend mathematics.
- Full theme marketplace/skin system (basic light/dark accessibility options are sufficient).

## Technical non-goals
- No attempt to preserve legacy flat-file storage as primary runtime database.
- No binary compatibility guarantee with internal Perl data structures.
- No premature optimization for distributed concurrency scenarios.

## Delivery constraints
- Prioritize correctness and behavioral parity for calculations and logs over visual polish.
- Defer non-essential enhancements until parity acceptance criteria are met.
