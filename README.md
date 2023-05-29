cargo watch -x check

cargo watch -x check -x test -x run

cargo expand
or
cargo +nightly expand

PIPELINE:

- cargo test
- cargo tarpaulin --ignore-tests
- cargo clippy -- -D warnings
- cargo fmt -- --check
- cargo audit

# .cargo/config.toml

# On Windows

# ```

# cargo install -f cargo-binutils

# rustup component add llvm-tools-preview

# ```

[target.x86_64-pc-windows-msvc]
rustflags = ["-C", "link-arg=-fuse-ld=lld"]

[target.x86_64-pc-windows-gnu]
rustflags = ["-C", "link-arg=-fuse-ld=lld"]

# On Linux:

# - Ubuntu, `sudo apt-get install lld clang`

# - Arch, `sudo pacman -S lld clang`

[target.x86_64-unknown-linux-gnu]
rustflags = ["-C", "linker=clang", "-C", "link-arg=-fuse-ld=lld"]

# On MacOS, `brew install michaeleisel/zld/zld`

[target.x86_64-apple-darwin]
rustflags = ["-C", "link-arg=-fuse-ld=/usr/local/bin/zld"]

[target.aarch64-apple-darwin]
rustflags = ["-C", "link-arg=-fuse-ld=/usr/local/bin/zld"]
