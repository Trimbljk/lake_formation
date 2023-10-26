# Lake Formation Permissions and Cross Account Resource Sharing

This folder contains all the information associated with Lake Formation and cross account resource sharing. 

## Purpose: Lake Formation allows production data to be shared to development, sandbox and other accounts for real workflow engineering efficency.

All infrastructure built for lake formation is represented in multiple cloudformation templates. They are as follows:

1. **lake_formation_base_infrastructure** - This template builds a bucket, dynamodb table, sns topic, Athena data base and some outputs that the other templates reference
2. **lake_formation_all_tables** - This template builds all the glue tables, that are represented in Athena, that the lake formation engine uses
3. **lake_formation_cross_account** - This template is the engine that captures any changes to the tables in lake formation. Newly created tables will be shared to the other accounts in the AgBiome Org. Currently, they are only accessible in the development account.
4. **lake_formation_recipient_side** - This template is deployed to the development side and it retrieves table information coming from the production account.
5. **lake_formation_query_checker** - This template builds infrastructure that captures FAILED queries in athena. The idea being that any Lake Formation failures will be mitigated quicker. 

Think Lake Formation engine is designed to handle as many scenerios as possible when a new table is created or a table is deleted. It also handles data path location updates. The Lake Formation engine checks for changes to tables every 30 minutes. If there are updates to be made the engine will run and process all the tables respectively.

<img src="https://github.com/AgBiome/data-science-infrastructure/blob/lake-formation/lake-formation/images/table_actions.png" height="400" width="450">

## Primary Permissions

- When a table is shared to the organizations role arn, Lake formation handles the underlying resource access manager (RAM) specifics. The table will then be available to all accounts in the organization, presuming it was not only shared to a sub group of accounts. This doesn't mean the data is available. A resource-link has to be created that references the underlying data in the production account.
- The Lake Formation engine shares and deletes the resources from production that are shared to the other accounts. It also grants "grants" to allow Lake formation admins in other accounts to share the data to other roles in those accounts. Only roles that have been granted Lake Formation Administrator privileges can access the data via a resource link at the time of sharing. As mention 2 sentences ago, only then can an LF administrator share that data to another role or user.

## Table Views

- Views are not shared from production to other accounts. The LF engine handles them by skipping over them when sorting event records
- If a view needs to be replicated, it can be done manually in the account it's needed.


