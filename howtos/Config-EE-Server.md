# Automatic setup
Please visit the [Auto-setup of Camunda BPM in a Shared Engine Environment](/howtos/was-wls-autosetup.md)

# Manual Setup

## Websphere

### Create a Postgres Datasource on Websphere 9 in Portainer

#### Make JDBC Driver available

1. Copy the Postgres JDBC driver to the file system of the websphere container (if running on Portainer, this can be done via scp. Make sure port 22 is mapped out of the container). Let's call this file `/home/camunda/jdbc/postgres.jar`.

#### Create JDBC Provider

1. In Websphere Admin console, navigate to **Resources -> JDBC -> JDBC providers**.
1. Select a scope, e.g. `Node=localhostNode01` has worked for me.
1. Click `New...`
1. Step 1:
   * Database type: User-defined
   * Implementation class name: `org.postgresql.ds.PGConnectionPoolDataSource`. This must be an implementation of `javax.sql.ConnectionPoolDataSource`.
   * Name: Postgres
1. Step 2:
   * Classpath: `/home/camunda/jdbc/postgres.jar` (local path to jar file).
1. Step 3: Finish and Save.

#### Create a Data Source

1. In Websphere Admin console, navigate to **Resources -> JDBC -> Data Sources**.
1. Select the same scope as before.
1. Click `New...`
1. Step 1:
   * Data source name: process-engine (or any other name)
   * JNDI name: jdbc/ProcessEngine
1. Step 2:
   * Select previously created JDBC provider
1. Step 3: Leave the data store helper as is, i.e. as `com.ibm.websphere.rsadapter.GenericDataStoreHelper`
1. Step 4: Do nothing
1. Step 5: Finish and Save.
1. In the overview, click on the new data source
1. Click on **Custom properties** and set the following:
   * user: `<database user name>`
   * password: `<database user password>`
   * url: `<JDBC url>`, in my case `jdbc:postgresql://portainer.camunda.loc:30039/process-engine`
1. Click **Test connection** to verify that it works
