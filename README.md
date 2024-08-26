# gha-versioning 🏷️

![GitHub Tag](https://img.shields.io/github/v/tag/yoanm/gha-versioning?sort=semver&logo=githubactions&logoColor=white&logoSize=auto&link=https%3A%2F%2Fgithub.com%2Fyoanm%2Fgha-versioning%2Freleases)
 [![CI](https://github.com/yoanm/gha-versioning/actions/workflows/CI.yml/badge.svg)](https://github.com/yoanm/gha-versioning/actions/workflows/CI.yml)

Lightweight composite github action managing `vX` and `vX.Y` tags for a github action.

---

Designed to act upon created or edited github releases, action will create or override `vX` and `vX.Y` tags based on the provided tag.

> [!NOTE]
> **Action will also override the original tag** (reference and the message stay as is though), in order to keep it as latest tag !
> 
> Otherwise, when creating a new github releases with an automatically generated changelog, `vX` or `vX.Y` tag will be picked as previous tag rather than the expected previous `vX.Y.Z` tag.

## Usage

> [!IMPORTANT]
> - Do not forget to set `contents: write` permission !
> - When action is used with another event than release creation (or edition), you must fill the `tag` input !

```yaml
name: Publish

on:
  release:
    types: [published, edited]

jobs:
  tag:
    name: Publish vX and vX.Y tags
    runs-on: ubuntu-latest
    if: ${{ github.event.release.prerelease == false && github.event.release.draft == false }}
    permissions:
      contents: write # Required to manage tags & releases !
    steps:
      - name: Checkout
        uses: actions/checkout@v4

      - name: Generate vX and vX.Y tags
        uses: yoanm/gha-versioning@v1
```

> [!TIP]
> The repository uses the action too, therefore a `vX` and a `vX.Y` tag are automatically created for each release !

### `vX` / `vX.Y` on GitHub Marketplace
If you want to make `vX` and/or `vX.Y` versions available on the marketplace, follow this procedure:

- Use `update-inner-releases` at `true` when using the action

  ```yaml
  - name: Generate vX and vX.Y tags
    uses: yoanm/gha-versioning@v1
    with:
      update-inner-releases: true
  ```

- Wait for the related tag (`vX` or `vX.Y`) to be created by this action.
- Manually create the releases targeting the tag (`vX` or `vX.Y` or both, up to you) and publish it to the marketplace.

Then continue creating `vX.Y.Z` releases as usual, related existing `vX` and `vX.Y` releases will be automatically updated to reflect the actual `vX.Y.Z` version.

## Inputs
- `tag`: Default to `${{ github.event.release.tag_name }}`

  The full tag used to generate vX and vX.Y tags

- `update-inner-releases`: Default to `false`.

  Whether to also update releases name linked to `vX` and `vX.Y` tags and set them as latest if provided tag release is currently the latest release.

  > [!IMPORTANT]
  > Will work only on pre-existing releases !

- `inner-releases-as-latest`: Default to `auto`.

  Whether to set inner releases as latest.
  - `auto`: Will be set only if releases linked to provided `tag` is the latest
  - `true`: Set them as latest whatever if provided `tag` release is the latest or not
  - `false`: Do not set them as latest

- `git-email`: Default to `github-actions[bot]@users.noreply.github.com`. 

  _Git user email is required when creating tag with a message._
- `git-name`: Default to `github-actions[bot]`.
  
  _Git user name is required when creating tag with a message._
- `working-directory`: Default to `${{ github.workspace }}`.

  Directory to the Git repository to tag.

## Outputs
- `minor-tag`: Minor tag created (e.g. `vX.Y`)
- `major-tag`: Major tag created (e.g. `vX`)
