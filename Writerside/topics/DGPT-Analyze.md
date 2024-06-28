# Analyze Command

Analyze specific Dataverse Artifacts in the current environment.

## Sub-Commands

This table lists the commands used in the DigitallPower CLI.

| Command                                                | Description                                                                            |
|--------------------------------------------------------|----------------------------------------------------------------------------------------|
| _dgtp analyze_ **[entityallassets](#entityallassets)** | Scans the specified solutions for entities containing with assets                      |
| _dgtp analyze_ **noactivelayer**                       | Scans the specified (unmanaged) solutions for components without active layer          |
| _dgtp analyze_ **redundantcomponents**                 | Scans for components that are in multiple of the specified solutions                   |
| _dgtp analyze_ **activelayer**                         | Scans the specified (managed) solutions for components with active layer               |
| _dgtp analyze_ **toplayer**                            | Scans the specified (managed) solutions for components where solution is not top layer |

### entityallassets

### noactivelayer

### redundantcomponents

### activelayer

Checks given solution for any components that have an existing active layer

| Parameter          | Description                                                                           |
|--------------------|---------------------------------------------------------------------------------------|
| --inline           | Comma separated list of solution unique names to analyze                              |
| --generate-report  | Generate a csv report of the findings in a file './Analyze/ActiveLayer-result.csv'    |
| --generate-summary | Generate a json report of the findings in a file './Analyze/ActiveLayer-summary.json' |

```Bash
dgtp analyze activelayer --inline UniqueSolutionName
```

### toplayer
