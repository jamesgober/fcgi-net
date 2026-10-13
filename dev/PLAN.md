# fcgi-net - Project Plan

> The design, strategy, and decision record for `fcgi-net`. A living document: every design decision, idea, or open question about fcgi-net is written here or in [`ROADMAP.md`](./ROADMAP.md) before it is acted on.
>
> **Read order:** [`../REPS.md`](../REPS.md) &rarr; [`DIRECTIVES.md`](./DIRECTIVES.md) &rarr; **this file** &rarr; [`ROADMAP.md`](./ROADMAP.md).
>
> **Status:** scaffold, `0.1.0`. Design lock is `0.2.0`.

---

## 1. Purpose

fcgi-net speaks FastCGI, the protocol web servers use to hand requests to a long-running application process. With it, a Rust service runs behind Apache (`mod_fcgid`, `mod_proxy_fcgi`) or nginx (`fastcgi_pass`), including on shared hosting where a process may not keep its own port open.

It is sans-IO and `no_std` with `alloc`, like http-net: bytes in, requests out; responses in, records out. serve-net runs it in its protocol engine slot.

## 2. When it matters

| Hosting | Mode |
|---|---|
| cPanel with WHM/root, VPS, dedicated | reverse proxy (Apache `ProxyPass`); FastCGI optional |
| shared cPanel hosting | often FastCGI only (`mod_fcgid` starts our process) |

## 3. Protocol scope

- FastCGI 1.0 records: 8-byte header (version, type, request id, content length, padding length), content, padding.
- Record types: `BEGIN_REQUEST`, `ABORT_REQUEST`, `END_REQUEST`, `PARAMS`, `STDIN`, `STDOUT`, `STDERR`, `DATA`, `GET_VALUES`, `GET_VALUES_RESULT`, `UNKNOWN_TYPE`.
- The **responder** role. Authorizer and filter roles are out of scope until needed.
- Name-value pair decoding (1-byte or 4-byte lengths) with budgets.
- `FCGI_KEEP_CONN`; multiplexed requests on one connection (`FCGI_MPXS_CONNS`), with a cap; `FCGI_GET_VALUES` answered (`FCGI_MAX_CONNS`, `FCGI_MAX_REQS`, `FCGI_MPXS_CONNS`).
- Request abort handled cleanly (the bouncer ticket is cancelled).
- Responses written as CGI-style output on `STDOUT` (`Status:` and headers, then the body) in records, ending with `END_REQUEST`.

## 4. The `cgi` module (shared with plain CGI)

CGI meta-variables (RFC 3875) map to the normalized request once, here, and serve both FastCGI and plain CGI mode:

- `REQUEST_METHOD`, `SCRIPT_NAME`, `PATH_INFO`, `QUERY_STRING`, `CONTENT_TYPE`, `CONTENT_LENGTH`, `SERVER_NAME`, `SERVER_PORT`, `HTTPS`, `REMOTE_ADDR`, and `HTTP_*` headers back into header names.
- `REMOTE_ADDR` is trusted only because the FastCGI listener accepts only the configured front end (serve-net binds it to loopback or a socket and checks the peer).

## 5. Process start under `mod_fcgid`

`mod_fcgid` starts the application itself and passes a listening socket as descriptor 0 (`FCGI_LISTENSOCK_FILENO`). serve-net detects this and hands the inherited socket to net-io (net-io's inherited-listener support covers it; noted for net-io 0.4.0).

## 6. Budgets

Params total size, param count, name and value lengths, concurrent requests per connection, `STDIN` size (route policy), record content length (protocol maximum 65,535). Each is checked before buffering.

## 7. Threats

| Threat | Defence |
|---|---|
| FastCGI port reachable from the internet (anyone could forge `REMOTE_ADDR`) | loopback or socket bind by default; startup refuses a public bind; peer check |
| oversized or never-ending params | budgets |
| request id confusion in multiplexing | strict per-id state; unknown ids refused |
| malformed records | strict refusal, connection closed |

## 8. Architecture

```
src/
  lib.rs      crate docs, Tier-1 entry points
  record.rs   record header and framing
  conn.rs     sans-IO connection: requests by id, events
  params.rs   name-value pairs
  respond.rs  STDOUT records, END_REQUEST
  cgi.rs      CGI meta-variables to request mapping
  error.rs    FcgiError (#[non_exhaustive])
```

## 9. Testing

Record streams as data (whole, split, byte at a time); interop tests against Apache `mod_proxy_fcgi` and nginx `fastcgi_pass` in CI containers; multiplexing and abort tests; fuzz target for records and params.

## 10. Open decisions

| Id | Question | Recommendation |
|---|---|---|
| O-1 | Is this first run? Depends on whether the cPanel server is shared hosting. | James answers the hosting question |
| O-2 | Publish `0.1.0` to crates.io to claim the name? | yes |

## 11. Decision log

| Id | Date | Decision | Reason |
|---|---|---|---|
| D-001 | 2026-10-13 | Sans-IO, `no_std` with `alloc`, responder role first | same shape as http-net; the role web servers use |
| D-002 | 2026-10-13 | CGI meta-variable mapping lives here and serves plain CGI too | one mapping, two ingress modes |
| D-003 | 2026-10-13 | Public binds refused by default | a reachable FastCGI port lets anyone forge the client address |
