# nerves-project/build-tools

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
