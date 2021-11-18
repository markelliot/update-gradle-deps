Uses com.markelliot.versions tasks checkNewVersion, updateVersionsProps and updatePlugins to
discover and apply updates to dependencies of a Gradle project and applied plugins. When
updates are found, creates or updates a branch `auto/versions` and creates a PR to propose
the changes.

Note that a Personal Access Token is required so that the created PR triggers any other
repository-defined actions.

