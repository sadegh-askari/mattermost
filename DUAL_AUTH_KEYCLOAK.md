# Local password and Keycloak login

This fork is based on Mattermost `v11.10.0` (the exact release targeted by the existing `mattermost-oidc` Keycloak patch). It allows one Mattermost account to use both its local password and a Keycloak OpenID Connect identity.

## Account behavior

- A manually reviewed mapping keeps the existing Mattermost user ID and history, sets `AuthService` to `openid`, and stores the Keycloak `sub` claim in `AuthData`.
- When the auth service is `openid`, setting that link preserves the existing password hash. Other external auth providers retain Mattermost's normal single-method behavior.
- Local password login is accepted only when the account has both a nonempty local password hash and a nonempty OpenID subject.
- Password changes and password resets keep the OpenID subject attached.
- An OIDC login does not attach itself to an existing local account by email. Existing users must be linked by an administrator-reviewed subject map. An unknown Keycloak user can be provisioned through OIDC after all legacy accounts are linked and Mattermost account creation is enabled.
- The same account can use local MFA with its Mattermost password and Keycloak MFA on the OIDC path.

For a staged rollout, enable Mattermost email/username password sign-in and OIDC. Keep email signup disabled. Enable user creation only after all legacy user mappings have been applied; then new Mattermost accounts can be created by Keycloak sign-in.

## Build

The OIDC provider source is vendored under `third_party/mattermost-oidc` at the compatible patch revision. Build an AMD64 image from this fork with:

```sh
docker buildx build --platform linux/amd64 --load \
  -f Dockerfile.dual-auth \
  -t sadeghdev/mattermost-oidc:11.10.0-dual-auth \
  .
```

`Dockerfile.dual-auth` compiles with Go 1.26.4 and caps Go build parallelism at two workers. The runtime layer uses the existing pinned Mattermost 11.10.0 OIDC image so the deployment's configuration, entrypoint, plugins, and file-store layout stay consistent.

## Focused checks

The changes add model and app integration coverage for password preservation, local password authentication, password changes, and retained Keycloak subjects. The OIDC provider test ensures email matches alone do not link identities.
