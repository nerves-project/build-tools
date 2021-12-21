# nerves-project/build-tools

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
