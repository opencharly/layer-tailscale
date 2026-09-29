# AGENTS.md — layer-tailscale

Standalone candy repo for the `tailscale` layer — the Tailscale mesh VPN daemon
baked in as an enabled systemd service. The candy lives in `charly.yml` at the
repo root: the `package:` / `distro:` repository sections, the `plan:` steps
(including the build-time `systemctl enable tailscaled.service`), and the
embedded `skill:` entity projected into the marketplace corpus as
`/charly-infrastructure:tailscale`.

Canonical files:

- `charly.yml` — the `tailscale:` candy entity and the `tailscale-skill:` skill
  entity.
- `.github/workflows/tag-on-merge.yml` — CalVer tag + `CHANGELOG/` on merge.
- `README.md` — user overview only; never agent guidance.

## Load these skills first (R0)

- `/charly-infrastructure:tailscale` — the owning skill. The daemon install, the
  `tailscaled.service` enable, and the difference from the deploy-time sidecar
  model. Load before editing or troubleshooting the layer.
- `/charly-image:layer` — the candy authoring reference (`charly.yml` schema,
  `plan:` step verbs incl. `check:`, per-distro `distro:` arms, package/repo
  sections, service declarations). Load before editing any entity field or plan
  step.

## Build / validate / test

- The merge gate is the **org-wide** `charly/pr-validator` (required check
  `validate / validate`, defined in `opencharly/.github`); this repo has **no
  per-repo candy gate** and ships only `.github/workflows/tag-on-merge.yml`.
- The candy's `plan:` `check:` steps are the functional evidence. Every check is
  a build-time fact (binary present, `tailscale version` runs offline, unit
  enabled) — keep them valid on every `distro:` arm they run on.
- The upstream repository sections are per-distro; a package/repo change must be
  applied to every arm that ships the candy.

## Modify this repo

- Edit the `tailscale:` candy entity AND the `tailscale-skill:` skill entity in
  `charly.yml` together. The skill is the projected usage source, so a behaviour
  change not mirrored in the skill leaves the corpus stale.
- New behaviour claims belong in the `plan:` as an observable `check:` step, and
  in the skill body.

## Landing

- PR-only. Every change lands through a pull request; the org-required
  `charly/pr-validator` validates the diff and body and arms native auto-merge on
  PASS. Direct pushes to `main` are blocked.
- History lives in `CHANGELOG/` (written by `tag-on-merge` at merge time); the PR
  body IS the changelog.
- The authoritative rulebook is the umbrella `AGENTS.md` in
  `opencharly/opencharly` and `charly/AGENTS.md` in the charly repo. Do not
  restate its rules here.
