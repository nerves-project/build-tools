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

### Public Cache

This orb can sync the buildroot dependencies to a public cache in AWS S3.
Perform the following steps to enable the public cache for a Nerves system.

- Add `save-public-cache: true` to the `build-tools/get-br-dependencies` job.

```yaml
workflows:
  version: 2
  build_test_deploy:
    jobs:
      - build-tools/get-br-dependencies:
          exec:
            <<: *exec
          save-public-cache: true
```

- Create an AWS S3 bucket.
- Set the bucket policy to allow read-only access for anonymous users.

```json
{
  "Version":"2012-10-17",
  "Statement":[
    {
      "Sid":"PublicRead",
      "Effect":"Allow",
      "Principal": "*",
      "Action":["s3:GetObject","s3:GetObjectVersion"],
      "Resource":["arn:aws:s3:::<bucket-name>/*"]
    }
  ]
}
```

- In the AWS dashboard, create an AWS access key for the AWS user account that
  CI will use to access the S3 bucket.

- Add the following environment variables to the CircleCI project for the
  Nerves system:
  - `AWS_ACCESS_KEY_ID`
  - `AWS_SECRET_ACCESS_KEY`
  - `AWS_REGION`
  - `CACHE_BUCKET_URI` (`s3://<bucket-name>/`)

- In your Nerves system `nerves_defconfig` file, set the buildroot cache to the
  S3 bucket URL: `BR2_PRIMARY_SITE="https://<bucket-name>.s3.amazonaws.com"`
