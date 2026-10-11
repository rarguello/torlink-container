# torlink-container

Run [torlink](https://github.com/baairon/torlink) from a container image, built
straight from the upstream git repo — no local checkout and no host Node.js.

Upstream deliberately does not ship a Dockerfile (container/NAS deployment is out
of scope for an interactive terminal app), so this repo provides one plus a small
launcher.

## Usage

```sh
./torlink                 # build (first run) then launch the TUI
./torlink --help          # any torlink argument is forwarded
TORLINK_REF=v1.9.0 ./torlink    # build a specific tag or branch
TORLINK_REBUILD=1 ./torlink     # force a rebuild
```

Put it on your `PATH`:

```sh
ln -s "$PWD/torlink" ~/.local/bin/torlink
```

## How it works

- `Dockerfile` clones the upstream repo at build time and runs
  `npm ci && npm run build` (multi-stage; the runtime image is Node 24 slim).
- `torlink` builds the image if it is missing and runs it with
  `--userns=keep-id`, keeping state in `~/.local/share/torlink-container` and
  downloads in `~/Downloads/torlink`.

## Environment

| Variable | Default | Purpose |
|---|---|---|
| `TORLINK_IMAGE` | `localhost/torlink:latest` | image tag to build/run |
| `TORLINK_REF` | `main` | upstream branch or tag to build |
| `TORLINK_REBUILD` | `0` | set to `1` to force a rebuild |

## Requirements

- Podman (rootless).
- Network access during the first build (clones the source and installs npm deps).
