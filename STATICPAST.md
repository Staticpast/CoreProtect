# Staticpast build of CoreProtect

This fork builds CoreProtect for the SuegoFaults Minecraft server
(https://github.com/Staticpast/SuegoFaults). The `staticpast` branch is an
upstream release tag plus the small patches below. Nothing else changes.

## Patches on top of the upstream tag

- `ConfigHandler.LATEST_VERSION`: the newest Minecraft version the build
  accepts. The Community Edition refuses to start on a newer version.
- `pom.xml` `project.branch = staticpast`: CoreProtect disables itself when
  the branch is empty. The name must not contain `-dev`, because a `-dev`
  branch skips the database upgrade scripts.

The plugin version stays the upstream release number, so the database schema
is the one upstream released.

## Follow upstream

```bash
git fetch upstream --tags
git rebase --onto <new upstream tag> <old upstream tag> staticpast
# Re-check LATEST_VERSION: drop the patch when upstream already covers it.
```

Follow upstream release tags, not `master`: `master` can carry unreleased
database changes.

## Build and release

```bash
mvn -q -DskipTests package
sha256sum target/CoreProtect-*.jar
gh release create v<version>-staticpast.<n> target/CoreProtect-<version>.jar --title "..." --notes "..."
```

SuegoFaults pulls the release asset with
`mise run plugins:third-party` (`minecraft-config/third-party.toml`).
