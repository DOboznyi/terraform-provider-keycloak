---
page_title: "keycloak_openid_client_optional_scopes Resource"
---

# keycloak\_openid\_client\_optional\_scopes Resource

Allows for managing a Keycloak client's optional client scopes. An optional scope that is attached to a client using the
OpenID Connect protocol will allow a client to request it using the OAuth 2.0 `scope` parameter. When requested, the scope's
protocol mappers defined within that scope will be used to build claims for this client.

Note that this resource attempts to be an **authoritative** source over optional scopes for a Keycloak client using the
OpenID Connect protocol. This means that once Terraform controls a particular client's optional scopes, it will attempt
to remove any optional scopes that were attached manually, and it will attempt to add any optional scopes that were detached
manually.

By default, Keycloak sets the `address`, `phone`, `offline_access`, and `microprofile-jwt` scopes as optional scopes for
every newly created client. If you create this resource for the first time and do not include these scopes, a following
run of `terraform plan` will result in changes.

## Example Usage

```hcl
resource "keycloak_realm" "realm" {
  realm   = "my-realm"
  enabled = true
}

resource "keycloak_openid_client" "client" {
  realm_id  = keycloak_realm.realm.id
  client_id = "test-client"

  access_type = "CONFIDENTIAL"
}

resource "keycloak_openid_client_scope" "client_scope" {
  realm_id = keycloak_realm.realm.id
  name     = "test-client-scope"
}

resource "keycloak_openid_client_optional_scopes" "client_optional_scopes" {
  realm_id  = keycloak_realm.realm.id
  client_id = keycloak_openid_client.client.id

  optional_scopes = [
    "address",
    "phone",
    "offline_access",
    "microprofile-jwt",
    keycloak_openid_client_scope.client_scope.name
  ]
}

```

## Argument Reference

- `realm_id` - (Required) The realm this client and scopes exists in.
- `client_id` - (Required) The ID of the client to attach optional scopes to. Note that this is the unique ID of the client generated by Keycloak.
- `optional_scopes` - (Required) An array of client scope names to attach to this client as optional scopes.

## Import

This resource does not support import. Instead of importing, feel free to create this resource
as if it did not already exist on the server.