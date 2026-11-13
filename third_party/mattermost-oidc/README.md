# Vendored Mattermost OpenID Connect provider

This directory vendors the provider from [`toowoxx/mattermost-oidc`](https://github.com/toowoxx/mattermost-oidc), revision `5632504c7819866fe592b0df20df7a60ff4f84dc`, under the included AGPL-3.0 license.

The provider supplies the generic OIDC implementation used by this Mattermost fork. The fork's `DUAL_AUTH_KEYCLOAK.md` documents how it is integrated and the dual local-password/Keycloak behavior added here.

Account linking is intentionally administrator-managed. `IsSameUser` accepts only an already-linked matching OIDC subject; an email match alone does not link identities. Existing Mattermost users are mapped to Keycloak subjects by the reviewed account map in the deployment workspace.
