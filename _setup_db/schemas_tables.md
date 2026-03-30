---
title:  "Defining schemas and tables"
layout: single
sidebar:
  nav: "setup_db"
excerpt: "In the Xspatula framework, schemas and tables are defined as part of the database setup. Only the superuser can create, update or delete schemas and tables."
permalink: /setup_db/schemas_tables/
author_profile: false
date:   2026-03-15 16:13:03 +0200
last_modified_at:   2026-03-29 21:43:13 +0200
---

In the Xspatula framework, schemas and tables are defined as part of the database setup. Only the superuser can create, update or delete schemas and tables. As long as you do not set overwrite or delete to true, no data will be lost. But if you set overwrite to true, existing data in the table you overwrite will be lost.

## Create schema

Defining a schema is simple, you just create a process file, or edit the default JSON process file for creating schema with the path ./setup/zzz/xspatula/setup_db/jsonsql/schema_v10_sql.json:

```
{
  "process": [
    {
      "process_id": "create_schema",
      "parameters": {
        "schema": "observation"
      }
    },
    {
      "process_id": "create_schema",
      "parameters": {
        "schema": "observation_utility"
      }
    },
    {
      "process_id": "create_schema",
      "parameters": {
        "schema": "utility"
      }
    },
    {
      "process_id": "create_schema",
      "parameters": {
        "schema": "process"
      }
    },
    {
      "process_id": "create_schema",
      "parameters": {
        "schema": "community"
      }
    }
  ]
}
```

## Create table

To create a table is more complicated and requires properly structured Standard Query Language (SQL) syntax. The syntax is not evaluated by the framework but is executed as it is given by the user. If there are errors in the syntax the script will crash with a rudimentary feedback on the error.

Framework default process for _create_table_, example schema.table community.user, command objects ending with '_id' or '_code' denote foreign keys from other tables; the hashtag comments are added as explanations and should not be included in production files.
```
{
  "process": [
    {
      "process_id": "create_table",
      "overwrite": false, # added for security so the table is not overwritten by mistake
      "delete": false, # added for security so the table is not deleted by mistake
      "parameters": {
        "schema": "community", # target schema for this table
        "table": "user", # name of table
        "command": [
          "id SERIAL", # automatically incremented value giving all users a unqique id
          "organisation_id INTEGER REFERENCES community.organisation (id)", # the id of the organisation for this user, 0 = no organisation
          "user_name VARCHAR UNIQUE", # unique user name
          "password VARCHAR", # password for logging into the framework for the user
          "stratum_code SMALLINT DEFAULT 1", # stratum_code is the privilege level for the user, 1 = few rights
          "first_name VARCHAR", # first name of user
          "middle_name VARCHAR", # middle name of user
          "last_name VARCHAR", # last (family) name of user
          "email VARCHAR UNIQUE", # email of user (must be unique)
          "email_alt VARCHAR", # alternative email of user
          "address1 VARCHAR", # physical address of user (first line)
          "address2 VARCHAR", # physical address of user (second line)
          "postal_address VARCHAR", # postal address of user
          "postal_zip_code VARCHAR", # postal zip code of user
          "state VARCHAR", # state the user is associated with
          "territory_id INTEGER REFERENCES utility.territory (id)", # id of territory, where territory must be defined in another table if used
          "telephone VARCHAR", # telephone number of user
          "department VARCHAR", # department the user
          "section VARCHAR",
          "position VARCHAR",
          "create_timestamp TIMESTAMP DEFAULT CURRENT_TIMESTAMP(1)", # automatic timestamp of registering in the database
          "last_update_timestamp TIMESTAMP DEFAULT CURRENT_TIMESTAMP(1)", # automatic timestamp of last activity
          "status_code INTEGER DEFAULT 10", # status_codes include active, passive, dormant, ended etc
          "PRIMARY KEY (user_name)"
        ]
      }
    }  
  ]
}
```

## Table insert

While the intention is to build processes that interact with the database, inserting, updating and deleting records, there is a bypass function available when setting up the database to directly insert values in the database. Among other things, this allows defining default users for the database at setup, eliminating the need for using the superuser to login for creating the first users. Thus the default processes defined for setting up the database include the creation of some default users:
- a user for handling other users,
- a user with stratum 5 privileges to access all data, and
- Jane Doe (the example user in the documentation) as a stratum 1 (restricted) user.

None of these users are linked to any organisation ("organisation_id": 0).

You can use the suggested users to test setting up the database. Then delete the whole database and define your own users and recreate the database.

```
{
  "process": [
    {
      "process_id": "table_insert",
      "overwrite": false,
      "delete": false,
      "parameters": {
        "schema": "community",
        "table": "user",
        "command": {
          "columns": [
            "organisation_id",
            "email",
            "email_alt",
            "password",
            "first_name",
            "last_name",
            "user_name",
            "territory_id",
            "stratum_code"
          ],
          "values": [
            [
              "0",
              "user_manager_email@example.com",
              "user_manager_email_alt@example.com",
              "cuttingedge-railway-precipitation-cumbersome",
              "user",
              "manager",
              "user_manager",
              "0",
              "6"
            ],
            [
              "0",
              "cat_5_email@example.com",
              "cat_5_email_alt@example.com",
              "armchair-scouting-windowframe-bicycling",
              "ini_cat_5_user",
              "Ini",
              "Cat",
              "0",
              "5"
            ],
            [
              "0",
              "jane_doe@example.com",
              "jane_doe_alt@example.com",
              "hello-xspatula",
              "Jane",
              "Doe",
              "jane_doe",
              "0",
              "1"
            ]
          ]
        }
      }
    }
  ]
}
```

## Default processes

The most important insert process is the adding of the root and sub processes that define the structure for adding any other process. All definitions related to defining processes are in the schema _process_ holding the following tables:

- root_process (for grouping sub_processes)
- sub_process (actual processes linked to framework script functions)
- process_parameter (parameters expected and required for running a process)
- process_parameter_set_value (if a text parameter can only take a on a set of distinct values)
- process_parameter_minmax (if a numerical parameter can only take on a limited range)
- process_parameter_schema_table (the target schema.table for a parameter to be written to the database)
- process_parameter_permission (if the parameter is allowed to be updated or deleted)
- process_parameter_default (if a parameter can be automatically selected from another schema.table.record)  

The handling of processes is fairly complex. If you are in for a deeper understanding please have a look in the JSON process files setting up and inserting the data for defining processes:

```
./setup/zzz/xspatula/setup_db/jsonsql/processes_v10_sql.json
./setup/zzz/xspatula/setup_db/jsonsql/processes_records_v10_sql.json
```

[vscode]: https://code.visualstudio.com

[postgres]: ../postgres/

[anaconda]: ../anaconda/

[netrc]: ../netrc/

[download_anaconda]: https://www.anaconda.com/download

[install_anaconda]: https://www.anaconda.com/docs/getting-started/anaconda/install/overview
