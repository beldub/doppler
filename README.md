# doppler — lightweight oracle program for Solana

Doppler is a small on-chain oracle program focused on low compute cost (about 21 CUs per update for the bundled `PriceFeed` payload). The admin-signed update path writes a monotonically increasing sequence and payload into a PDA-owned account.

This repository is a Rust workspace: on-chain program, client SDK, and an example binary. It is **not** a desktop app; there are no release installers here—build from source or integrate the SDK in your own tooling.

## Repository layout

| Path | Purpose |
|------|---------|
| `program/` | Solana program (`doppler` crate), `cdylib` + `lib` |
| `sdk/` | `doppler-sdk` — instruction builders, `Oracle<T>` helpers, declared program id |
| `example/` | Example transaction that updates an oracle account on a cluster |
| `oracle.json` | Sample exported account (for local testing or reference) |

## Prerequisites

- [Rust](https://www.rust-lang.org/tools/install) (stable), `cargo`
- For deploying or integration tests that load the built `.so`: [Solana CLI](https://docs.solanalabs.com/cli/install) and `cargo build-sbf` (or your usual Solana program build flow)

## Build the program

From the workspace root, build the BPF artifact with the Solana toolchain (exact command depends on your Solana version):

```bash
cargo build-sbf -p doppler
```

The deployable artifact is typically under `target/deploy/` (e.g. `doppler.so`).

## Tests

- **SDK unit tests** (no on-chain binary required):

  ```bash
  cargo test -p doppler-sdk
  ```

- **Program integration tests** (`program/tests/`) use [mollusk-svm](https://crates.io/crates/mollusk-svm) against `../target/deploy/doppler`. Build the program first, then:

  ```bash
  cargo test -p doppler
  ```

## Using the example client

The `example` crate sends an update transaction. It expects:

1. A keypair file at `./admin.json` (must match the program’s compiled-in admin).
2. A running RPC endpoint (defaults to `http://localhost:8899`).

Override the RPC URL:

```bash
set SOLANA_RPC_URL=https://api.devnet.solana.com
cargo run -p doppler-example
```

On Unix shells, use `export SOLANA_RPC_URL=...` instead of `set`.

Adjust `oracle_pubkey` in `example/src/main.rs` to your oracle account’s address.

## Program id and admin

The declared program id and admin pubkey are fixed in the source (`sdk` program id, `program` admin constants). Changing them requires a coordinated rebuild and redeploy.

## Contributing

Issues and pull requests are welcome on [GitHub](https://github.com/beldub/doppler).

## License

See [LICENSE](LICENSE).
