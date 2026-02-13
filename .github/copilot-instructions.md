# Copilot instructions for ZLUDA

## Big picture (read first)
- ZLUDA is a CUDA compatibility layer targeting non-NVIDIA GPUs (ROCm/HIP backend), not a generic Rust app.
- Runtime flow: CUDA app -> loader/injection (`zluda_inject`, `zluda_redirect`, `zluda_ld`) -> CUDA ABI shim (`zluda`) -> PTX pipeline (`ptx_parser` -> `ptx` -> `llvm_zluda`) -> AMD GPU code.
- CUDA library shims are split per domain: `zluda_blas`, `zluda_blaslt`, `zluda_dnn8`, `zluda_dnn9`, `zluda_fft`, `zluda_sparse`, `zluda_ml`.
- Tracing is a separate stack (`zluda_trace*` + `zluda_trace_common` + `format`), with trace library names exposed via `trace/...` aliases.

## Build and packaging workflow
- Use `cargo xtask` (not ad-hoc `cargo build`) for normal workspace builds; docs define this as the canonical path.
- Main commands:
  - `cargo xtask` -> debug build
  - `cargo xtask --release` -> release build
  - `cargo xtask zip --release` -> package artifacts (`tar.gz` on Unix, `.zip` on Windows)
- `xtask` enforces `--locked`, discovers buildable crates from Cargo metadata, then creates platform-specific symlinks/copies.
- Linux packaging patches DT_NEEDED entries (via `patchelf`) to unversioned ROCm sonames for ROCm 6/7 compatibility.
- Clone/update with submodules (`--recursive`), because `ext/llvm-project` and other externals are required.

## Workspace conventions that matter
- Cross-crate build exposure is controlled by `[package.metadata.zluda]` in each crate `Cargo.toml`.
- Metadata fields used by tooling: `linux_only`, `windows_only`, `debug_only`, `linux_symlinks`, `windows_paths`, `windows_extra_files`.
- Example patterns:
  - `compiler/Cargo.toml`: `debug_only = true` (offline compiler `zoc` only in debug xtask builds)
  - `zluda_inject/Cargo.toml`: `windows_only = true`
  - `zluda/Cargo.toml`: exports `nvcuda` cdylib and ships extra Windows runtime DLLs via metadata
- When adding a new CUDA shim cdylib, update metadata aliases so canonical CUDA names resolve correctly on both platforms.

## Platform-specific code patterns
- Windows build scripts commonly use delay-load link args (`delayimp.lib`, `/DELAYLOAD:...`) for ROCm DLLs; keep this style.
- `zluda_inject/build.rs` compiles helper binaries only on Windows debug builds; avoid enabling this path on Linux/release.
- `llvm_zluda/build.rs` intentionally builds LLVM in Release for normal debug builds; use Cargo profile `dev-llvm` only when LLVM debugging is needed.

## Practical agent rules
- Prefer minimal, crate-scoped changes; this workspace has many specialized crates with tight ABI expectations.
- If changing exported CUDA-facing behavior, check both the base crate and matching trace crate(s) for parity.
- For build failures, inspect `build.rs` and crate metadata before changing Rust logic; many issues are platform/link layout problems.
- Do not remove symlink/path aliases in metadata unless you also update packaging/runtime lookup expectations.
