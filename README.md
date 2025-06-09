# Terraform File Provider

Utilities for managing files with Terraform.

## Current Features

### Save A File in Local Filesystem

Use a Terraform data source to write a file to the local filesystem at refresh phase.

 - this taboo data source makes sure that a file exists on the filesystem before other terraform actions can happen
 - WARNING! this purposefully has side effects during plan time!

Use a Terraform resource to write a file to the local filesystem within the dependency chain, at apply time.

  - this resource writes content to a local file while respecting the dependency chain

### Retrieve A File from the Local Filesystem

Use a Terraform data source to read a file at refresh phase.

  - this is similar to the data source in the local_file provider

Use a Terraform resource to read a file within the dependency chain, at apply time.

  - this read respects the dependency chain, allowing users to read a file that may not have existed at the beginning of the Terraform run.

### Store File Snapshot in State

Use a Terraform resource to store the contents of a file at a current period in time in the state.

  - the contents will be gzip archived and base64 encoded (after archive)
  - the triggers which recreate this resource will determine when the snapshot is updated

### Write File Snapshot to Local Filesystem

Use a Terraform data source to write a file stored in state to the local filesystem

  - this taboo data source makes sure that a file exists on the local filesystem before other terraform actions can happen
  - WARNING! this purposefully has side effects during plan time!
  - this expects the data from the file to be base64 encoded and gzip compressed (eg. using the store_file_snapshot or base64gzip function)
  - it will decode the base64, unpack the archive, and write the file to the local filesystem
  - WARNING! This has serious memory implications, make sure the server running Terraform can handle the files you need to save.

Use a Terraform resource to write a file which exists archived in the state to the local filesystem within the dependency chain, at apply time.

  - this write respects the dependency chain, allowing users to write a file whose content may not exist until a certain point in the Terraform apply
  - this expects the data from the file to be base64 encoded and gzip compressed (eg. using the store_file_snapshot or base64gzip function)
  - it will decode the base64, unpack the archive, and write the file to the local filesystem
  - WARNING! This has serious memory implications, make sure the server running Terraform can handle the files you need to save.


# Terraform Provider Scaffolding (Terraform Plugin Framework)

_This template repository is built on the [Terraform Plugin Framework](https://github.com/hashicorp/terraform-plugin-framework). The template repository built on the [Terraform Plugin SDK](https://github.com/hashicorp/terraform-plugin-sdk) can be found at [terraform-provider-scaffolding](https://github.com/hashicorp/terraform-provider-scaffolding). See [Which SDK Should I Use?](https://developer.hashicorp.com/terraform/plugin/framework-benefits) in the Terraform documentation for additional information._

This repository is a *template* for a [Terraform](https://www.terraform.io) provider. It is intended as a starting point for creating Terraform providers, containing:

- A resource and a data source (`internal/provider/`),
- Examples (`examples/`) and generated documentation (`docs/`),
- Miscellaneous meta files.

These files contain boilerplate code that you will need to edit to create your own Terraform provider. Tutorials for creating Terraform providers can be found on the [HashiCorp Developer](https://developer.hashicorp.com/terraform/tutorials/providers-plugin-framework) platform. _Terraform Plugin Framework specific guides are titled accordingly._

Please see the [GitHub template repository documentation](https://help.github.com/en/github/creating-cloning-and-archiving-repositories/creating-a-repository-from-a-template) for how to create a new repository from this template on GitHub.

Once you've written your provider, you'll want to [publish it on the Terraform Registry](https://developer.hashicorp.com/terraform/registry/providers/publishing) so that others can use it.

## Requirements

- [Terraform](https://developer.hashicorp.com/terraform/downloads) >= 1.0
- [Go](https://golang.org/doc/install) >= 1.23

## Building The Provider

1. Clone the repository
1. Enter the repository directory
1. Build the provider using the Go `install` command:

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

Fill this in for each provider

## Developing the Provider

If you wish to work on the provider, you'll first need [Go](http://www.golang.org) installed on your machine (see [Requirements](#requirements) above).

To compile the provider, run `go install`. This will build the provider and put the provider binary in the `$GOPATH/bin` directory.

To generate or update documentation, run `make generate`.

In order to run the full suite of Acceptance tests, run `make testacc`.

*Note:* Acceptance tests create real resources, and often cost money to run.

```shell
make testacc
```
