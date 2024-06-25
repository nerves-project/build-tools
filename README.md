# nerves-project/build-tools

A collection of common commands, jobs, and executors for building, testing, and
deploying Nerves systems on CircleCI.

## Usage

See [this orb's listing in CircleCI's Orbs Registry](https://circleci.com/orbs/registry/orb/nerves-project/build-tools)
for details on usage, or see below example:

## Examples

The following example shows how to build a system, construct a test app, and
deploy.

```yaml
exec: &exec
  name: build-tools/nerves-system-br
  version: 1.28.0
  elixir: 1.17.1-otp-27

version: 2.1

orbs:
  build-tools: nerves-project/build-tools@dev:0.3.0

workflows:
  version: 2
  build_test_deploy:
    jobs:
      - build-tools/get-br-dependencies:
          exec:
            <<: *exec
          context: org-global
          filters:
            tags:
              only: /.*/
      - build-tools/build-system:
          exec:
            <<: *exec
          resource-class: large
          context: org-global
          requires:
            - build-tools/get-br-dependencies
          filters:
            tags:
              only: /.*/
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

### CircleCI download cache

By default the packages downloaded by Buildroot (`~/.nerves/dl`), are cached by
CircleCI for use in other steps. If you would like to disable saving this cache,
set `save-dl-cache: false` in the `build-tools/get-br-dependencies` job for your
Nerves system's CI workflow.

```yaml
workflows:
  version: 2
  build_test_deploy:
    jobs:
      - build-tools/get-br-dependencies:
          exec:
            <<: *exec
          save-dl-cache: false
```

### Package download sites

System builds download software packages from many sites on the Internet. In
order to mitigate download failures from sites going offline, package URLs
changing, slow downloads, etc, Buildroot supports primary and backup download
sites. The Buildroot project maintains the default backup download site.
Nerves-specific packages are not on the Buildroot site. Keeping an archive of
all downloaded packages is important to ensure that your project can be built in
the future.

The Nerves project maintains a package download site. This orb will use it by
setting `BR2_PRIMARY_SITE` to it. Buildroot will attempt to download packages
from there first. If they don't exist, the normal download URLs will be tried
and if those fail, Buildroot's backup download site will be tried.

This orb can push new packages to the download site for future builds. Only AWS
S3 is supported. To use this feature, you will need to set parameters as
environment variables in CircleCI:

- Add `download-site-url: https://<download-site-url>` to the
  `build-tools/get-br-dependencies` job and set it to the public URL of the S3
  bucket. The steps to create the bucket are below.

```yaml
workflows:
  version: 2
  build_test_deploy:
    jobs:
      - build-tools/get-br-dependencies:
          exec:
            <<: *exec
          download-site-url: https://<bucket-name>.s3.amazonaws.com
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
  - `DOWNLOAD_SITE_BUCKET_URI` (`s3://<bucket-name>/`)
  - `PUSH_TO_DOWNLOAD_SITE` (`true`)

- If you would like your Nerves system to pull packages from the download site
  when building outside of CI, set the Buildroot primary site in your Nerves
  system `nerves_defconfig` file to the S3 bucket URL:
  `BR2_PRIMARY_SITE="https://<bucket-name>.s3.amazonaws.com"`

### Creating a release

To create a release for your custom Nerves system:
  - Update the system's `VERSION` file with the version number for the release,
    *without* a leading `v` (`1.0.0`).
  - Update `CHANGELOG.md`, ensuring to create a header with the version number
    that *includes* the leading `v` (`## v1.0.0`).
  - Commit these changes.
  - Add a tag to this commit named after the version that *includes* the leading
    `v` (`v1.0.0`).
  - Push the commit and tag to the git remote (GitHub).
  - CircleCI will detect the tag and create a draft release with the Nerves
    system artifact.
  - Review the draft release in GitHub, change the name, and publish the release.

#### Configuration

CircleCI requires a GitHub personal access token to be able to create the GitHub
draft release.

To create this token:
  - Navigate to your GitHub user settings (click avatar) `->` developer settings
    `->` personal access tokens `->` fine-grained tokens.
  - Click the `generate new token` button.
  - Fill out the fields according to your needs until you get to
    `repository access`.
  - Select either `all repositories` or `only select repositories`.
  - Under `repository permissions`, set:
    - `Contents` - read and write
    - `Metadata` - read-only

Add this token to your CircleCI project's or context's `GITHUB_TOKEN`
environment variable.
