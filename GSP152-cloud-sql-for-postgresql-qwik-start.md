# Cloud SQL for PostgreSQL: Qwik Start

## GSP152

### Overview

In this lab you'll learn how to create and connect to a Google Cloud SQL PostgreSQL instance and perform basic SQL operations using the Cloud Console and the psql client.

### Task 1. Create a Cloud SQL instance

Click on the menu icon in the top left of the screen to see the **Navigation menu**.

In the left menu of the Console, click on **SQL**.

Click **Create Instance**.

Click **Choose PostgreSQL**.

Create your instance with the following settings:

- Enter `myinstance` for Instance ID.
- Enter a password for the postgres user. Save or remember this password, you'll need it in the next section.
- For **Choose a Cloud SQL edition**, select **Enterprise**.
- For **Region** select `us-central1`.
- Leave the default values for the other fields.

Click **Create Instance**.

You are returned to the instances list. 

Your new instance is greyed out while it initializes and starts.

After a few minutes your instance is created and you can continue to the next section. 

If it seems to be taking a long time refresh your browser.

### Task 3. Connect to your instance using the psql client in the Cloud Shell

In the Cloud Console, click **Cloud Shell** in the upper right corner.

Then click **Continue** if prompted.

At the Cloud Shell prompt, connect to your Cloud SQL instance by running:

```bash
gcloud sql connect myinstance --user=postgres
```

Enter your postgres password.

> Note: The cursor will not move. Press Enter when you're done typing.

You should now see the psql prompt.

### Task 4. Upload data into the postgres database

Insert sample data into the postgres database:

```sql
CREATE TABLE guestbook (guestName VARCHAR(255), content VARCHAR(255),
                        entryID SERIAL PRIMARY KEY);
INSERT INTO guestbook (guestName, content) values ('first guest', 'I got here!');
INSERT INTO guestbook (guestName, content) values ('second guest', 'Me too!');
```

Retrieve the data:

```sql
SELECT * FROM guestbook;
```

You should now see:

```sql
 guestname   |   content   | entryid
--------------+-------------+---------
 first guest  | I got here! |       1
 second guest | Me too!     |       2
(2 rows)
postgres=>
```

You have created a Google Cloud SQL PostgreSQL instance and connected to it.

### Congratulations
