---
# metadata # 
title: Egress To An SQL Database
description: Learn how to use data egress to end data to an SQL database. 
date: 
# taxonomy #
tags: ["sql", "data-operations", "export"]
series:
seriesPart:
directory: true
---

{{% notice warning %}}
SQL Egress is an [experimental feature](/{{%release%}}/manage/supported-releases/#experimental).
{{% /notice %}}

{{% productName %}} already implements [egress to object storage](/{{%release%}}/export-data/export-data-egress) as an optional egress field in the pipeline specification. 
Similarly, our **SQL egress** lets you seamlessly export data from a {{% productName %}}-powered pipeline output repo to an SQL database.

Specifically, we help you connect to a remote database and push the content of CSV files to **interface tables**, matching their column names and casting their content into their respective SQL datatype. 

Interface tables are intermediate tables used for staging the data being egressed from {{% productName %}} to your data warehouse.
They are the tables your SQL Egress pipeline inserts its data into and should be **dedicated tables**. The content of your interface tables matches the content of the latest output commit of your pipeline. 

{{% notice note %}}
 Best Practice.

A new output commit will trigger a **delete of all data in the interface tables** before inserting more recent values. As a best practice, we strongly recommend to **create a separate database** for {{% productName %}} Egress. 
{{% /notice %}}

As of today, we support the following drivers:

- postgres and postgresql : connect to Postgresql (or compatible databases such as Redshift).
- mysql : connect to MySQL (or compatible databases such as MariaDB).
- snowflake : connect to Snowflake.
## Use SQL Egress

To egress data from the output commit of a pipeline to an SQL database, you will need to:

 1. *On your cluster* 

    [Create a **secret**](#1-create-a-secret) containing your database password. 

 1. *In the Specification file of your egress pipeline*

    Reference your secret by providing its name, provide the [**connection string**](#2-update-your-pipeline-spec) to the database and choose the **format of the files (CSV for now - we are planning on adding JSON soon)** containing the data to insert.

 1. *In your user code*

    Write your data into CSV files placed in [root directories named after the table](#3-in-your-user-code-write-your-data-to-directories-named-after-each-table) you want to insert them into. 
    You can have multiple directories.

### 1. Create a Secret 

Create a **secret** containing your database password in the field `PACHYDERM_SQL_PASSWORD`. This secret is identical to the database secret of {{% productName %}} SQL Ingest. Refer to the SQL Ingest page for instructions on [how to create your secret](/{{%release%}}/manage/secrets).

### 2. Update your Pipeline Spec

Append an egress section to your pipeline specification file, then fill in:

- the `url`: the connection string to your database.
- the `fileFormat` type: CSV for now.
- the `name`: the Kubernetes secret name.
- the `columns`: Optional array for egress of **CSV files with headers only**. The order of the columns in this array must match the order of the schema columns; however, the CSV columns can be any order. So if the array is ["foo", "bar"] and the CSV file is:

    ```s
    bar,foo
    1,"string"
    2,"text!"
    ```
    The following table will be written to the database:

    ```s
    foo   |  bar
    ===============
    string | 1
    text!  | 2
    ```

#### Example

```json
{
"pipeline": {
    "name": "egress"
},
"input": {
    "pfs": {
        "repo": "input_repo",
        "glob": "/",
        "name": "in"
    }
},
"transform": {
   ...
},
"egress": {
    "sqlDatabase": {
        "url": "snowflake://pachyderm@WHMUWUD-CJ80657/PACH_DB/PUBLIC?warehouse=COMPUTE_WH",
        "fileFormat": {
            "type": "CSV",
            "columns": ["foo", "bar"]
        },
        "secret": {
            "name": "snowflakesecret",
            "key": "PACHYDERM_SQL_PASSWORD"
        }
    }
}
}
```

### 3. In your User Code, Write Your Data to Directories Named After Each Table
 
The user code of your pipeline determines what data should be egressed and to which tables. 
Data (in the form of CSV files) that the pipeline writes to the output repo is interpreted as tables corresponding to directories. 

**Each top-level directory is named after the table you want to egress its content to**. All of the files reachable in the walk of each root directory are parsed in the given format indicated in the egress section of the pipeline specification file (CSV for now), then inserted in their corresponding table. Find more information on how to format your CSV file depending on your targeted SQL Data Type in our [SQL Ingest Formatting section](/{{%release%}}/prepare-data/sql-ingest#formats).

{{% notice warning %}}
 - All interface tables must pre-exist before an insertion.
 - Files in the root produce an error as they do not correspond to a table.
 - The directory structure below the top level does not matter.  The first directory in the path is the table; everything else is walked until a file is found.  All the data in those files is inserted into the table.
 - The order of the values in each line of a CSV must match the order of the columns in the schema of your interface table unless you were using headers AND specified the `"columns": ["foo", "bar"],` field in your pipeline specification file.
{{% /notice %}}
   
#### Example 
```s
"1","Tim","2017-03-12T21:51:45Z","true"
"12","Tom","2017-07-25T21:51:45Z","true"
"33","Tam","2017-01-01T21:51:45Z","false"
"54","Pach","2017-05-15T21:51:45Z","true"
```

{{% notice note %}}
- {{% productName %}} queries the schema of the interface tables before insertion then parses the data into their SQL data types.    
- Each insertion creates a new row in your table.
{{% /notice %}}

## Troubleshooting

You have a pipeline running but do not see any update in your database? 

Check your logs:

```s
pachctl list pipeline
pachctl logs -p <your-pipeline-name> --master
```


  

