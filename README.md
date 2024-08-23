# gha-versioning 🏷️

![GitHub Tag](https://img.shields.io/github/v/tag/yoanm/gha-versioning?sort=semver&logo=githubactions&logoColor=white&logoSize=auto&link=https%3A%2F%2Fgithub.com%2Fyoanm%2Fgha-versioning%2Freleases)

Lightweight composite github action managing `vX` and `vX.Y` tags for a github action.

---

Designed to act upon created or edited github releases, action will create or override `vX` and `vX.Y` tags based on the provided tag.

> [!NOTE]
> **Action will also override the original tag**, in order to keep it as latest tag !
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
    permissions:
      contents: write # Required to push new tags !
    steps:
      - name: Checkout
        uses: actions/checkout@v4

      - name: Generate vX and vX.Y tags
        uses: yoanm/gha-versioning@v1
```

> [!TIP]
> The repository uses the action too, therefore a `vX` and a `vX.Y` tag are automatically created for each release !

## Inputs
- `tag`: Default to `${{ github.event.release.tag_name }}`
- `git-email`: Default to `github-actions[bot]@users.noreply.github.com`. 

  _Git user email is required when creating tag with a message._
- `git-name`: Default to `github-actions`.
  
  _Git user name is required when creating tag with a message._

## Outputs
- `minor-tag`: Minor tag created (e.g. `vX.Y`)
- `major-tag`: Major tag created (e.g. `vX`)
