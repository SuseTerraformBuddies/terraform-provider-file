# Terraform File Provider

Utilities for managing files with Terraform.

## Requirements

- [Terraform](https://developer.hashicorp.com/terraform/downloads) >= 1.5
- [Go](https://golang.org/doc/install) >= 1.23

## Building The Provider

1. Clone the repository
2. Enter the repository directory
3. Build the provider using the Go `install` command:

```shell
go install
```

## Adding Dependencies

This provider uses [Go modules](https://github.com/golang/go/wiki/Modules).
Please see the Go documentation for the most up to date information about using Go modules.

To add a new dependency `github.com/author/dependency` to your Terraform provider:

```shell
go get github.com/author/dependency
go mod tidy
```

Then commit the changes to `go.mod` and `go.sum`.

## Using the provider

### Future Feature List
#### Manage Files in the Local Filesystem at Read and Apply Time
1. write at refresh/read phase
   - use the "filesystem_write" data source to save a file to the local filesystem at read/refresh time
   - WARNING! this purposefully has side effects during plan time!
   - use case: make sure a file exists on the filesystem before other terraform actions can happen (such as plan or apply)
2. write at apply time, within the dependency chain
   - use the "local_file" resource to generate a file at a specific point in time during deployment
3. read at refresh/read phase
   - use a "local_file" data source to read a file from the local file system at read/refresh time
4. read at apply time, within the dependency chain
   - use a "filesystem_read" resource to read a file from the local file system at a specific point during the deployment
   - this allow us to read in files that were generated earlier during the deploy

#### Store File Snapshot in State
1. save file snapshot
   - a "file_snapshot_store" resource which requires "file_path", "archive_path", and "trigger"
     - it will compress the file using gzip
     - it will save the archive to the "archive_path" location
     - it will base64 encode the gzipped archive and save it in state
     - it will only recreate the snapshot when the "trigger" value changes, this gives precise control over when the snapshot is updated
     - it will only store one snapshot per resource
     - it will output the saved contents
   - WARNING! While the files will be stored in archive form many large files will cause performance issues and OOM crashes
2. retrieve file snapshot
   - a "file_snapshot_retrieve" resource which retrieves the value of a snapshot
     - it will require the contents from a "file_snapshot_store" resource
     - it will require a temporary file location on the local filesystem to work in
     - it will base64 decode the contents and store the archive to the filesystem
     - it will decompress the archive and save the output to a file
     - it will base64 encode the decompressed file
     - it will return the base64 encoded file
     - it will run at apply time within the dependency chain
     - the user can then read the contents using Terraform's built in "base64decode" function
   - WARNING! reading large files will cause performance issues and OOM crashes

## Developing the Provider

If you wish to work on the provider, you'll first need [Go](http://www.golang.org) installed on your machine (see [Requirements](#requirements) above).

To compile the provider, run `go install`. This will build the provider and put the provider binary in the `$GOPATH/bin` directory.

To generate or update documentation, run `make generate`.

In order to run the full suite of Acceptance tests, run `make testacc`.

*Note:* Acceptance tests create real resources, and often cost money to run.

```shell
make testacc
```
