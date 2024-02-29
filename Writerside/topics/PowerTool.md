# DigitallPower CLI

DIGITALLPOWER - the dotnet tool for the PowerPlatform from DIGITALL.
A swiss army knife for all ALM issues where PAC still has weaknesses.

Currently focusing the requirements of DIGITALL in the development, deployment and operation of PowerPlatform solutions.

With the goal of universally helping the community.

## Install DigitallPower CLI

```Shell
dotnet tool install -g dgt.power
```

## Update DigitallPower CLI

```Shell
dotnet tool update -g dgt.power
```

## Uninstall DigitallPower CLI

```Shell
dotnet tool uninstall -g dgt.power
```

# Commands

This table lists the ommands used in the DigitallPower CLI.

| Command                                  |Description|
|------------------------------------------|-----------|
| [dgtp profile](DGPT-Profile.md)          |Managed authentication profiles.|
| [dgtp analyze](DGPT-Analyze.md)          |Analyse specific Dataverse artifacts.|
| [dgtp codegeneration](DGPT-Code-Generation.md)               |Generates .cs, .ts and metadata.xml model files for Dataverse.|
| [dgtp export]        |Export specific Dataverse artifacts.|
| [dgtp import]        |Import specific Dataverse artifacts.|
| [dgtp maintaince] |Handles Authentication.|
| [dgtp push]          |Import assembly Dataverse artifacts.|

> For the complete list of supported commands run the `dgtp` command or `dgtp` \<subcommand> - for example: `dgtp codegeneration`.