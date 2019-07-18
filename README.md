# nerves-project/build-tools

A collection of common commands, jobs, and executors for building, testing, and
deploying Nerves systems.

## Usage

See [this orb's listing in CircleCI's Orbs Registry](https://circleci.com/orbs/registry/orb/nerves-project/build-tools)
for details on usage, or see below example:

## Examples

The following example shows how to build a system, construct a test app,
and deploy.

```yaml
exec: &exec
  name: build-tools/nerves-system-br
  version: 1.8.4
  elixir_version: 1.9.0-otp-22

version: 2.1

orbs:
  build-tools: nerves-project/build-tools@0.1.0

workflows:
  version: 2
  build_test_deploy:
    jobs:
      - build-tools/build-system:
          exec:
            <<: *exec
          context: org-global
          filters:
            tags:
              only: /.*/
      - build-tools/build-test:
          exec:
            <<: *exec
          context: org-global
          requires:
            - build-tools/build-system
      - build-tools/deploy-test:
          exec:
            <<: *exec
          context: org-global
          requires:
            - build-tools/build-test
      - build-tools/deploy-system:
          exec:
            <<: *exec
          context: org-global
          requires:
            - build-tools/build-system
          filters:
            branches:
              ignore: /.*/
            tags:
              only: /v.*/
```
