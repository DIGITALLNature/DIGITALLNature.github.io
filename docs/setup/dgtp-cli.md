# dgtp CLI

## Install

```shell
dotnet tool install -g dgt.power
```

## Update / uninstall

```shell
dotnet tool update -g dgt.power
dotnet tool uninstall -g dgt.power
```

Update regularly — `dgtp` ships an interceptor that warns on the console if your installed
version is behind, but don't rely on noticing that; check at the start of a new project or
sprint.

## Profiles

`dgtp` connects to Dataverse through a **profile**: a named, stored authentication
configuration, so you don't pass a connection string on every invocation.

```shell
dgtp profile create dev "AuthType=OAuth;Url=https://yourorg.crm.dynamics.com;AppId=...;RedirectUri=...;LoginPrompt=Auto"
dgtp profile list
dgtp profile select dev
dgtp profile delete dev
dgtp profile purge   # removes all profiles
```

`dgtp profile create` takes the profile **name** and **connection string** as *positional*
arguments (there is no `--name`/`--connectionstring` option), plus flags including `--msal`
(token-based, for CI) and `--skipcheck`. The full option list is in the
[Command Reference → `profile`](../reference/dgtp-commands.md#profile).

Run `dgtp profile auth-check` before automated/CI runs to verify the active profile's MSAL token
is still valid without triggering an interactive login — see
[Command Reference → `profile`](../reference/dgtp-commands.md#profile) for exit codes.

- Use **interactive/OAuth** profiles for local developer work — this is the same
  [Microsoft Entra](https://learn.microsoft.com/en-us/entra/fundamentals/whatis) sign-in flow
  you already use for `pac` and the Dataverse web UI.
- Use **token-based** (`--msal`, service principal / client secret) profiles for CI — never
  store a personal account's credentials in a pipeline
  ([`DGT-ALM-070`](../alm/build-pipeline.md#dgt-alm-070)). See
  [Azure DevOps](../alm/azure-devops.md#pipeline-structure) and
  [GitHub Actions](../alm/github-actions.md#secrets-authentication) for how those are wired
  into pipeline secrets.
- Switch the active profile with `dgtp profile select <name>` before running any other command
  — every `dgtp` command operates against whichever profile is currently selected. There is no
  per-command `--profile` override; the alternative to a selected profile is an inline
  connection string (see [Connecting](#connecting-profile-or-connection-string) below).

## Where profiles are stored

Profiles are stored in isolated storage scoped to the current user, not in the project
directory and not in source control — there is nothing to `.gitignore` here, but also nothing
to share between teammates; each developer creates their own profiles locally.

## Connecting: profile or connection string

A command uses an **inline connection string** when one is configured (`xrm:connection`,
typically the `dgtp:xrm:connection` environment variable in CI) — which **takes precedence over a
selected profile** — otherwise it falls back to the selected profile. That's why CI needs no
stored profile: setting `dgtp:xrm:connection` from a pipeline secret is enough.

```yaml title="CI: connect via environment variable, no profile"
env:
  dgtp:xrm:connection: $(PowerPlatformConnectionString)
```

Full resolution order and the `xrm:*` settings: see
[Command Reference → Connecting & global configuration](../reference/dgtp-commands.md#connecting-global-configuration).

## Configuration files

`dgtp.json` (optional, in the current directory) holds *program-wide* settings — most usefully
`pollrate` (default **5000 ms**, the Dataverse async-operation poll interval). Many commands also
take their own per-command `-c`/`--config` file (e.g. `codegeneration`, `analyze`, several
`maintenance` tasks). Both kinds — and the `dgtp:`-prefixed environment-variable form — are
documented in
[Command Reference → Connecting & global configuration](../reference/dgtp-commands.md#connecting-global-configuration).

