[中文版](README_cn.md) | [English](README.md)

# MimicLite Integration Repository

This repository is the public-facing integration entry point for the MimicLite
training and deployment stack. It does not vendor source files from the training
or deployment projects. Instead, it pins each component through Git submodules so
the public release boundary is explicit and auditable.

> Naming note: the current GitHub repository is `Roboparty/MimicLite`. Before a
> final public release, it should be renamed to a lowercase underscore name such
> as `roboparty_mimic_lite` to match the Roboparty repository naming policy.

## Layout

```text
MimicLite/
  README.md
  README_cn.md
  LICENSE
  MAINTAINERS
  reports/
    template/
  training/
    active-adaptation/        # submodule
      projects/mimic-lite/    # nested submodule from active-adaptation
  deploy/
    sim2real/                 # submodule
```

## Submodules

| Path | Repository | Branch | Purpose |
| --- | --- | --- | --- |
| `training/active-adaptation` | `git@github.com:Agent-3154/active-adaptation.git` | `dev/hdmi` | Training workspace entry point. |
| `training/active-adaptation/projects/mimic-lite` | `git@github.com:EGalahad/mimic-lite.git` | `main` | MimicLite project. |
| `deploy/sim2real` | `git@github.com:EGalahad/sim2real.git` | `main` | Deployment and sim2real integration code. |

`training/active-adaptation/projects/mimic-lite` is declared by the nested
`.gitmodules` file inside `training/active-adaptation`, because Git stores
submodules owned by a child repository in that child repository.

## Clone

```bash
git clone git@github.com:Roboparty/MimicLite.git MimicLite
cd MimicLite
git submodule update --init --recursive --remote
```

For an existing checkout:

```bash
git pull --recurse-submodules
git submodule sync --recursive
git submodule update --init --recursive --remote
```

The parent repository still records concrete submodule commits for
reproducibility. The `--remote` flag tells Git to follow the branches declared in
`.gitmodules`, such as `training/active-adaptation` on `dev/hdmi` and nested
`projects/mimic-lite` on `main`.

## Public Release Boundary

- The nested `mimic-lite` submodule tracks the reviewed `main` branch.
- `reports/template/` is intentionally empty for now and reserved for the common
  Roboparty technical report template.
- Do not commit secrets, robot logs, private datasets, checkpoints, internal
  endpoints, or private deployment topology.
- Demo credentials in documentation must be fake placeholders and must be marked
  as fake.

## License

This integration repository is released under GPL-3.0-or-later. Component
submodules keep their own repository histories and license files; verify
component license compatibility before publishing a release.
