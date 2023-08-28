This guide will walk you through the process of deploying Epsio in your GCP environment.
## Before you begin
Before proceeding with the deployment guide, ensure that you have the following:

- A VM instance to run epsio on with:
    - Network access to your PostgreSQL instance.
    - Inbound rule allowing TCP traffic to port 5449.
    - `docker`, `docker compose` plugin and `zip` installed on it
- A PostgreSQL Database with version 10+.
---

## 1. Launch Epsio in your VM
After you sign up, a wizard will appear and walk you through the stages of deployment.

:fontawesome-solid-1: Choose the architecture of your VM:

  ![Launch Epsio](/assets/deployment/wizard/GCP/GCP_main.png){ width="600" loading=lazy}


 :fontawesome-solid-2: Run the displayed command in your VM:

  ![Run command](/assets/deployment/wizard/GCP/install_command.png){ width="600" loading=lazy}


Wait until Epsio is successfully deployed, and then proceed to the next step.

---
## 2. Connect Epsio to your database
The next step will be to configure your database and connect it to Epsio.

Open a connection to your database and follow the steps below.

:fontawesome-solid-1: **Create a schema for Epsio's metadata:**
    ```postgresql
    CREATE SCHEMA epsio;
    ```

:fontawesome-solid-2: **Create a database user for Epsio's exclusive use:**  
    Replace secret with a strong password
    ```postgresql
    CREATE USER epsio_user WITH PASSWORD 'secret';
    ```

:fontawesome-solid-3:  **Grant user permissions**  
:octicons-command-palette-24: Grant the `epsio_user` access to the `epsio` schema:
```postgresql
GRANT USAGE ON SCHEMA epsio TO epsio_user;
GRANT CREATE ON SCHEMA epsio TO epsio_user;
GRANT ALL PRIVILEGES ON ALL TABLES IN SCHEMA epsio TO epsio_user;
```
:octicons-command-palette-24: Grant the `epsio_user` read-only access to all tables in your schema by running the following commands:
```postgresql
GRANT USAGE ON SCHEMA my_schema TO epsio_user;
GRANT SELECT ON ALL TABLES IN SCHEMA public TO epsio_user;
ALTER DEFAULT PRIVILEGES IN SCHEMA public GRANT SELECT ON TABLES TO epsio_user;
```
!!! note ""
    If you plan to access schemas other than the public schema, you'll need to run these commands for each schema.  
    Replace `public` with the name of your schema.

:fontawesome-solid-4:  **Enable the foreign data wrappers and dblink extensions on your database:**
    ```postgresql
    CREATE EXTENSION IF NOT EXISTS postgres_fdw;
    CREATE EXTENSION IF NOT EXISTS dblink;
    GRANT USAGE ON FOREIGN DATA WRAPPER postgres_fdw TO epsio_user;
    ```

:fontawesome-solid-5:  **Create a publication that will be used to consume changes:**
    ```postgresql
    CREATE PUBLICATION epsio_publication;
    ```

:fontawesome-solid-6: **Enter the Hostname / IP of the VM you deployed Epsio in**:  

  ![Connect to database](/assets/deployment/wizard/GCP/host_fill.png){ width="400" loading=lazy}

:fontawesome-solid-7: **Enter the credentials of the epsio user you've just created in the wizard and click connect**:  
    You will also need to provide the hostname (or IP address), port and database name.

  ![Connect to database](/assets/deployment/wizard/GCP/db_details.png){ width="400" loading=lazy}


After connection, Epsio will check that your database is configured correctly and will create the functions under the `epsio` schema.  
Continue to the next step to configure logical replication.
 
---
## 3. Configure logical replication
### 3.1 Check if logical replication is enabled
  Run the following command to check if your instance is already configured with logical replication:
    ```postgresql
    postgres=> SHOW wal_level;
     wal_level 
    -----------
     replica
    (1 row)
    ```
    If the result is  `logical`, it means that logical replication is enabled, skip to [set up replication](#33-set-up-replication).  
    If not, follow the steps below to enable logical replication.

### 3.2 Enable logical replication

=== "GCP Cloud SQL"
    :fontawesome-solid-1: Edit your PostgreSQL server.
    
    :fontawesome-solid-2: Edit the Server's flags.
    
    ![Flags edit](/assets/deployment/wizard/GCP/flags_edit.png){ width="400" loading=lazy}

    :fontawesome-solid-3: Turn on the `cloudsql.logical_decoding` flag.
    
    ![New Flag](/assets/deployment/wizard/GCP/new_flag.png){ width="400" loading=lazy}

    :fontawesome-solid-4: Save the new configuration.


    :fontawesome-solid-5: Restart the server.
    
    ![New Flag](/assets/deployment/wizard/GCP/save_and_restart.png){ width="600" loading=lazy}

    :fontawesome-solid-6: Verify that the `wal_level` parameter is set to "logical".
        ```postgresql
        SHOW wal_level;
         wal_level
        -------------------------
            logical
            (1 row)
        ```

=== "Self Hosted"
    :fontawesome-solid-1: To enable logical replication in a PostgreSQL database, you need to set the `wal_level` parameter in your database configuration to logical. For standard PostgreSQL installations, you can do this by either:

    * **Method 1:** Adding a `wal_level = logical` line to the `postgresql.conf` file.
    * **Method 2:** Running `ALTER SYSTEM SET wal_level = logical;`;
    !!! note ""
        **Optional**: Set the `max_slot_wal_keep_size` parameter to 4096 to limit the amount of WAL data that is retained for logical replication slots. (Postgres 13+)

    :fontawesome-solid-2: Restart your database for the changes to take effect.  
    
    :fontawesome-solid-3: Verify that the `wal_level` parameter is set to "logical".
        ```postgresql
        SHOW wal_level;
         wal_level
        -------------------------
            logical
            (1 row)
        ```

### 3.3 Set up replication
Next, you'll need to set up replication by running the following commands in your database:
    ```postgresql
    CREATE PUBLICATION epsio_publication;
    ALTER USER epsio_user WITH REPLICATION;
    ```

Once finished, click on the "Validate Configuration" and Epsio will verify that logical replication is set up correctly.

![Parameter Group in Sync](/assets/deployment/wizard/AWS/setup-replication.png){ width="400" loading=lazy}

Congratulations! You've successfully enabled logical replication in your database.

---


Once Epsio successfully connects to your database, you'll be redirected to the Epsio dashboard.

You are set to go and can create your first view.
Visit the [create_view](/reference/views-management/create_view) for further details.
