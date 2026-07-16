# loopkit-example-api

A **single-stack (Rust)** proof repo for [loopkit](https://github.com/kapdroid/loopkit) — an existing
repo brought under the loop with `kap adopt`. Its gate (`cargo fmt --check && clippy && test`) is the
definition of done; CI runs exactly that gate, and the loop (`kap start → check → done → merge`) builds
features on top. Scaffolded from loopkit's rust template.
