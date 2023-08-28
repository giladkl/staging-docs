This guide will walk you through the process of deploying Epsio for Epsio in your AWS environment.
## Before you begin
Before proceeding with the deployment guide, ensure that you have the following:

- The VPC and subnet in your AWS account where you want to deploy Epsio in.  
      Make sure that the VPC has the required [network access policy](../networking#network-acl-requirements).
- A PostgreSQL Database with version 10+.
- An AWS user with the following permissions: 
    * Create, launch, stop, and rollback CloudFormation stacks.
    * Create and modify IAM users, roles, and policies.
---

## 1. Launch Epsio in your Cloud Environment
After you sign up to Epsio, a wizards will appear and walk you through the stages of deployment.

You'll be required to choose the deployment size, medium is recommended for most deployments.  

Epsio uses a [Cloudformation](https://docs.aws.amazon.com/cloudformation/index.html) stack to create and manage your Epsio deployment.  
To launch Epsio's cloudformation template, press "Launch CloudFormation" in the wizard.

  ![Launch CloudFormation](/assets/deployment/wizard/AWS/instance-size-and-launch-cloud-formation.png){ width="600" loading=lazy}


You'll be requested to choose the VPC and Subnet in which you want to deploy Epsio.

  ![CloudFormation Parameters](/assets/deployment/cloud-formation/vpc-subnet-parameters.png){ width="600" loading=lazy}

To create the stack, scroll to the bottom of the page and select the checkbox labeled  
_I acknowledge that AWS CloudFormation might create IAM resources with customer names_.  
  
After selecting this box, click on the _Create stack_ button.

  ![Create CloudFormation](/assets/deployment/cloud-formation/create-cloud-formation.png){ width="800" loading=lazy}


!!! note ""
    ** Epsio will not have permissions for any resources that weren't created by us. **  
    Check [AWS role permissions](../aws-role-permissions) for a detailed description of the permissions given to the management role.

The CloudFormation stack will now start running.  

You can now go back to Epsio Cloud and track the progress of the deployment in the wizard.

  ![Waiting for CloudFormation](/assets/deployment/wizard/AWS/waiting-for-cloud-formation.png){ width="300" loading=lazy}


Wait until Epsio is successfully deployed, and then proceed to the next step.

---
## 2. Connect Epsio to your database
After the creation of the CloudFormation stack, an Epsio instance will be deployed in your cloud environment.
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

:fontawesome-solid-6: **Enter the credentials of the epsio user you've just created in the wizard and click connect**:  
    You will also need to provide the hostname (or IP address), port and database name.

  ![Connect to database](/assets/deployment/wizard/AWS/connect-to-database.png){ width="400" loading=lazy}


After connection, Epsio will check that your database is configured correctly and will create the functions under the `epsio` schema.  
Continue to the next step to configure logical replication.
 
---
## 3. Configure logical replication
### 3.1 Check if logical replication is enabled
  Run the following command to check if your instance is already configured with logical replication:
=== "AWS RDS"
    ```postgresql
    postgres> SHOW rds.logical_replication;
     rds.logical_replication 
    -------------------------
     off
    (1 row)
    ```
    If the result is `on` (or 1), it means that logical replication is enabled, skip to [set up replication](#33-set-up-replication).  
    If not, follow the steps below to enable logical replication.

=== "AWS Aurora"
    ```postgresql
    postgres> SHOW rds.logical_replication;
     rds.logical_replication 
    -------------------------
     off
    (1 row)
    ```
    If the result is `on` (or 1), it means that logical replication is enabled, skip to [set up replication](#33-set-up-replication).  
    If not, follow the steps below to enable logical replication.

=== "Self Hosted"
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

=== "AWS RDS"
    :fontawesome-solid-1: Create a [custom RDS parameter group](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_WorkingWithDBInstanceParamGroups.html#USER_WorkingWithParamGroups.Creating). If your instance already uses a custom parameter group, skip to the next stage.
    
    ![Custom Parameter Group](/assets/deployment/logical-replication/RDS-create-paramater-group.png){ width="500" loading=lazy}
    
    :fontawesome-solid-2: Edit the custom parameter group. set the `rds.logical_replication` parameter to 1.
    
    ![Edit logical_replication](/assets/deployment/logical-replication/RDS-change-logical-replication-parameter.png){ width="800" loading=lazy}
    
    !!! note ""
        **Optional**: Set the `max_slot_wal_keep_size` parameter to 4096 to limit the amount of WAL data that is retained for logical replication slots. (Postgres 13+)
    :fontawesome-solid-3: Associate the custom parameter group with your RDS instance.
    Go to the RDS management console, select your instance and click on the "Modify" button.
    
    ![Modify DB](/assets/deployment/logical-replication/RDS-modify-instance-button.png){ width="400" loading=lazy}

    In the "Modify DB Instance" page, select the custom parameter group you created in the previous step.
    
    ![Update DB Parameter Group](/assets/deployment/logical-replication/RDS-choose-parameter-group.png){ width="500" loading=lazy}

    Make sure you choose "Apply Immediately" to apply the changes immediately.
    
    ![Apply Parameter Group](/assets/deployment/logical-replication/RDS-modify-instance.png){ width="500" loading=lazy}

    :fontawesome-solid-4: Wait for the parameter group configuration to change to "Pending reboot" status.
    
    The parameter group status can be found in the "Configuration" tab of your RDS instance.
    ![Reset DB](/assets/deployment/logical-replication/RDS-configuration-tab.png){ width="500" loading=lazy}
    ![Reset DB](/assets/deployment/logical-replication/RDS-pending-reboot.png){ width="500" loading=lazy}

    Then, reboot the database for the changes to take effect.  

    You'll know that the changes have taken affect when the status of your DB instance Parameter Group changes to "In Sync".
    
    ![Parameter Group in Sync](/assets/deployment/logical-replication/RDS-in-sync.png){ width="500" loading=lazy}

    :fontawesome-solid-5: Verify that the `rds.logical_replication` parameter is set to "on" (or 1).
    ```postgresql
    SHOW rds.logical_replication;
     rds.logical_replication
    -------------------------
        on
        (1 row)
    ```

=== "AWS Aurora"
    :fontawesome-solid-1: Create a [custom Aurora parameter group](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_WorkingWithDBInstanceParamGroups.html#USER_WorkingWithParamGroups.Creating). If your instance already uses a custom parameter group, skip to the next stage.
    
    ![Custom Parameter Group](/assets/deployment/logical-replication/Aurora-create-parameter-group.png){ width="500" loading=lazy}

    :fontawesome-solid-2: Edit the custom parameter group, set the `rds.logical_replication` parameter to 1.
    
    ![Edit Parameter Group](/assets/deployment/logical-replication/RDS-change-logical-replication-parameter.png){ width="800" loading=lazy}

    !!! note ""
        **Optional**: Set the `max_slot_wal_keep_size` parameter to 4096 to limit the amount of WAL data that is retained for logical replication slots. (Postgres 13+)
    :fontawesome-solid-3: Associate the custom parameter group with your Aurora cluster. Go to the RDS management console, select your instance and click on the "Modify" button.
    
    ![Modify DB](/assets/deployment/logical-replication/RDS-modify-instance-button.png){ width="400" loading=lazy}

    In the "Modify DB Instance" page, select the custom parameter group you created in the previous step.
    
    ![Update DB Parameter Group](/assets/deployment/logical-replication/Aurora-choose-parameter-group.png){ width="500" loading=lazy}

    Make sure you choose "Apply Immediately" to apply the changes immediately.
    
    ![Apply Parameter Group](/assets/deployment/logical-replication/Aurora-modify-cluster.png){ width="500" loading=lazy}

    :fontawesome-solid-4: Wait for the parameter group configuration to change to "Pending reboot" status.
    
    The parameter group status can be found in the "Configuration" tab of your RDS instance.
    ![Reset DB](/assets/deployment/logical-replication/RDS-configuration-tab.png){ width="500" loading=lazy}
    ![Reset DB](/assets/deployment/logical-replication/Aurora-pending-reboot.png){ width="500" loading=lazy}

    Then, reboot the database for the changes to take effect.  

    You'll know that the changes have taken affect when the status of your DB instance Parameter Group changes to "In Sync".
    
    ![Parameter Group in Sync](/assets/deployment/logical-replication/Aurora-in-sync.png){ width="500" loading=lazy}

    :fontawesome-solid-5: Verify that the `rds.logical_replication` parameter is set to "on" (or 1).
    ```postgresql
    SHOW rds.logical_replication;
     rds.logical_replication
    -------------------------
        on
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
=== "AWS RDS"
    ```postgresql
    CREATE PUBLICATION epsio_publication;
    GRANT rds_replication TO epsio_user;
    ```

=== "AWS Aurora"
    ```postgresql
    CREATE PUBLICATION epsio_publication;
    GRANT rds_replication TO epsio_user;
    ```

=== "Self Hosted"
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
