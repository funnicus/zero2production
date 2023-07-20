# Zero to Production

Example Rust backend app, from the book by the same name.

## Development

Running the app:

```bash
cargo watch -x check -x test -x run # Compile, lint and test on file change
```

Database migrations:

```bash
sqlx migrate add add_status_to_subscriptions # create a new migration file, fill it with whatever commands you want

SKIP_DOCKER=true ./scripts/init_db.sh # migrate, use SKIP_DOCKER if you already have an existing database you are migrating
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

## Docker

You can build an docker image of this application via the following command `docker build --tag zero2prod --file Dockerfile .`

## Deployement

You can automatically create and deploy this project as an digital ocean app using the spec.yaml at the root of this project:

```bash
# At the root of the project

doctl auth init # Log in

doctl apps create --spec spec.yaml # Create the app

# Incase of any updates to the spec...

doctl apps list # List apps and get their IDs

doctl apps update YOUR-APP-ID --spec=spec.yaml # Update the app

# Remember to push any changes to github!
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

## Linux issue with tests

If you are running Linux, you might see errors like

```bash
thread 'actix-rt:worker' panicked at
'Can not create Runtime: Os { code: 24, kind: Other, message: "Too many open files" }',
```

when you run cargo test.

This is due to a limit enforced by the operating system on the maximum number of open file descriptors (including sockets) for each process - given that we are running all tests from different files as part of a single binary, we might be exceeding it. The limit is usually set to 1024, but you can raise it with ulimit -n X (e.g. `ulimit-n 10000`) to resolve the issue.
