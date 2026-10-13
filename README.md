<h1 align="center">
    <img width="99" alt="Rust logo" src="https://raw.githubusercontent.com/jamesgober/rust-collection/72baabd71f00e14aa9184efcb16fa3deddda3a0a/assets/rust-logo.svg">
    <br>
    <b>fcgi-net</b>
    <br>
    <sub><sup>FASTCGI</sup></sub>
</h1>

<div align="center">
    <a href="https://crates.io/crates/fcgi-net"><img alt="Crates.io" src="https://img.shields.io/crates/v/fcgi-net"></a>
    <a href="https://crates.io/crates/fcgi-net"><img alt="Downloads" src="https://img.shields.io/crates/d/fcgi-net?color=%230099ff"></a>
    <a href="https://docs.rs/fcgi-net"><img alt="docs.rs" src="https://img.shields.io/docsrs/fcgi-net"></a>
    <a href="https://github.com/jamesgober/fcgi-net/actions"><img alt="CI" src="https://github.com/jamesgober/fcgi-net/actions/workflows/ci.yml/badge.svg"></a>
    <a href="https://github.com/rust-lang/rfcs/blob/master/text/2495-min-rust-version.md"><img alt="MSRV" src="https://img.shields.io/badge/MSRV-1.85%2B-blue"></a>
</div>

<br>

<div align="left">
    <p>
        <strong>fcgi-net</strong> speaks FastCGI, the protocol Apache (<code>mod_fcgid</code>, <code>mod_proxy_fcgi</code>) and nginx (<code>fastcgi_pass</code>) use to hand requests to an application process. On shared hosting it is often the only way to run a long-lived service.
    </p>
    <p>
        It does no I/O: feed it the bytes from the web server's connection and it yields requests (parameters, body); hand it a response and it writes the records back. serve-net runs it so that a request arriving through FastCGI looks exactly like one arriving directly.
    </p>
    <br>
    <hr>
    <p>
        <strong>MSRV is 1.85+</strong> (Rust 2024 edition). <code>#![forbid(unsafe_code)]</code>.
    </p>
    <blockquote>
        <strong>Status: scaffold (<code>0.1.0</code>).</strong> No functional code yet. The design is written down in <a href="./dev/PLAN.md"><code>dev/PLAN.md</code></a> and the schedule in <a href="./dev/ROADMAP.md"><code>dev/ROADMAP.md</code></a>; the public API is locked in <code>0.2.0</code>.
    </blockquote>
</div>

<hr>
<br>

## Scope

FastCGI 1.0 records, the responder role, parameter (name-value pair) decoding with budgets, stdin and stdout streams, multiplexed requests on one connection, management records (FCGI_GET_VALUES), request abort, and strict refusal of malformed records. The authorizer and filter roles are out of scope until needed.

See [`dev/PLAN.md`](./dev/PLAN.md) for the full scope, what is deliberately left to other crates, and the decision log.

<hr>
<br>

## Contributing

See <a href="./dev/DIRECTIVES.md"><code>dev/DIRECTIVES.md</code></a> for engineering standards and the definition of done. Before a PR: `cargo fmt --all`, `cargo clippy --all-targets --all-features -- -D warnings`, and `cargo test --all-features` must be clean.

<br>

<div id="license">
    <h2>License</h2>
    <p>Licensed under either of</p>
    <ul>
        <li><b>Apache License, Version 2.0</b> &mdash; <a href="./LICENSE-APACHE">LICENSE-APACHE</a></li>
        <li><b>MIT License</b> &mdash; <a href="./LICENSE-MIT">LICENSE-MIT</a></li>
    </ul>
    <p>at your option.</p>
</div>

<div align="center">
  <h2></h2>
  <sup>COPYRIGHT <small>&copy;</small> 2026 <strong>James Gober.</strong></sup>
</div>
