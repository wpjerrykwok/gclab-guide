# Migrate to Cloud SQL for PostgreSQL using Database Migration Service
## GSP918
## Overview
Database Migration Service provides options for one-time and continuous jobs to migrate data to Cloud SQL using different connectivity options, including IP allowlists, VPC peering, and reverse SSH tunnels (see documentation on connectivity options at https://cloud.google.com/database-migration/docs/postgresql/configure-connectivity).

In this lab, you migrate a stand-alone PostgreSQL database (running on a virtual machine) to Cloud SQL for PostgreSQL using a continuous Database Migration Service job and VPC peering for connectivity.

Migrating a database via Database Migration Service requires some preparation of the source database, including creating a dedicated user with replication rights, adding the pglogical database extension to the source database and granting rights to the schemata and tables in the database to be migrated, as well as the postgres database, to that user.

After you create and run the migration job, you confirm that an initial copy of your database has been successfully migrated to your Cloud SQL for PostgreSQL instance. You also explore how continuous migration jobs apply data updates from your source database to your Cloud SQL instance. To conclude the migration job, you promote the Cloud SQL instance to be a stand-alone database for reading and writing data.

## What you'll do
1. Prepare the source database for migration.
2. Create a profile for a source connection to a PostgreSQL instance (e.g., stand-alone PostgreSQL).
3. Configure connectivity between the source and destination database instances using VPC peering.
4. Configure firewall and database access rules to allow access to the source database for migration.
5. Create, run, and verify a continuous migration job using Database Migration Service.
6. Promote the destination instance (Cloud SQL for PostgreSQL) to be a stand-alone database for reading and writing data.

## Setup
### Verify that the Database Migration API is enabled
In the Google Cloud console, enter Database Migration API in the top search bar. Click on the result for Database Migration API.
This page will either show status information or give you the option to enable the API.

If necessary, Enable the API.
### Verify that the Service Networking API is enabled
The Service Networking API is required in order to be able to configure Cloud SQL to support VPC Peering and connections over a private ip-address.

In the Cloud console, enter Service Networking API in the top search bar. Click on the result for Service Networking API.
This page will either show status information or give you the option to enable the API.

If necessary, enable the API.
## Task 1. Prepare the source database for migration
In this task you will add supporting features to the source database which are required in order for Database Migration Service to perform a migration. These are:

Installing and configuring the pglogical database extension.
Configuring the stand-alone PostgreSQL database to allow access from Cloud Shell and Cloud SQL.
Adding the pglogicaldatabase extension to the postgres, orders and gmemegen_db databases on the stand-alone server.
Creating a migration_admin user (with Replication permissions) for database migration and granting the required permissions to schemata and relations to that user.
### Upgrade the database with the pglogical extension
In this step you will download and add the pglogical database extension to the orders and postgres databases on the postgresql-vm VM Instance.

In the Google Cloud console, on the Navigation menu (Navigation menu icon), click Compute Engine > VM instances.

In the entry for postgresql-vm, under Connect click SSH.

If prompted, click Authorize.

In the terminal in the new browser window, install the pglogical database extension:
```
sudo apt install postgresql-13-pglogical
```
Note: pglogical is a logical replication system implemented entirely as a PostgreSQL extension. Fully integrated, it requires no triggers or external programs. This alternative to physical replication is a highly efficient method of replicating data using a publish/subscribe model for selective replication. Read more here: https://github.com/2ndQuadrant/pglogical
Download and apply some additions to the PostgreSQL configuration files (to enable pglogical extension) and restart the postgresql service:
```
sudo su - postgres -c "gsutil cp gs://cloud-training/gsp918/pg_hba_append.conf ."
sudo su - postgres -c "gsutil cp gs://cloud-training/gsp918/postgresql_append.conf ."
sudo su - postgres -c "cat pg_hba_append.conf >> /etc/postgresql/13/main/pg_hba.conf"
sudo su - postgres -c "cat postgresql_append.conf >> /etc/postgresql/13/main/postgresql.conf"

sudo systemctl restart postgresql@13-main
```

In `pg_hba.conf` these commands added a rule to allow access to all hosts:

#GSP918 - allow access to all hosts
host    all all 0.0.0.0/0   md5
In `postgresql.conf`, these commands set the minimal configuration for pglogical to configure it to listen on all addresses:

#GSP918 - added configuration for pglogical database extension

wal_level = logical         # minimal, replica, or logical
max_worker_processes = 10   # one per database needed on provider node
                            # one per node needed on subscriber node
max_replication_slots = 10  # one per node needed on provider node
max_wal_senders = 10        # one per node needed on provider node
shared_preload_libraries = 'pglogical'
max_wal_size = 1GB
min_wal_size = 80MB

listen_addresses = '*'         # what IP address(es) to listen on, '*' is all
The above code snippets were appended to the relevant files and the PostgreSQL service restarted.

Launch the psql tool:
```
sudo su - postgres
```
```
psql
```
Add the pglogical database extension to the postgres, orders and gmemegen_db databases.
\c postgres;
Copied!
CREATE EXTENSION pglogical;
Copied!
\c orders;
Copied!
CREATE EXTENSION pglogical;
Copied!
\c gmemegen_db;
Copied!
CREATE EXTENSION pglogical;
Copied!
List the PostgreSQL databases on the server:
\l
Copied!
Here you can see, besides the default postgresql databases, the orders and gmemegen_db databases provided for this lab. You will not use the gmemegen_db database in this lab, but will include it in the migration for use in a later lab.

                               List of databases
    Name     |  Owner   | Encoding | Collate |  Ctype  |   Access privileges   
-------------+----------+----------+---------+---------+-----------------------
 gmemegen_db | postgres | UTF8     | C.UTF-8 | C.UTF-8 |
 orders      | postgres | UTF8     | C.UTF-8 | C.UTF-8 |
 postgres    | postgres | UTF8     | C.UTF-8 | C.UTF-8 |
 template0   | postgres | UTF8     | C.UTF-8 | C.UTF-8 | =c/postgres          +
             |          |          |         |         | postgres=CTc/postgres
 template1   | postgres | UTF8     | C.UTF-8 | C.UTF-8 | =c/postgres          +
             |          |          |         |         | postgres=CTc/postgres
(5 rows)
### Create the database migration user
In this step you will create a dedicated user for managing database migration.

In psql, enter the commands below to create a new user with the replication role:
```
CREATE USER migration_admin PASSWORD 'DMS_1s_cool!';
ALTER DATABASE orders OWNER TO migration_admin;
ALTER ROLE migration_admin WITH REPLICATION;
```
### Assign permissions to the migration user
In this step you will assign the necessary permissions to the migration_admin user to enable Database Migration Service to migrate your database.

In psql, grant permissions to the pglogical schema and tables for the postgres database.
```
\c postgres;
```
```
GRANT USAGE ON SCHEMA pglogical TO migration_admin;
GRANT ALL ON SCHEMA pglogical TO migration_admin;

GRANT SELECT ON pglogical.tables TO migration_admin;
GRANT SELECT ON pglogical.depend TO migration_admin;
GRANT SELECT ON pglogical.local_node TO migration_admin;
GRANT SELECT ON pglogical.local_sync_status TO migration_admin;
GRANT SELECT ON pglogical.node TO migration_admin;
GRANT SELECT ON pglogical.node_interface TO migration_admin;
GRANT SELECT ON pglogical.queue TO migration_admin;
GRANT SELECT ON pglogical.replication_set TO migration_admin;
GRANT SELECT ON pglogical.replication_set_seq TO migration_admin;
GRANT SELECT ON pglogical.replication_set_table TO migration_admin;
GRANT SELECT ON pglogical.sequence_state TO migration_admin;
GRANT SELECT ON pglogical.subscription TO migration_admin;
```
In psql, grant permissions to the pglogical schema and tables for the orders database.
```
\c orders;
```
```
GRANT USAGE ON SCHEMA pglogical TO migration_admin;
GRANT ALL ON SCHEMA pglogical TO migration_admin;

GRANT SELECT ON pglogical.tables TO migration_admin;
GRANT SELECT ON pglogical.depend TO migration_admin;
GRANT SELECT ON pglogical.local_node TO migration_admin;
GRANT SELECT ON pglogical.local_sync_status TO migration_admin;
GRANT SELECT ON pglogical.node TO migration_admin;
GRANT SELECT ON pglogical.node_interface TO migration_admin;
GRANT SELECT ON pglogical.queue TO migration_admin;
GRANT SELECT ON pglogical.replication_set TO migration_admin;
GRANT SELECT ON pglogical.replication_set_seq TO migration_admin;
GRANT SELECT ON pglogical.replication_set_table TO migration_admin;
GRANT SELECT ON pglogical.sequence_state TO migration_admin;
GRANT SELECT ON pglogical.subscription TO migration_admin;
```
In psql, grant permissions to the public schema and tables for the orders database.
```
GRANT USAGE ON SCHEMA public TO migration_admin;
GRANT ALL ON SCHEMA public TO migration_admin;

GRANT SELECT ON public.distribution_centers TO migration_admin;
GRANT SELECT ON public.inventory_items TO migration_admin;
GRANT SELECT ON public.order_items TO migration_admin;
GRANT SELECT ON public.products TO migration_admin;
GRANT SELECT ON public.users TO migration_admin;
```
In psql, grant permissions to the `pglogical` schema and tables for the `gmemegen_db` database.
\c gmemegen_db;
Copied!
GRANT USAGE ON SCHEMA pglogical TO migration_admin;
GRANT ALL ON SCHEMA pglogical TO migration_admin;

GRANT SELECT ON pglogical.tables TO migration_admin;
GRANT SELECT ON pglogical.depend TO migration_admin;
GRANT SELECT ON pglogical.local_node TO migration_admin;
GRANT SELECT ON pglogical.local_sync_status TO migration_admin;
GRANT SELECT ON pglogical.node TO migration_admin;
GRANT SELECT ON pglogical.node_interface TO migration_admin;
GRANT SELECT ON pglogical.queue TO migration_admin;
GRANT SELECT ON pglogical.replication_set TO migration_admin;
GRANT SELECT ON pglogical.replication_set_seq TO migration_admin;
GRANT SELECT ON pglogical.replication_set_table TO migration_admin;
GRANT SELECT ON pglogical.sequence_state TO migration_admin;
GRANT SELECT ON pglogical.subscription TO migration_admin;

Copied!
In psql, grant permissions to the public schema and tables for the gmemegen_db database.
GRANT USAGE ON SCHEMA public TO migration_admin;
GRANT ALL ON SCHEMA public TO migration_admin;

GRANT SELECT ON public.meme TO migration_admin;
Copied!
The source databases are now prepared for migration. The permissions you have granted to the migration_admin user are all that is required for Database Migration Service to migrate the postgres, orders and gmemegen_db databases.

Make the migration_admin user the owner of the tables in the orders database, so that you can edit the source data later, when you test the migration.

In psql, run the following commands:
\c orders;
\dt
Copied!
ALTER TABLE public.distribution_centers OWNER TO migration_admin;
ALTER TABLE public.inventory_items OWNER TO migration_admin;
ALTER TABLE public.order_items OWNER TO migration_admin;
ALTER TABLE public.products OWNER TO migration_admin;
ALTER TABLE public.users OWNER TO migration_admin;
\dt
Copied!
               List of relations
 Schema |         Name         | Type  | Owner
--------+----------------------+-------+-------
 public | distribution_centers | table | migration_admin
 public | inventory_items      | table | migration_admin
 public | order_items          | table | migration_admin
 public | products             | table | migration_admin
 public | users                | table | migration_admin
(5 rows)
Exit psql and the postgres user session
\q
Copied!
exit
Copied!
Click Check my progress to verify the objective.
Prepare the PostgreSQL source instance for migration.

Task 2. Create a Database Migration Service connection profile for a stand-alone PostgreSQL database
In this task, you will create a connection profile for the PostgreSQL source instance.

Get the connectivity information for the PostgreSQL source instance
In this step, you identify the internal IP address of the source database instance that you will migrate to Cloud SQL.

In the Google Cloud Console, on the Navigation menu (Navigation menu icon), click Compute Engine > VM instances.

Locate the line with the instance called postgresql-vm.

Copy the value for Internal IP (e.g., 10.128.0.2).

Create a new connection profile for the PostgreSQL source instance
A connection profile stores information about the source database instance (e.g., stand-alone PostgreSQL) and is used by the Database Migration Service to migrate data from the source to your destination Cloud SQL database instance. After you create a connection profile, it can be reused across migration jobs.

In this step you will create a new connection profile for the PostgreSQL source instance.

In the Google Cloud Console, on the Navigation menu (Navigation menu icon), click Database Migration > Connection profiles.

Click + Create Profile.

For Database engine, select PostgreSQL.

For Connection profile name, enter postgres-vm.

For Hostname or IP address, enter the internal IP for the PostgreSQL source instance that you copied in the previous task (e.g., 10.128.0.2)

For Port, enter 5432.

For Username, enter migration_admin.

For Password, enter DMS_1s_cool! .

For Region select (region).

For all other values leave the defaults.

Click Create.

A new connection profile named postgres-vm will appear in the Connections profile list.

Click Check my progress to verify the objective.
Create a connection profile for the PostgreSQL source instance.

Task 3. Create and start a continuous migration job
When you create a new migration job, you first define the source database instance using a previously created connection profile. Then you create a new destination database instance and configure connectivity between the source and destination instances.

In this task, you use the migration job interface to create a new Cloud SQL for PostgreSQL database instance and set it as the destination for the continuous migration job from the PostgreSQL source instance.

Create a new continuous migration job
In this step you will create a new continuous migration job.

In the Google Cloud Console, on the Navigation menu (Navigation menu icon), click Database Migration > Migration jobs.

Click + Create Migration Job.

For Migration job name, enter vm-to-cloudsql.

For Source database engine, select PostgreSQL.

For Destination region, select (region).

For Destination database engine, select Cloud SQL for PostgreSQL.

For Migration job type, select Continuous.

Leave the defaults for the other settings.

Click Save & Continue.
Define the source instance
In this step, you will define the source instance for the migration.

For Source connection profile, select postgres-vm.
Leave the defaults for the other settings.

Click Save & Continue.
After you select the source connection profile, you can see its configuration details, including source hostname or IP address, port, username, and encryption type.

Create the destination instance
In this step, you will create the destination instance for the migration.

For Destination Instance ID, enter postgresql-cloudsql.

For Password, enter supersecret!.

For Choose a Cloud SQL edition, select Enterprise edition.

For Database version, select Cloud SQL for PostgreSQL 13.

In Choose region and zone section, select Single zone and select (zone) as primary zone.

For Instance connectivity, select Private IP and Public IP.

Select Use an automatically allocated IP range.

Leave the defaults for the other settings.

Click Allocate & Connect.
Leave the default option selected to use an automatically allocated IP range.

Note: This step may take a few minutes. If asked to retry the request, click the Retry button to refresh the Service Networking API.

When this step is complete, an updated message notifies you that the instance will use the existing managed service connection.

You will need to edit the pg_hba.conf file on the VM instance to allow access to the IP range that is automatically generated in point 5 of the previous step. You will do this in a later step before testing the migration configuration at the end of this task.

The updated message says that the instance will use the existing managed service connection.

Enter the additional information needed to create the destination instance on Cloud SQL.

For Machine shapes. check 1 vCPU, 3.75 GB

For Storage type, select SSD

For Storage capacity, select 10 GB

Click Create & Continue.

If prompted to confirm, click Create Destination & Continue. A message will state that your destination database instance is being created. Continue to the next step while you wait.

Define the connectivity method
In this step, you will define the connectivity method for the migration.

The message states that the button for configure & continue is enabled when the destination Cloud SQL instance has been created.

For Connectivity method, select VPC peering.

For VPC, select default.

VPC peering is configured by Database Migration Service using the information provided for the VPC network (the default network in this example).

When you see an updated message that the destination instance was created, proceed to the next step.

The updated message informs you that the destination Cloud SQL instance has been created.

Click Configure & Continue.
Allow access to the postgresql-vm instance from automatically allocated IP range
In this step you will edit the pg_hba.conf PostgreSQL configuration file to allow the Database Migration Service to access the stand-alone PostgreSQL database.

Get the allocated IP address range. In the Google Cloud Console on the Navigation menu (Navigation menu icon), right-click VPC network > VPC network peering and open it in a new tab.

Click on the servicenetworking-googleapis-com entry.

In the Imported routes tab, select and copy the Destination IP range (e.g. 10.107.176.0/24).

In the Terminal session on the VM instance, edit the pg_hba.conf file as follows:

sudo nano /etc/postgresql/13/main/pg_hba.conf
Copied!
On the last line of the file:
#GSP918 - allow access to all hosts
host    all all 0.0.0.0/0   md5
replace the "all IP addresses" range (0.0.0.0/0) with the range copied in point 3 above.

#GSP918 - allow access to all hosts
host    all all 10.107.176.0/24   md5
Note: The above step is not required to make the migration work, but it is good practice to make the source database more secure during the migration process, and also restricts access after the migration when the migrated database becomes the source of truth.
Save and exit the nano editor with Ctrl-O, Enter, Ctrl-X

Restart the PostgreSQL service to make the changes take effect. In the VM instance Terminal session:

sudo systemctl start postgresql@13-main
Copied!
Test and start the continuous migration job
In this step, you will test and start the migration job.

In the Database Migration Service tab you open earlier, review the details of the migration job.

Click Test Job.

After a successful test, click Create & Start Job.

Be sure to click on the button for create & start job to ensure that the job is successfully started.

If prompted to confirm, click Create & Start.

Review the status of the continuous migration job
In this step, you will confirm that the continuous migration job is running.

In the Google Cloud Console, on the Navigation menu (Navigation menu icon), click Database Migration > Migration jobs.

Click the migration job vm-to-cloudsql to see the details page.

Review the migration job status.

If you have not started the job, the status will show as Not started. You can choose to start or delete the job.
After the job has started, the status will show as Starting and then transition to Running Full dump in progress to indicate that the initial database dump is in progress.
After the initial database dump has been completed, the status will transition to Running CDC in progress to indicate that continuous migration is active.
When the job status changes to Running CDC in progress, proceed to the next task.

The migration job named vm-to-cloudsql has a status of running CDC in progress.

Note: Continuous migration jobs remain in a running status to ensure that the destination database continues to receive data updates from the source.

A completed status is achieved after the destination database is promoted to be a stand-alone database for reading and writing data which you will see in the final task in the lab.

Click Check my progress to verify the objective.
Create, start, and review a continuous migration job.

Task 4. Confirm the data in Cloud SQL for PostgreSQL
Check the PostgreSQL databases in Cloud SQL
In the Google Cloud Console, on the Navigation menu (Navigation menu icon), click Databases > SQL.

Expand the instance ID called postgresql-cloudsql-master.

Click on the instance postgresql-cloudsql (PostgreSQL read replica).

In the Replica Instance menu, click Databases.

Notice that the databases called postgres, orders and gmemegen_db have been migrated to Cloud SQL.

The migrated databases on Cloud SQL.

Connect to the PostgreSQL instance
In the Replica Instance menu, click Overview.

Scroll down to the Connect to this instance section and click Open Cloud Shell.

The command to connect to PostgreSQL will pre-populate in Cloud Shell:

gcloud sql connect postgresql-cloudsql --user=postgres --quiet
Run the pre-populated command.
If prompted, click Authorize for the API.

When prompted for a password, which you previously set, enter:
supersecret!
Copied!
You have now activated the PostgreSQL interactive console for the destination instance.

Review the data in the Cloud SQL for PostgreSQL instance
To select the database in the PostgreSQL interactive console, run the following command:
\c orders;
Copied!
When prompted for a password, enter:
supersecret!
Copied!
Query the distribution_centers table:
select * from distribution_centers;
Copied!
(Output)

 longitude | latitude |                    name                     | id
-----------+----------+---------------------------------------------+----
 -89.9711  | 35.1174  | Memphis TN                                  |  1
 -87.6847  | 41.8369  | Chicago IL                                  |  2
 -95.3698  | 29.7604  | Houston TX                                  |  3
 -118.25   | 34.05    | Los Angeles CA                              |  4
 -90.0667  | 29.95    | New Orleans LA                              |  5
 -73.7834  | 40.634   | Port Authority of New York/New Jersey NY/NJ |  6
 -75.1667  | 39.95    | Philadelphia PA                             |  7
 -88.0431  | 30.6944  | Mobile AL                                   |  8
 -79.9333  | 32.7833  | Charleston SC                               |  9
 -81.1167  | 32.0167  | Savannah GA                                 | 10
Exit the PostgreSQL interactive console by typing:
\q
Copied!
Update stand-alone source data to test continuous migration
In Cloud Shell, type the following commands to connect to the source PostgreSQL instance:
export VM_NAME=postgresql-vm
export PROJECT_ID=$(gcloud config list --format 'value(core.project)')
export POSTGRESQL_IP=$(gcloud compute instances describe ${VM_NAME} \
  --zone=(zone) --format="value(networkInterfaces[0].accessConfigs[0].natIP)")
echo $POSTGRESQL_IP
psql -h $POSTGRESQL_IP -p 5432 -d orders -U migration_admin
Copied!
Note: The above is an alternative approach to accessing the stand-alone database on the VM instance.
When prompted for a password, enter:
DMS_1s_cool!
Copied!
In psql, enter the following commands:
\c orders;
Copied!
insert into distribution_centers values(-80.1918,25.7617,'Miami FL',11);
Copied!
Close the interactive psql session:
\q
Copied!
Connect to the Cloud SQL PostgreSQL database to check that updated data has been migrated
In Cloud Shell, type the following commands to connect to the destination Cloud SQL PostgreSQL instance:
gcloud sql connect postgresql-cloudsql --user=postgres --quiet
Copied!
When prompted for a password, which you previously set, enter the password for the Cloud SQL instance:
supersecret!
Copied!
You have now activated the PostgreSQL interactive console for the destination instance.

Review data in Cloud SQL for PostgreSQL database
In Cloud Shell, select the active database in the PostgreSQL interactive console:
\c orders;
Copied!
When prompted for a password, which you previously set, enter:
supersecret!
Copied!
Query the distribution_centers table:
select * from distribution_centers;
Copied!
(Output)

 longitude | latitude |                    name                     | id
-----------+----------+---------------------------------------------+----
 -89.9711  | 35.1174  | Memphis TN                                  |  1
 -87.6847  | 41.8369  | Chicago IL                                  |  2
 -95.3698  | 29.7604  | Houston TX                                  |  3
 -118.25   | 34.05    | Los Angeles CA                              |  4
 -90.0667  | 29.95    | New Orleans LA                              |  5
 -73.7834  | 40.634   | Port Authority of New York/New Jersey NY/NJ |  6
 -75.1667  | 39.95    | Philadelphia PA                             |  7
 -88.0431  | 30.6944  | Mobile AL                                   |  8
 -79.9333  | 32.7833  | Charleston SC                               |  9
 -81.1167  | 32.0167  | Savannah GA                                 | 10
 -80.1918  | 25.7617  | Miami FL                                    | 11
Note that the new row added on the stand-alone orders database, is now present on the migrated database.

Exit the PostgreSQL interactive console:
\q
Copied!
Click Check my progress to verify the objective.
Test the continuous migration of data from the source to the destination.

Task 5. Promote Cloud SQL to be a stand-alone instance for reading and writing data
In the Google Cloud Console, on the Navigation menu (Navigation menu icon), click Database Migration > Migration jobs.

Click the migration job name vm-to-cloudsql to see the details page.

Click Promote.

If prompted to confirm, click Promote.

When the promotion is complete, the status of the job will update to Completed.

The migration job named vm-to-cloudsql has a status of complete.

In the Google Cloud Console, on the Navigation menu (Navigation menu icon), click Databases > SQL.
Note that postgresql-cloudsql is now a stand-alone instance for reading and writing data.

The instance named postgresql-cloudsql is labeled as the primary instance.

Click Check my progress to verify the objective.
Promote Cloud SQL for PostgreSQL database to be a stand-alone instance for reading and writing data.

Congratulations!
You have learned how to configure a continuous Database Migration Service job to migrate databases from a PostgreSQL instance to Cloud SQL for PostgreSQL.