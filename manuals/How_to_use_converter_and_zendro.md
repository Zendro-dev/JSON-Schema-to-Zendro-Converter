# How to setup Zendro using the supported databases

### 1. Install Zendro
- Verify that you have the [project requirements](https://zendro-dev.github.io/quickstart.html#project-requirements).
- Follow the [instructions](https://zendro-dev.github.io/quickstart.html#step-1-installation) to install Zendro.

### 2. Install JSON Schema to Zendro Converter
- Follow the [instructions](https://github.com/Zendro-dev/JSON-Schema-to-Zendro-Converter?tab=readme-ov-file#installation) to install the JSON-Schema to Zendro Converter.

### 3. Use the converter in your JSON Schemas
Before using the converter on your schemas, please ensure that your models meet the necessary requirements for the converter to work. Check the details [here](https://github.com/Zendro-dev/JSON-Schema-to-Zendro-Converter?tab=readme-ov-file#json-schema-requirements).

-  General Usage:
    ```bash
    python methods/converter.py -i [input-path] -o [output-path] [other command-line arguments]
    ```

- Command-Line Arguments

    The `converter` script converts JSON schemas into Zendro data models. Below are the available arguments:

    #### Required Arguments

    - `-i, --input-path`  
    **Description:** Path to the JSON schemas.  

    - `-o, --output-path`  
    **Description:** Path where the Zendro data models should be stored.  

    #### Optional Arguments

    - `-s, --storage-type`  
    **Description:** Type of storage where the model is stored.  
    **Choices:**  
        - `sql` (default)  
        - `generic`  
        - `zendro-server`  
        - `cassandra`  
        - `mongodb`  
        - `neo4j`  
        - `presto/trino`  
        - `amazon-s3`  
        - `distributed-data-model`  
        - `adapter`  

    - `-p, --primary-key-name`  
    **Description:** Name of the primary key.  

    - `-t, --primary-key-type`  
    **Description:** Type of the primary key.  
    **Choices:**  
        - `Int`  
        - `String` (default)  

    - `-d, --database-mapping`  
    **Description:** Mapping of models to specific databases.  
    **Format:** `"sql=model_1,model_2;mongodb=model_3"`  
    **Note:** Models not listed here will use the storage type set by `-s`.  

- For example, to convert BrAPI models you can execute:
    ```
    python methods/converter.py -i brapi_input_example/ -o results_bd/
    ```
    This command will generate sql models.

    To use different storage types for specific models, list them in the `-d` argument as follows:

    ```
    python methods/converter.py -i brapi_input_example/ -o results_distributed/ -d "mongodb=trial,trait;cassandra=study"
    ```

    This command will generate models with `trial` and `trait` using `MongoDB` as the storage type, `study` with `Cassandra`, and all other models using `SQL` (default for the `-s` argument). You can use the `-s` argument to specify a different general storage type.

### 4. Create Zendro instance

- Once you have your Zendro data models, you can set up Zendro. Go out from the previusly created zendro directory and create a new Zendro instance

    ```
    zendro new -d <project_name>
    ```
- If you want to use the Graphical User Interface edit the following config files to add your expected secret word in the `NEXTAUTH_SECRET` variable.
    - GraphiQL in development mode: `./graphiql-auth/.env.development `
    - GraphiQL in production mode: `./graphiql-auth/.env.production `
    - SPA in development mode: `./single-page-app/.env.development `
    - SPA in production mode: `./single-page-app/.env.production `

    If not, please edit only the GraphiQL files and comment the `zendro-spa` service in `./docker-compose-dev.yml` and `./docker-compose-prod.yml`.

    _Note: If you wish to know more about enviroment variables you can check [this](https://zendro-dev.github.io/setup_root/env_var)._

### 5. Add models 
Add your previusly converted models to `./data_model_definitions` folder.
### 6. Configure the database

You can find the database configuration example in `./config/data_models_storage_config_example.json`

Depending on the database you want to work with, you can copy the configuration from the example file and replace it in `./config/data_models_storage_config.json`. Additionally, you need to add the service to the Docker Compose configuration.

You can find the service configurations example in `./docker-compose-dev-example.yml`, and you should add them to the development and production Docker Compose files as needed.

For example, if we want to work with MongoDB, the `data_models_storage_config.json` file should contain:

```
{
    "default-mongodb": {
    "storageType": "mongodb",
    "username": "sciencedb",
    "password": "sciencedb",
    "database": "sciencedb_development",
    "host": "zendro-mongodb",
    "port": "27017"
    }
}
```

Whereas the docker-compose-prod.yml file should include the services (as applicable):

* zendro-keycloak-postgres
* zendro-keycloak
* zendro-mongodb
* zendro-graphql-server
* zendro-spa
* zendro-graphiql

_Note: If you are going to work with Cassandra, please keep in mind [these limitations](https://zendro-dev.github.io/setup_root/data_models/cassandra#cassandra-storage-type)._

### 7. Generate migrations

After setting up your data models use the next command to generate the model-specific code and fill your Zendro skeleton project with life.

Inside the new project execute:
```
zendro generate -m
```
This will automatically generate all basic create, read, update and delete (CRUD) operations for each data model specified in the scheme from the previous step.

Also, this will create migration files. By default, every migration file has two functions, namely up and down. The up function creates a table, the down function deletes the existing table. Additionally, up and down functions would add and drop indices for primary keys. Furthermore it is possible to customize the migration functions. 

Please check [this](https://zendro-dev.github.io/zendro_cli.html) to learn more about migrations.

In case you want to connect Zendro to an already existing database, please check [this](https://zendro-dev.github.io/setup_root.html#step-6-optional-define-your-database-connection) for details. 

### 8. Start Zendro

Execute this command to start Zendro in production mode or without `-p` to start in development mode.
```
$ zendro dockerize -u -p
```
This command will create docker containers for each Zendro component:

* Keycloak: manage users and roles
* API: CRUD API that can be accessed through a GraphQL query language
* GraphiQL interface: An implementation of the GraphQL IDE with Zendro login and advanced filter functionalities.
* Single Page App (SPA): graphical interface to send CRUD requests to a Zendro GraphQL endpoint (if applicable).

We recommend to check [this guide](https://zendro-dev.github.io/setup_root.html#getting-started) to fully understand the Zendro configuration and ensure you don’t miss any details.

### 9. Find migration to setup your database 

In the `./graphql-server/migrations/` folder, you'll find the migration files that sets up each model. If you choose not to use Zendro, you can still use the migrations to generate the complete GraphQL schema and set up the database.

To generate and execute a specific migration, ensure that your database is accessible. You can do this starting Zendro only with the database service. Modify `./docker-compose-dev.yml` or `./docker-compose-prod.yml` as needed and start Zendro.

Then, navigate to the `./graphql-server/` directory and execute:

```
npm install
node utils/run_migrations.js <path_to_migration_model> up
```

### 10. Find migration to generate the complete GraphQL schema

There is a migration in the `migrations` folder named `2025-02-23T17_37_17.804Z#schema_to_text_file.js`, which generates a GraphQL file with the complete schema created by Zendro. You can execute it in the same way as the previous migrations:

```
node utils/run_migrations.js migrations/2025-02-23T17_37_17.804Z#schema_to_text_file.js up
```

This migration will generate a file named `schema.graphql` in the `graphql-server/` directory.

In [zendro_schema_output.graphql](https://github.com/Zendro-dev/Zendro-Converter/blob/main/manuals/zendro_schema_output.graphql), you will find the example file that Zendro will generate when you run the migration as indicated.

## Technologies that do not come with a schema creation and can be used just with the configs

### 1. Document-based databases (MongoDB)

Document-based databases like **MongoDB** are a type of **NoSQL** database that store information in documents rather than tables and rows like relational databases. These documents are usually stored in JSON or BSON format (a binary variant of JSON).

MongoDB is **schema-less**, meaning you don’t need to define a rigid database schema beforehand. Documents within a collection can have different fields and data types, providing great flexibility to store unstructured or semi-structured data.


### 2. Data Lakes (Presto, Trino)

**Data Lakes** are large repositories that allow storing data in its **raw** form without requiring processing or transformation beforehand. The data can be structured, unstructured, or semi-structured.

**Presto** and **Trino** are query engines that allow you to run SQL queries over these **Data Lakes**. They can work over multiple data sources, including databases, cloud storage, CSV files, and more. Data Lakes and query engines like Presto and Trino do not require the data to be organized under a rigid schema.

Data Lakes allow storing data as-is, without the need for prior transformation. You do not need to define a schema to store the data since the goal is to accumulate large volumes of data that will only be processed or structured when necessary, through queries.

### 3. Comma-separated value (CSV) tables in cloud storage (Amazon S3)

**CSV files** are one of the simplest forms of storing data in text format, where values are separated by commas (or other delimiters like tabs). **Amazon S3** is a cloud storage service that allows storing these CSV files and other types of data at scale.

CSV files are simply text files and do not require a schema. Each line of the file represents a data row, and the values within each row are separated by commas or other delimiters. The data doesn't have a predefined structure, so you can add or remove columns at any time without needing to update a schema.

---

### Usage with Only Configurations

Since these technologies do not require schema creation, **Zendro** will not generate schemas for these storage types. Instead, they can be used directly with configuration files.

In all these cases, Zendro will rely on the provided configuration files to manage and interact with the data without generating a predefined schema.


## Mix different databases for different data models

- Once you have the compatible models generated by the converter for different storage types as [explained earlier](https://github.com/Zendro-dev/JSON-Schema-to-Zendro-Converter/blob/main/manuals/How_to_use_converter_and_zendro.md#3-use-the-converter-in-your-json-schemas), follow steps 4 and 5 of the manual:

    [4. Create Zendro instance](https://github.com/Zendro-dev/JSON-Schema-to-Zendro-Converter/blob/main/manuals/How_to_use_converter_and_zendro.md#4-create-zendro-instance)
    
    [5. Add models](https://github.com/Zendro-dev/JSON-Schema-to-Zendro-Converter/blob/main/manuals/How_to_use_converter_and_zendro.md#5-add-models)


In step 6, you will add all the different databases you are using to your configuration. In the case of the example, we need to configure MongoDB, Cassandra, and SQL, so the file `./config/data_models_storage_config.json` will contain:

```
{
  "default-sql": {
      "storageType": "sql",
      "username": "zendro",
      "password": "zendro",
      "database": "zendro_development",
      "host": "zendro-postgres",
      "dialect": "postgres"
    },
    "default-mongodb": {
      "storageType": "mongodb",
      "username": "sciencedb",
      "password": "sciencedb",
      "database": "sciencedb_development",
      "host": "zendro-mongodb",
      "port": "27017"
    },
    "default-cassandra": {
      "storageType": "cassandra",
      "username": "cassandra",
      "password": "cassandra",
      "keyspace": "sciencedb",
      "host": "zendro-cassandra",
      "port": "9042"
    }
}
```

While the docker compose file will contain the following services. Remember that the configuration can be found in the file `./docker-compose-dev-example.yml`:

- zendro-keycloak-postgres
- zendro-keycloak
- zendro-postgres
- zendro-mongodb
- zendro-cassandra
- zendro-graphql-server
- zendro-spa
- zendro-graphiql

Then you can proceed with the following steps of the manual.

