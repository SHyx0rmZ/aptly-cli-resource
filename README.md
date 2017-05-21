# aptly CLI Resource

Enables you to transfer packages between your job and an [aptly](https://www.aptly.info/) repository in your [Concourse](http://concourse.ci/) pipeline.

## Source Configuration

* `api_uri`: *Required.* URL pointing to the aptly repository's API.

* `repo_uri`: *Required.* URL pointing to the aptly repository.

* `component`: *Required.* Repository component the package should be placed in.

* `distribution`: *Required.* Distribution that the package is built for.

* `package`: *Required.* Name of the package.

### Example

Resource configuration:

``` yaml
resources:
- name: concourse-deb
  type: aptly-cli
  source:
    api_uri: http://127.0.0.1:8080/
    repo_uri: http://127.0.0.1/concourse/
    component: concourse
    distribution: jessie
    package: concourse
```

Downloading latest version of package:

```yaml
- get: concourse-deb
  params:
    archive: concourse_latest.deb
```

Uploading new version of package:

``` yaml
- put: concourse-deb
  params:
    archive_file: source-code/filename
    gpg_passphrase_file: /root/aptly.pass
```

## Behavior

### `check`: Detect all versions of package.

Detects versions of the package.

### `in`: Download latest version of package

Downloads the latest version of the package.

The following files will be placed in the destination:

* `(filename|params.archive)`: The downloaded package. Either named after the filename or after the `archive` parameter, if it is set.

* `filename`: Contains the filename of the package, as sent by aptly.

* `path`: Contains the path to the downloaded package.

#### Parameters

* `archive`: *Optional.* Where the downloaded package should be placed.

### `out`: Upload package

Uploads a new version of a package to aptly, signs it, creates a snapshot, and updates the published repository.

#### Parameters

* `gpg_passphrase_file`: *Required.* Path to a file containing a key to sign packages with on the Aptly server.

One of the following two options must be specified:

* `archive`: *Optional.* Path to the package that should be uploaded.

* `archive_file`: *Optional.* Path to a file containing the path to the package that should be uploaded.
