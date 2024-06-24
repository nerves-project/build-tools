# Changelog

## v0.3.0

This CircleCI Orb is required to build `nerves_system_br` `v1.28.0` and later
systems due to the change to require non-root user builds. If you're not using
`v1.28.0` yet, continue to use `v0.2.x` Orbs.

Please update your `.circleci/config.yml` to have the following:

```yaml
orbs:
  build-tools: nerves-project/build-tools@0.3.0
```

* Changed
  * Don't require root when installing Elixir in `install-elixir`
  * Changes the default install path to `$HOME/elixir`. Use the `install_path`
    parameter to change
  * Reverse order of dep fetch and Nerves downloads to improve build time
  * Remove unused `TAG` var in `build-system`

## v0.2.3

* Changed
  *  `nerves-system-br` executor now points to GitHub Contrainer registry

## v0.2.2

* Changed
  * [get-br-dependencies] Enable download site with environment variable
    (`PUSH_TO_DOWNLOAD_SITE`) instead of CI parameter

## v0.2.1

* Bug fixes
  * [get-br-dependencies] Change default download site URL to be HTTP
    to fix certificate errors when downloading
  * [get-br-dependencies] Only override the download site when the job is
    set to update the download site. If not updating the download site, use
    the nerves_defconfig settings.

## v0.2.0

* Changed
  * Move fetching and caching Buildroot dependencies to its own job
  * https://dl.nerves-project.org is used as BR2_PRIMARY_SITE by default

## v0.1.5

* Bug fixes
  * Add boolean parameter `save-dl-cache` to disable saving the dl cache
    files. Useful for preventing builds with git repos in the dl cache
    from taking too much time.
  * Add list parameter `env-setup` for passing extra steps to execute when
    setting up the build environment.

## v0.1.4

* Bug fixes
  * Added boolean parameter `hex-validate` disable hex package
    validation. This is useful when using dependencies from git.

## v0.1.3

* Bug fixes
  * Allow passing `resource-class` to `build_system` job.

## v0.1.2

* Bug fixes
  * Do not cache `_build` between `build_test` and `deploy_test` jobs. This
    causes issues loading dependency code when calling nerves_hub mix commands.

## v0.1.1

* Bug fixes
  * Update br2-primary-site to be safer and allow it to fail without exiting.

## v0.1.0

Initial release
