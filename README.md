# pj-nvim

Neovim plugin layer for [kata](https://github.com/yukimemi/kata)
templates. Compose it under
[`pj-base`](https://github.com/yukimemi/pj-base).

Lua is interpreted, so there is no build system to wire up. What
every Neovim plugin *does* share is the CI shape and the formatting
policy — that is what this layer carries.

## What it ships

| File | Mode | Notes |
|---|---|---|
| `.github/workflows/ci.yml` | overwrite, always | 3 OS x nvim stable/nightly test matrix + a `stylua --check` lint job |
| `.stylua.toml` | overwrite, always | Shared formatting policy |
| `.styluaignore` | overwrite, once | Seeds `deps/`; consumer-owned afterwards |
| `.kata/vars.toml` | merge-toml, once | Action pins + `nvim.test_runner` |
| `AGENTS.md` | merge-section, always | `<!-- kata:agents:nvim:* -->` block |

## Picking a test framework

The plugins split on framework, so `ci.yml` branches on
`nvim.test_runner` in the consumer's `.kata/vars.toml`:

```toml
[nvim]
test_runner = "mini"     # or "plenary"
```

- **`mini`** (default) — clones `echasnovski/mini.nvim` into
  `deps/mini.nvim`, runs each `tests/**/test_*.lua` through
  `scripts/run_tests.lua`.
- **`plenary`** — clones `nvim-lua/plenary.nvim` into
  `deps/plenary.nvim`, runs each `tests/**/*_spec.lua` through
  `PlenaryBustedFile` with `tests/minimal_init.lua`.

The value is seeded once and never overwritten, so switching a plugin
to `plenary` sticks across applies.

Everything around the test step — the matrix, the nvim setup, the
lint job — is identical either way. That similarity is the reason
this layer exists: it was previously copy-pasted across eight plugin
repos, and they had already drifted (one had no lint job at all).

## Usage

Via the `nvim` preset in
[`pj-presets`](https://github.com/yukimemi/pj-presets):

```sh
kata init --preset nvim
```

Or added to an existing project:

```sh
kata add github.com/yukimemi/pj-nvim
```

## License

MIT
