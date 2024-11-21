# Solution Concept Documentation

## Overview

This documentation provides a structured guide, including video tutorials, to help users effectively utilize and configure our **Digital Solution Concept**. This innovative solution streamlines the management of solution artifacts across various environments, ensuring efficient deployment and maintenance.

---

## Key Features

- **Workbenches**: Independent, self-contained solutions that enable makers to implement and test changes within the system.
- **Carriers**: Containers for artifacts that facilitate seamless staging of development work across testing and production environments.
- **Provisioning**: A streamlined approach for merging changes between workbenches and carriers.
- **Automatic Semantic Versioning**: Ensures that version control is automatically maintained throughout the process.

---

## Video Tutorials

### 1. Usage Tutorial

This tutorial demonstrates how to effectively utilize the solution. Topics covered include:

- Overview of workbenches
- Creating a new workbench
- Associating a workbench with an existing carrier
- Merging changes from a workbench into a carrier
- Managing workbench statuses
- Best practices for working with Power Platform solutions

<video src="https://youtu.be/DLfFqvcOrrs?si=xeUrjaQqoptmUmmJ" title="DIGITALL Solution Workbench: Usage Tutorial" />

#### Summary of Key Steps for Using a Workbench:
- **Creating a New Workbench**:
  - Use a descriptive name (best practice: reference the work item).
  - Select a target carrier (the solution to which this workbench will be deployed).
  - Automatically generates a workbench solution.
- **Navigating to the Solution**:
  - Use provided buttons to open the solution.
  - Add necessary components and make required changes.
- **Merging Changes**:
  - Merge changes when they are ready for testing.
- **Preparing for Production**:
  - Merge changes into the release carrier and close the workbench after successful deployment to production.
  - Finalize the workbench towards the release carrier if necessary.

### 2. Configuration Tutorial

This tutorial guides users through the configuration process, covering:

- Solution installation
- Solution configuration
- Creating a carrier

<video src="https://youtu.be/FHTKOyrkGoM" title="DIGITALL Solution Workbench: Configuration Tutorial" />

---

## Quick References

| **Name**               | **Description**                                                                                                                                   |
|-------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------|
| **Table: Carrier**      | Reference to a solution that is prepared for deployment (transport solution).                                                                    |
| **Table: Workbench**    | A temporary solution containing items related to a work unit. Must be merged into a target carrier for deployment.                               |
| **Table: Workbench History** | Contains details about copied components and error logs.                                                                                     |
| **Workbench Status: Merge**  | Copies all components from the workbench solution to the target carrier.                                                                    |
| **Workbench Status: Finalize** | Copies all components to the target carrier, deletes the workbench solution, and deactivates the workbench.                               |
| **Workbench Status: Close**  | Deletes the workbench solution and deactivates the workbench.                                                                               |
| **Workbench Status: Failure** | Indicates an error occurred—review history for more details.                                                                               |

---

## Additional Resources

To download the solution, refer to the repository [here](https://github.com/DIGITALLNature/DigitallSolutions/releases).

---
