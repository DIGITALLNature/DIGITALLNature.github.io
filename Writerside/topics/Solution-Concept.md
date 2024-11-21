# Solution Concept

# Overview

This documentation provides a series of video tutorials designed to guide users through the usage and configuration of our Digital Solution Concept, which facilitates the management of solution artifacts across different environments.

---

## Key Features

- **Workbenches**: Self-contained solutions that enable makers to implement and test changes in the system.
- **Carriers**: Containers for artifacts, allowing seamless staging of development work across testing and production environments.
- **Provisioning**: A streamlined approach for merging changes between workbenches and carriers.
- **Automatic Semantic Versioning**: Ensures version control is maintained automatically throughout the process.

---

## Video Tutorials

### 1. Usage Tutorial
Watch the following video to learn how to effectively utilize the solution. This tutorial covers:

- Overview of workbenches
- Creating a new workbench
- Associating a workbench with an existing carrier
- Merging changes from a workbench into a carrier
- Managing workbench statuses
- Best practices and tips for working with Power Platform solutions
---
<video src="https://youtu.be/DLfFqvcOrrs?si=xeUrjaQqoptmUmmJ" title="DIGITALL Solution workbench: Usage tutorial" />

### Working with a workbench summary
Create new workbench:
    Use descriptive name (best practice to reference work item)
    Select target carrier (which solution this needs to be deployed with)
This automatically creates a workbench solution
Go to the solution (buttons will open the solution)
    Add necessary components
    Do changes
Merge changes if they are ready for test
If ready for prod either:
    Merge in release carrier and close workbench after successful prod deployment
    Finalize workbench towards release carrier

### 2. Configuration Tutorial
Watch the following video to learn how to properly configure the solution. This tutorial covers:

- Solution installation
- Solution configuration
- Create a carrier

<video src="https://youtu.be/FHTKOyrkGoM" title="DIGITALL Solution workbench: Configuration tutorial" />

---
## Quick References
Name	Description
Table: Carrier	Reference to a solution that should be deployed (transport solution)
Table: Workbench	A temporary (workbench) solution containing items relating to a work unit. For deployment needs to be merged towards a target carrier
Table: Workbench History	Holds details about copied components and failure details
Workbench Status: Merge	Copy all components in workbench solution to target carrier
Workbench Status: Finalize	Copy all components in workbench solution to target carrier, delete workbench solution and deactivate workbench
Workbench Status: Close	Delete workbench solution and deactivate workbench
Workbench Status: Failure	Something went wrong - check history for details
---

## Additional Resources

If you are looking to download the solution, refer to the following repository [here](https://github.com/DIGITALLNature/DigitallSolutions/releases).

---