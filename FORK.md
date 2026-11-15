# This fork

`bambithedeer/Gameplane` is a fork of [GameplanePanel/Gameplane](https://github.com/GameplanePanel/Gameplane),
maintained for one homelab cluster (`bambithedeer/cluster`). Upstream is the `upstream` remote. Keep the diff
small, and list every divergence here so an upstream merge knows what to keep.

## Divergences

| What | Where | Why |
|---|---|---|
| Release images are signed with this fork's own cosign key, `signing/fork-cosign.pub` | `.github/workflows/{release,images,publish-edge}.yaml` (the "committed key must match" checks), `deploy/kind/release-consumer-lib.sh` (picks the key by registry) | `signing/cosign.pub` is also upstream's **module-signing** key (`charts/gameplane/values.yaml` `defaultModuleSource.oci.verify`, checked in `ci.yaml`). Replacing it would break verification of upstream's official game modules, which this fork keeps using. Upstream releases that the upgrade fixture starts from still verify with upstream's key |
| Chart default `image.registry: ghcr.io/bambithedeer/gameplane` | `charts/gameplane/values.yaml` | The published chart pulls this fork's images. The module source stays upstream's (`ghcr.io/gameplanepanel/gameplane-modules`) |

## Releases

Tag `vX.Y.Z-ba.N` on the commit to release (`ba` = this fork; the suffix keeps tags clear of upstream's). The
`release` workflow builds and signs the images under `ghcr.io/bambithedeer/gameplane/*` and the chart under
`ghcr.io/bambithedeer/charts/gameplane`. The secrets `COSIGN_PRIVATE_KEY` and `COSIGN_PASSWORD` must be set on
this repo. The packages must be **public**, because the release's consumer check and the cluster both pull
anonymously.

## Syncing upstream

`git fetch upstream --tags && git merge upstream/master` on a branch, resolve against the table above, and run
CI before tagging.
