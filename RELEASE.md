# Building and publishing the orb

The orb is built using the CircleCI pack command

```bash
circleci config pack src > build-tools.yml
circleci orb validate build-tools.yml

Orb at `build-tools.yml` is valid.

circleci orb publish build-tools.yml nerves-project/build-tools@$(cat VERSION)
```
