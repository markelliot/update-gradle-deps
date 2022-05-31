markelliot/update-gradle-deps@v1
================================
This action checks for updates to Java dependencies and Gradle plugins and submits a PR with
any such updates. It works for Java projects that use Gradle as the build tooling and depends
on the [`com.markelliot.versions`](https://github.com/markelliot/gradle-versions) Gradle plugin
tasks `checkNewVersions`, `updateVersionsProps`, `updatePlugins` and `updateGradleWrapper`.
Compatible projects will also be managing dependencies with a versions.props file (such as with
the `nebula.dependency-recommender` or `com.palantir.gradle-consistent-versions` plugins).

In addition to updating versions.props and build script plugin references, this action will
run `./gradlew --write-locks` to update any relevant lock files.

To reduce noise from a scheduled version of this action, it pushes changes to a dedicated
branch (default `auto/versions`) and only creates a PR if there are updates. If a PR exists
and newer dependency or plugin versions are available, the branch is force-overwritten.
Additionally, the action will consistently bring the branch up to date with the primary
development branch.

Note: a GitHub Personal Access Token with `repo` permission is required so that the created 
PR triggers any other repository-defined actions. If you do not wish to trigger downstream
actions you may use the action-supplied `GITHUB_TOKEN`.

Note: you must set `fetch-depth` to `0` on the `actions/checkout` step to allow this action
to force-overwrite the branch, should new dependencies or plugins be available.

Configuration
-------------
Required configuration:
* `push-to-repo-token`: a Personal Access Token with `repo` permission (or pass an
  appropriately permissioned action `GITHUB_TOKEN` if you do not with to trigger additional
  actions).
  
  Note that your checkout action should use the same token. See the sample below.
* `commit-user`: the username to use when creating an update commit
* `commit-email`: the email address to use when creating an update commit (typically
  `<commit-user>@users.noreply.github.com`
 
Overridable defaults:
* `commit-title`: the title to use for the update commit (default `Auto-update dependencies and plugins`)
* `primary-branch`: the branch to target the update pull request (default `develop`)
* `working-branch`: the branch to use for new commits (default `auto/versions`)
* `pr-title`: the title to use for the update pull request (default `Auto-update dependencies and plugins`)

Sample
------
To schedule an update check every day at 10am and allow for manual triggering:
```yaml
name: Update Deps
on:
  # allow manual triggers
  workflow_dispatch: {}
  # run every day at 10am
  schedule:
    - cron: '0 10 * * *'

jobs:
  check:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
        with:
          token: ${{ secrets.GH_PUSH_TO_REPO_TOKEN }}
          fetch-depth: 0
      - uses: actions/setup-java@v2
        with:
          java-version: '17'
          distribution: 'zulu'
      - uses: markelliot/update-gradle-deps@v1
        with:
          push-to-repo-token: ${{ secrets.GH_PUSH_TO_REPO_TOKEN }}
          commit-user: markelliot
          commit-email: markelliot@users.noreply.github.com
```
