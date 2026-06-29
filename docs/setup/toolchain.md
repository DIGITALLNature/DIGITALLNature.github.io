# Toolchain

Install these before opening the project for the first time.

## Core

| Tool | Notes |
|---|---|
| [.NET SDK](https://dotnet.microsoft.com/download) 8.0+ | Required by `dgtp` itself; plugin projects still target `net462` at runtime — see [Project Setup](../coding/serverside/project-setup.md). |
| [Node.js](https://nodejs.org) LTS | For TypeScript web resources and PCF. Use whatever the current LTS is at project start, pinned via `.nvmrc`/`engines` — don't hardcode an old minor version in this guideline or in a `package.json`. |
| [Power Platform CLI (`pac`)](https://aka.ms/PowerAppsCLI) | Install via the **Power Platform Tools** VS Code extension, or standalone. Keep it updated: `pac install latest`. |
| [`dgtp`](https://github.com/DIGITALLNature/DigitallPower) | `dotnet tool install -g dgt.power` — see [dgtp CLI](dgtp-cli.md). |
| [XrmToolBox](https://www.xrmtoolbox.com/) | For anything with a good plugin already (ribbon/command bar edits, bulk metadata changes) instead of hand-rolling it. |

## IDE

Visual Studio, JetBrains Rider, or VS Code with the C# Dev Kit all work for server-side
projects; VS Code is the default for client-side (TypeScript/PCF) work regardless of which IDE
you use for C#.

## Project-specific additions

A given project may add to this list — e.g. a specific PCF generator version, a data migration
tool, or Azure CLI for projects with a wider Azure footprint. Document additions in the
project's own README rather than only in chat/onboarding conversations, so the next person who
clones the repository doesn't have to ask.

