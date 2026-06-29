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
dgtp profile create --name dev --connectionstring "AuthType=OAuth;Url=https://yourorg.crm.dynamics.com;..."
dgtp profile list
dgtp profile select dev
dgtp profile delete dev
dgtp profile purge   # removes all profiles
```

- Use **interactive/OAuth** profiles for local developer work — this is the same Microsoft
  Entra sign-in flow you already use for `pac` and the Dataverse web UI.
- Use **token-based** (service principal / client secret) profiles for CI — never store a
  personal account's credentials in a pipeline. See
  [Azure DevOps](../alm/azure-devops.md#pipeline-structure) and
  [GitHub Actions](../alm/github-actions.md#secrets-authentication) for how those are wired
  into pipeline secrets.
- Switch the active profile with `dgtp profile select <name>` before running any other command
  — every `dgtp` command operates against whichever profile is currently selected, there is no
  per-command `--profile` override.

## Where profiles are stored

Profiles are stored in isolated storage scoped to the current user, not in the project
directory and not in source control — there is nothing to `.gitignore` here, but also nothing
to share between teammates; each developer creates their own profiles locally.

## Configuration file

Some commands (notably `codegeneration`) read additional settings from `dgtp.json` in the
current directory, plus a `pollrate` default (5000 ms) for how long Dataverse operations are
polled before timing out, overridable via `dgtp:pollrate` environment variable if a slower
sandbox needs more patience.

