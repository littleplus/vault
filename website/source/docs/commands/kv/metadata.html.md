---
layout: "docs"
page_title: "kv metadata - Command"
sidebar_title: "<code>metadata</code>"
sidebar_current: "docs-commands-kv-metadata"
description: |-
  The "kv metadata" command has subcommands for interacting with the metadata
  endpoint in Vault's key-value store.
---

# kv metadata

~> **NOTE:** This is a [K/V Version 2](/docs/secrets/kv/kv-v2.html) secrets
engine command, and not available for Version 1.


The `kv metadata` command has subcommands for interacting with the metadata and
versions for the versioned secrets (K/V Version 2 secrets engine) at the
specified path.  


## Usage

```text
Usage: vault kv metadata <subcommand> [options] [args]

  # ...

Subcommands:
    delete    Deletes all versions and metadata for a key in the KV store
    get       Retrieves key metadata from the KV store
    put       Sets or updates key settings in the KV store
```

### kv metadata delete

The `kv metadata delete` command deletes all versions and metadata for the
provided key.

#### Examples

Deletes all versions and metadata of the key "creds":

```text
$ vault kv metadata delete secret/creds
Success! Data deleted (if it existed) at: secret/metadata/creds
```


### kv metadata get

The `kv metadata get` command retrieves the metadata of the versioned secrets at
the given key name. If no key exists with that name, an error is returned.

#### Examples

Retrieves the metadata of the key name, "creds":

```text
$ vault kv metadata get secret/creds
======= Metadata =======
Key                Value
---                -----
cas_required       false
created_time       2019-06-06T04:07:33.279432Z
current_version    5
max_versions       0
oldest_version     0
updated_time       2019-06-06T06:03:26.595978Z

====== Version 1 ======
Key              Value
---              -----
created_time     2019-06-06T04:07:33.279432Z
deletion_time    n/a
destroyed        false

====== Version 2 ======
Key              Value
---              -----
created_time     2019-06-06T06:03:12.187441Z
deletion_time    n/a
destroyed        false

...
```


### kv metadata put

The `kv metadata put` command can be used to create a blank key in the K/V v2
secrets engine or to update key configuration for a specified key.


#### Examples

Create a key in the K/V v2 with no data at the key "creds":

```text
$ vault kv metadata put secret/creds
Success! Data written to: secret/metadata/creds
```

Set the maximum number of versions to keep for the key "creds":

```text
$ vault kv metadata put -max-versions=5 secret/creds
Success! Data written to: secret/metadata/creds
```

**NOTE:** If not set, the backend’s configured max version is used. Once a key
has more than the configured allowed versions the oldest version will be
permanently deleted.


Require Check-and-Set for the key "creds":

```text
$ vault kv metadata put -cas-required secret/creds
```

**NOTE:** When check-and-set is required, the key will require the `cas`
parameter to be set on all write requests. Otherwise, the backend’s
configuration will be used.

#### Output Options

- `-format` `(string: "table")` - Print the output in the given format. Valid
  formats are "table", "json", or "yaml". This can also be specified via the
  `VAULT_FORMAT` environment variable.

#### Subcommand Options

- `-cas-required` `(bool: false)` - If true the key will require the cas
 parameter to be set on all write requests. If false, the backend’s
 configuration will be used. The default is false.

- `-max-versions` `(int: 0)` - The number of versions to keep per key. If not
 set, the backend’s configured max version is used. Once a key has more than the
 configured allowed versions the oldest version will be permanently deleted.