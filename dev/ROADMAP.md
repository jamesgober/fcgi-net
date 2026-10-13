# fcgi-net - Roadmap

> Path from scaffold to a stable 1.0. Hard parts are front-loaded; each phase has hard exit criteria.
> Design and decisions: [`PLAN.md`](./PLAN.md).
>
> **Anti-deferral rule:** no listed hard task moves to a later phase unless this file records the move and the reason.

## v0.1.0 - Scaffold (DONE, 2026-10-13)
- [x] Manifest, README, CHANGELOG, REPS, dual license, CI, deny, clippy, rustfmt, DIRECTIVES, ROADMAP, PLAN.

## v0.2.0 - Design lock
Exit: connection, event, response, and cgi-mapping APIs written in `docs/API.md` and approved.

## v0.3.0 - Responder
Exit: requests from Apache mod_proxy_fcgi and nginx fastcgi_pass are served through serve-net, multiplexed and aborted correctly.
- [ ] Records, params with budgets, responder role, multiplexing, GET_VALUES, abort.
- [ ] The cgi module (also used by plain CGI mode).
- [ ] Interop tests in CI containers; fuzz targets.

## v1.0.0 - Stable
- [ ] Frozen after a site has run behind a FastCGI front end.
