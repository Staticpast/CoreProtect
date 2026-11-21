# Staticpast build of CoreProtect

This fork builds CoreProtect for the SuegoFaults Minecraft server (https://github.com/Staticpast/SuegoFaults). The `staticpast` branch is an upstream release tag plus the patches below.

## Patches on top of the upstream tag

- `ConfigHandler.LATEST_VERSION` 26.2 -> 26.3: the newest Minecraft version the build accepts. The Community Edition refuses to start on a newer version.
- `pom.xml` `project.branch = staticpast`: CoreProtect disables itself when the branch is empty. The name must not contain `-dev`, because a `-dev` branch skips the database upgrade scripts.
- `Bukkit_v26_3`: logs red shrubs, shelf mushrooms and straw beds, which are new in 26.3.
- Copper golem chest transfers are logged on Paper 26.x. It is a backport of upstream commit `ac94648` (upstream issue #1033).
- Dependencies: Paper API 26.3, log4j 2.26.1 (CVE-2026-49844), FastAsyncWorldEdit 2.16.3, AdvancedChestsAPI 3.3, and the newest Maven plugins and test libraries. MockBukkit moves to `org.mockbukkit.mockbukkit:mockbukkit-v1.21`.
- No `.github/FUNDING.yml` or `.github/stale.yml`. They are for the upstream project.
- `.github/workflows/build.yml` builds the `staticpast` branch. Each action is pinned to a commit SHA, the token can only read, and the checkout keeps no credentials.
- `.github/dependabot.yml` opens weekly pull requests for the GitHub Actions SHAs and the Maven dependencies.

Keep one commit per patch. Then a rebase drops a patch that upstream already has, or you drop its commit.

The plugin version stays the upstream release number, so the database schema is the one upstream released.

## Known bugs that upstream fixed after 24.1

These fixes are on upstream `master` only. They need `master` code that 24.1 does not have, so they do not apply to this branch. Take them with the next upstream release.

- Upstream issue #1050: a lookup can show the items of another player's ender chest.
- Upstream issue #1023: cushions are not logged on 26.3.
- Upstream issue #1042: scaffolding that collapses when its support breaks is not logged.

## Follow upstream

```bash
git fetch upstream --tags
git rebase --onto <new upstream tag> <old upstream tag> staticpast
# Re-check each patch: drop it when upstream already has the fix.
```

Follow upstream release tags, not `master`: `master` can carry unreleased database changes.

## Build and release

```bash
mvn -q package
cp target/CoreProtect-<version>.jar CoreProtect-<version>-staticpast.jar
gh release create v<version>-staticpast.<n> CoreProtect-<version>-staticpast.jar \
  --target <full commit SHA> --title "<title>" --notes "Minecraft up to: <LATEST_VERSION>"
```

The first line of the release notes must be `Minecraft up to: <version>`, the same value as `LATEST_VERSION`. SuegoFaults reads it to know which Minecraft versions a release supports. The asset name ends in `-staticpast.jar`. GitHub gives each asset a sha256 digest, and SuegoFaults checks it.

SuegoFaults pulls the release asset with `mise run plugins:third-party` (`minecraft-config/third-party.toml`).
