---
layout: "docs"
page_title: "MFA"
sidebar_current: "docs-concepts-mfa"
description: |-
    MFA can be specified on all the ACL'd paths in Vault.
---

# MFA

Policy driven Multi-factor Authentication (MFA) can be enforced on all the
ACL'd paths of Vault (all but login and unauthenticated endpoints).

## MFA Types

Vault supports the following types of second factor types.

* TOTP

## Steps for setting up TOTP MFA

### Configure an MFA method

```
vault write sys/mfa/method/my_totp type=totp issuer=vault
```

This command configures new MFA method of type `totp`. Using this
configuration, a TOTP secret key is generated and stored in the entity by
invoking the following command.

### Generate MFA secret on an entity

```
vault write sys/mfa/method/my_totp/admin-generate entity_id=1119a250-d91f-7478-036a-d7c56729d151
```

This command creates a new secret based on the configuration above and stores
the secret key along with the configuration information in the entity. The
`entity_id` can be created directly using the Identity Store's API or it will be
tied to all the tokens which are created by logging in via any of the
authentication backends.

### Specify the MFA method on policy path

```
path "secret/foo" {
    ...
    mfa_methods = ["my_totp"]
}
```

```
vault policy-write policy-name policy.hcl
```

The method name here is the same as the one configured above. There can be
multiple MFA methods specified on a single path. If there are multiple methods
found on a path, Vault requires credentials from all the methods to be supplied
to get through the ACL system.


### Supply credentials on the API

```
vault read -mfa my_totp:123456 secret/foo
```

## API

Please see the [System Backend MFA API](/api/system/mfa/index.html) for all the
MFA related API details.
