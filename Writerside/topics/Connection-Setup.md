# Connection Setup

Quick guide to setup and configure connections and related options for power automate.

## TBD
- Questions to ask:
  - Who shall be owner of the flows, respectively which licenses/limits to run against?
  - Who is holding the connection and what are the implications?
    - Preferred: Shared user
    - Only named user: User leaves, changes in password policies
  - What access are we giving the connection
- Embed image to connection diagram overview
- Deployment
  - What is required to work for automated deployment?
  - What is our recommended way?
- Subtopic: dgtp workflowstates

## Required Users

- A dedicated service user to create and hold the connections
  - Usually an admin user, but would only need basic access to the system
  - Ideally an user with emails that is something 
- One or more application users used for deployments
  - System Administrator Role

## Steps to go along with

1. Collect required [connectors](https://learn.microsoft.com/en-us/connectors/connector-reference/connector-reference-powerapps-connectors)
   - Document them with a short description, what they are used for
   - Create or update [data policy](https://learn.microsoft.com/en-us/power-platform/admin/prevent-data-loss) to include the connectors (split into business and non-business classification where needed)
2. Collect available connections and required information for them (service users, endpoint urls, keys, etc.)
   - Document information including information about access (e.g.: which security role a service user has and which environments he can access)
3. Create connection references
   - Each different purpose should have its own connection references
   - Different connectors automatically require new connection references
   - Different modules/solutions should ideally get its own connection references
   - Document available connection references to indicate what they are being used for (and in turn what type of connection they may need)
4. Create connections
   - Connections should be created by the dedicated service user
   - Created connections should be shared with the application user used for deployments
   - Connections need to be created at least on all environments after dev
   - Document/configure which connection belongs to which connection reference
   - On development developers should create connections by themselves as needed

## Sample documentation

### Used connectors

| Connector Name       | Description                                                   | Classification |
|----------------------|---------------------------------------------------------------|----------------|
| Microsoft Dataverse  | Access data in dataverse environments                         | Business       |
| Microsoft Sharepoint | Access sharepoint to automate document uploads from dataverse | Business       |

### Available Connections

| Name            | Username               | Environment | Description                                  |
|-----------------|------------------------|-------------|----------------------------------------------|
| Dataverse Admin | dataverseadmin@org.com | All         | Technical admin only for holding connections |
