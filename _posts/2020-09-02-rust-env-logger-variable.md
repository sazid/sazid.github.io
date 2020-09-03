---
---

The `env_logger` crate in Rust which provides the underlying logging
facilities for the `log` crate requires an environment variable to be
set which configures the log levels (and subsequently which logs to)
show in the console - `RUST_LOG`.

## Log levels

- error
- warn
- info
- debug
- trace

## Commands for various shells

1. Powershell
   ```powershell
   $Env:RUST_LOG="debug"
   ```

2. CMD
   ```batch
   set RUST_LOG=debug
   ```

3. Bash/ZSH - Linux/Mac
   ```sh
   export RUST_LOG="debug"
   ```

[More information](https://docs.rs/log/0.4.1/log/enum.Level.html).

PS. I'm liking how simple the logging facility is with the
`env_logger` and `log` crates!
