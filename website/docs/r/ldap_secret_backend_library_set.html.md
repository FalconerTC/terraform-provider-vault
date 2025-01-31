---
layout: "vault"
page_title: "Vault: vault_ldap_secret_backend_library resource"
sidebar_current: "docs-vault-resource-ldap-secret-backend-library"
description: |-
  Creates a library on the LDAP Secret Backend for Vault.
---

# vault\_ldap\_secret\_backend\_library

Creates a library on an LDAP Secret Backend for Vault. Libraries create
a pool of existing LDAP service accounts which can be checked out
by users.

~> **Important** All data provided in the resource configuration will be
written in cleartext to state and plan files generated by Terraform, and
will appear in the console output when Terraform runs. Protect these
artifacts accordingly. See
[the main provider documentation](../index.html)
for more details.

## Example Usage

```hcl
resource "vault_ldap_secret_backend" "config" {
  path          = "ldap"
  binddn        = "CN=Administrator,CN=Users,DC=corp,DC=example,DC=net"
  bindpass      = "SuperSecretPassw0rd"
  url           = "ldaps://localhost"
  insecure_tls  = "true"
  userdn        = "CN=Users,DC=corp,DC=example,DC=net"
}

resource "vault_ldap_secret_library" "qa" {
  mount                        = vault_ldap_secret_backend.config.path
  name                         = "qa"
  service_account_names        = ["Bob", "Mary"]
  ttl                          = 60
  disable_check_in_enforcement = true
  max_ttl                      = 120
}
```

## Argument Reference

The following arguments are supported:

* `namespace` - (Optional) The namespace to provision the resource in.
  The value should not contain leading or trailing forward slashes.
  The `namespace` is always relative to the provider's configured [namespace](/docs/providers/vault#namespace).
   *Available only for Vault Enterprise*.

* `path` - (Required) The path the LDAP secret backend is mounted at,
  with no leading or trailing `/`s.

* `name` - (Required) The name to identify this set of service accounts.
  Must be unique within the backend.

* `service_account_names` - (Required) Specifies the slice of service accounts mapped to this set.

* `ttl` - (Optional) The password time-to-live in seconds. Defaults to the configuration
  ttl if not provided.

* `max_ttl` - (Optional) The maximum password time-to-live in seconds. Defaults
  to the configuration max_ttl if not provided.

* `disable_check_in_enforcement` - (Optional) Disable enforcing that service
  accounts must be checked in by the entity or client token that checked them
  out. Defaults to false.

## Import

LDAP secret backend libraries can be imported using the `path`, e.g.

```
$ terraform import vault_ldap_secret_backend_library.set ldap/library/bob
```
