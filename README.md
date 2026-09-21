# Delta Chat Tauri

Delta Chat desktop with tauri as runtime instead of electron.

## Start

Requirements

- rust (install it via https://rustup.rs)
- nodejs (use your favorite node version manager like [fnm](https://github.com/Schniz/fnm)) and [pnpm](https://pnpm.io) package manager
- on linux you need a few system dependencies:
  - debian/ubuntu `sudo apt-get install -y libwebkit2gtk-4.0-dev libwebkit2gtk-4.1-dev libappindicator3-dev librsvg2-dev patchelf`

This repo carries [deltachat-desktop](https://github.com/deltachat/deltachat-desktop)
as the `upstream/` submodule and builds its frontend, so clone with submodules
and install both workspaces:

```
git clone --recurse-submodules <this repo>
# or, in an existing clone: git submodule update --init

pnpm -C upstream install
pnpm i
pnpm tauri dev # or pnpm start
```

## Check code

```
pnpm check:types
pnpm check:upstream-version

```

Format rust code:

```
pnpm fmt
```

Lint rust code:

```
pnpm lint
# or
cd src-tauri && cargo clippy
```

## Following upstream

The pinned `upstream/` submodule is the source of truth for our version numbers,
so after moving the pointer these have to be brought back in line by hand. The
reference is upstream's version:

```
grep -m1 '"version"' upstream/package.json
```

**1. Our version** must equal it in two places — `version` in `package.json` and
in the `[package]` section of `src-tauri/Cargo.toml`.

**2. Catalog entries.** Every key in our `pnpm-workspace.yaml` `catalog:` has to
exist in `upstream/pnpm-workspace.yaml` with the exact same spec string

**3. deltachat-core.** The `deltachat` and `deltachat-jsonrpc` dependencies in
`src-tauri/Cargo.toml` pull core straight from git, so they must be kept in sync with upstream

Both crates need `tag = "v<X>"` and `version = "<X>"`, where `<X>` is upstream's
catalog entry for `@deltachat/jsonrpc-client`
This one is worth double checking: a mismatch here builds fine and only shows up as
JSON-RPC errors at runtime.

## Generate the icon

```
pnpm tauri icon deltachat-tauri2.svg --ios-color #2c3e50
```

## Recommended IDE Setup

- [VS Code](https://code.visualstudio.com/) + [Tauri](https://marketplace.visualstudio.com/items?itemName=tauri-apps.tauri-vscode) + [rust-analyzer](https://marketplace.visualstudio.com/items?itemName=rust-lang.rust-analyzer)

## Profiling startup time and get more information about tauri

You can use https://devtools.crabnebula.dev/ for that, for this you need to build with the `crabnebula_extras` feature:

```
pnpm tauri dev -f=crabnebula_extras
```

## Shorten compile times in development

- use [sccache](https://github.com/mozilla/sccache)
- (on linux) use [mold linker](https://github.com/rui314/mold)

# for users

## Log location

| platform | location                                |
| -------- | --------------------------------------- |
| linux    | ~/.config/chat.delta.desktop.tauri      |
| macOS    | ~/Library/Logs/chat.delta.desktop.tauri |
| windows  | %AppData%\chat.delta.desktop.tauri      |

## Data location

| platform | location                                               |
| -------- | ------------------------------------------------------ |
| linux    | ~/.config/chat.delta.desktop.tauri                     |
| macOS    | ~/Library/Application Support/chat.delta.desktop.tauri |
| windows  | %AppData%\chat.delta.desktop.tauri                     |

## Funding

This project was funded through [NGI0 Entrust](https://nlnet.nl/entrust), a fund established by [NLnet](https://nlnet.nl) with financial support from the European Commission's [Next Generation Internet](https://ngi.eu) program. Learn more at the [NLnet project page](https://nlnet.nl/project/DeltaTauri).

[<img src="https://nlnet.nl/logo/banner.png" alt="NLnet foundation logo" width="20%" />](https://nlnet.nl)
[<img src="https://nlnet.nl/image/logos/NGI0_tag.svg" alt="NGI Zero Logo" width="20%" />](https://nlnet.nl/entrust)
