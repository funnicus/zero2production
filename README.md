# Zero to Production

Example Rust backend app, from the book by the same name.

## Development

Running the app:

```bash
cargo watch -x check -x test -x run # Compile, lint and test on file change
```

Commands for testing, auditing, linting etc:

- cargo test
- cargo tarpaulin --ignore-tests
- cargo clippy -- -D warnings
- cargo fmt -- --check
- cargo audit
- cargo +nightly udeps

For inspecting macros

**cargo expand**
or
**cargo +nightly expand**

## Deployement

You can automatically create and deploy this project as a digital ocean app using the spec.yaml at the root of this project:

```bash
doctl auth init # Log in

doctl apps create --spec spec.yaml # Create the app

doctl apps list # List apps and get their IDs

doctl apps update YOUR-APP-ID --spec=spec.yaml # Update the app
```

## If you want faster builds...

````bash
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
````
