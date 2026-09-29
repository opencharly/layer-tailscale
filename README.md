# layer-tailscale

Tailscale mesh VPN daemon baked into an OpenCharly image as an enabled systemd
service.

The `tailscale` candy installs the [tailscale](https://tailscale.com) package —
the `tailscale` CLI plus the `tailscaled` daemon — from the upstream stable
repositories, and enables `tailscaled.service` at build time so the image boots
as its own tailnet node. Bringing the mesh up (`tailscale up --authkey=…`) is a
runtime concern, so every check here is a build-time fact.

## What it provides

| Property | Value |
|---|---|
| Layer / candy | `tailscale` |
| Packages | `tailscale` (upstream repo: `arch` / `debian-13` / `fedora` / `ubuntu-24.04`) |
| Service | `tailscaled.service` (systemd, enabled at build time) |
| Ports | none (WireGuard uses UDP; no host port mapping) |

## How to use it

Compose the layer by pinning this repo in a box's `candy:` list:

```yaml
my-tailnet-image:
  candy:
    base: fedora
    candy:
      - '@github.com/opencharly/layer-tailscale:v2026.240.0121'
```

Then bring the mesh up after boot — a runtime concern, not a build one:

```bash
sudo tailscale up          # interactive browser SSO
tailscale version          # the CLI is on PATH and runs offline
systemctl is-enabled tailscaled.service
```

The candy's `plan:` asserts the CLI at `/usr/bin/tailscale`, the daemon at
`/usr/sbin/tailscaled`, `tailscale version` exiting cleanly, and
`systemctl is-enabled tailscaled.service` returning `enabled`.

## Layout

- `charly.yml` — the `tailscale:` candy entity (the `package:` / `distro:`
  repository sections, the `plan:` steps, the `check:` assertions) and the
  embedded `tailscale-skill:` skill entity.
- `.github/workflows/tag-on-merge.yml` — CalVer tag + `CHANGELOG/` on merge.
- `README.md` — this user overview.

## Related

- Owning skill: `/charly-infrastructure:tailscale`
- `/charly-infrastructure:tailscale-up` — the runtime-config sibling for
  `target: local` host deploys (sets `--operator` + `--hostname`)
- `/charly-distros:container-nesting` — installs tailscale as a *tool* inside a
  nested-podman harness; a different use case, don't use both in one box
- `/charly-automation:sidecar` — deploy-time tailscale sidecar pattern
  (alternative, not a replacement)
- `/charly-core:deploy` — the `charly.yml` tunnel / sidecar configuration
- [`opencharly/charly`](https://github.com/opencharly/charly) — the charly CLI and image builder
- [`opencharly/opencharly`](https://github.com/opencharly/opencharly) — the umbrella
