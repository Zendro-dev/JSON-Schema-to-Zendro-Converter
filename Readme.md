# JSON-Schema to Zendro Converter

[JSON-Schema](https://json-schema.org/) is a declarative language for defining the structure, constraints, and validation rules of JSON data. It allows developers to enforce data integrity, document APIs, and enable automated validation of JSON objects. Common use cases include API request validation, configuration file validation, and ensuring data consistency in distributed systems. JSON Schema supports data types, required fields, pattern matching, and complex structures like nested objects and arrays.

[Zendro](https://zendro-dev.github.io/) is a software tool (code generator) to quickly create a data warehouse tailored to your specifications. You tell Zendro what the structure of your data is, in the form of models, and where the data is or shall be stored.

The **JSON-Schema to Zendro Converter** automatically transforms data-models schema defined in JSON-Schema into Zendro definition files (Zendro code generator input files). This allows users to leverage JSON-Schema to seamlessly generate a fully functional GraphQL server with complete [CRUD](https://en.wikipedia.org/wiki/Create,_read,_update_and_delete) support, distributed (cloud) deployment capabilities, full [OAuth2-compliant](https://oauth.net/2/) authentication and authorization, and an optional administrative graphical user interface.  

**Notes:**

- The converter applies certain expectations and minor [restrictions](https://github.com/Zendro-dev/JSON-Schema-to-Zendro-Converter?tab=readme-ov-file#json-schema-requirements) to the JSON-Schema input to ensure a successful transformation into Zendro data model definitions.
- The converter was originally developed for and tested on the full [**Breeding-API (BrApi) JSON-Schema (v2.1)**](https://brapi.org/) definitions. The **Breeding API** provides a standardized interface for managing, analyzing, and sharing breeding data, including genotypic and phenotypic information.   

---

## Installation

Install the project with git

```bash
git clone https://github.com/Zendro-dev/JSON-Schema-to-Zendro-Converter.git
```
The Python scripts are located in the `methods` subfolder.

---

 
## Usage/Examples

#### General Usage:
```bash
python methods/convert.py -i [input-path] -o [output-path] [other command-line arguments]
```

#### Command-Line Arguments

The `Converter` script converts JSON schemas into Zendro data models. Below are the available arguments:

### Required Arguments

- `-i, --input-path`  
  **Description:** Path to the JSON schemas.  
  **Required:** Yes  

- `-o, --output-path`  
  **Description:** Path where the Zendro data models should be stored.  
  **Required:** Yes  

### Optional Arguments

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

---

#### JSON Schema Requirements

The schema must meet certain characteristics for the converter to function properly. Below is an example schema to help adapt your models to the required input format:  

1. The schema must include the `$defs` key, where the entire model is defined.  
2. Inside `$defs`, the model name should be defined, and within the model name, there must be a key called `properties`.  
3. Inside `properties`, the "columns" of the model should be defined. It is not necessary to define an `id` as it can be specified using the `-p` and `-t` arguments of the converter. The data type must be specified, and ideally, a description should also be included so it can be used in the GraphQL schema in Zendro.  
4. Associations must include:  
   - **title**: Name of the association  
   - **description**: Description of the association  
   - **referencedAttribute**: Name of the association in the related model  
   - **relationshipType**: Type of relationship  
     - one-to-many  
     - many-to-many  
     - one-to-one  
     - many-to-one  
   - **items**:  
     - **$ref**: File where the related model is located  
     - **description**  
   - **type**:  
     - Default value  
     - Data type  

```
{
    "$defs": {
        "<model_name>": {
            "properties": {
                "<model_id>": {
                    "description": "",
                    "type": ""
                },
                "<property1>": {
                    "description": "",
                    "type": [
                        "null",
                        "<data_type>"
                    ]
                },
                "property2": {
                    "description": "",
                    "type": [
                        "null",
                        "<data_type>"
                    ]
                },
                "<model_association_1>": {
                    "title": "<association_name>",
                    "description": "",
                    "referencedAttribute": "<model_name>",
                    "relationshipType": "<one-to-many|many_to_many|one_to_one|many_to_one>",
                    "items": {
                        "$ref": "<associated_model_location>",
                        "description": ""
                    },
                    "type": [
                        "null",
                        "<data_type>"
                    ]
                }
            }
        }
    }
}
```
---

#### Regular example:
```bash
python convert.py -i "../BrAPI-Schema" -o "../results"
```

JSON Schema input:

```json
{
    "$defs": {
        "Season": {
            "properties": {
                "seasonDbId": {
                    "description": "The ID which uniquely identifies a season. For backward compatibility it can be a string like '2012', '1957-2004'",
                    "type": "string"
                },
                "seasonName": {
                    "description": "Name of the season. ex. 'Spring', 'Q2', 'Season A', etc.",
                    "type": [
                        "null",
                        "string"
                    ]
                },
                "year": {
                    "description": "The 4 digit year of the season.",
                    "type": [
                        "null",
                        "integer"
                    ]
                },
                "observations": {
                    "title": "observations",
                    "description": "Observations associated to this season",
                    "referencedAttribute": "season",
                    "relationshipType": "one-to-many",
                    "items": {
                        "$ref": "Observation.json#/$defs/Observation",
                        "description": "Observation"
                    },
                    "type": [
                        "null",
                        "array"
                    ]
                }
            },
            "required": [
                "seasonDbId"
            ],
            "title": "Season",
            "type": "object"
        }
    },
    "$id": "https://brapi.org/Specification/BrAPI-Schema/BrAPI-Core/Season.json",
    "$schema": "http://json-schema.org/draft/2020-12/schema"
}
```

Zendro compatible output:
```json
{
    "model": "season",
    "storageType": "sql",
    "attributes": {
        "seasonDbId": {
            "type": "[ String ]",
            "description": "The ID which uniquely identifies a season. For backward compatibility it can be a string like '2012', '1957-2004'"
        },
        "seasonName": {
            "type": "String",
            "description": "Name of the season. ex. 'Spring', 'Q2', 'Season A', etc."
        },
        "year": {
            "type": "Int",
            "description": "The 4 digit year of the season."
        },
        "observations_IDs": "[ String ]"
    },
    "associations": {
        "observations": {
            "type": "one_to_many",
            "implementation": "foreignkeys",
            "reverseAssociation": "season",
            "target": "observation",
            "targetKey": "season_ID",
            "sourceKey": "observations_IDs",
            "keysIn": "season",
            "targetStorageType": "sql"
        }
    },
    "internalId": "seasonDbId"
}
```

---

#### Custom primary key and type:
```bash
python convert.py -i "../BrAPI-Schema" -o "../results" -p "github" -t "Int"
```

Zendro compatible output:
```json
{
    "model": "season",
    "storageType": "sql",
    "attributes": {
        "github": {
            "type": "[ Int ]",
            "description": "The ID which uniquely identifies a season. "
        },
        "seasonName": {
            "type": "String",
            "description": "Name of the season. ex. 'Spring', 'Q2', 'Season A', etc."
        },
        "year": {
            "type": "Int",
            "description": "The 4 digit year of the season."
        },
        "observations_IDs": "[ String ]"
    },
    "associations": {
        "observations": {
            "type": "one_to_many",
            "implementation": "foreignkeys",
            "reverseAssociation": "season",
            "target": "observation",
            "targetKey": "season_ID",
            "sourceKey": "observations_IDs",
            "keysIn": "season",
            "targetStorageType": "sql"
        }
    },
    "internalId": "github"
}
```
As you can see, the primary key (here `github`) is the custom primary key name `github` and the data type is Integer.
Each generated data model contains the custom primary key name and is of the specified type.

---

#### Mapping of models to specific databases:

To use different storage types for specific models, list them in the `-d` argument as follows:

```bash
python methods/converter.py -i brapi_input_example/ -o results_distributed/ -d "mongodb=trial,trait;cassandra=study"
```

This command will generate models with `trial` and `trait` using `MongoDB` as the storage type, `study` with `Cassandra`, and all other models using `SQL` (default for the `-s` argument). 

You can use the `-s` argument to specify a different general storage type:

```bash
python methods/converter.py -i brapi_input_example/ -o results_distributed/ -s mongodb -d "neo4j=trial,trait;cassandra=study"
```

This command will generate models with `trial` and `trait` using `neo4j` as the storage type, `study` with `Cassandra`, and all other models using `mongodb` as specified in `-s` argument.

---

#### Associations

All associations/relationships are defined after Zendro's [paired-end foreign keys](https://zendro-dev.github.io/setup_root/data_models#paired-end-foreign-keys).

---

## API Reference

#### Packages used
- `os`
- `re`
- `json`
- `sys`
- `datetime`, `date` from `datetime`

---

#### get_files
Get all json files:

```python
get_files(input_path)
```

| Parameter | Type     | Description                |
| :-------- | :------- | :------------------------- |
| `input_path` | `string` | Path to the input files/directories |

Walks through the input path, searches for json files and extracts the relative path to the json file for the output path.

Function returns all found files in the input hierachy.

---

#### get_models
Get all models:

```python
get_models(input_files)
```

| Parameter | Type     | Description                |
| :-------- | :------- | :------------------------- |
| `input_files` | `list` | List of files to extract models from |

Read in the given files and extract all models from it.\n
Get a formatted dictionary of data models included in those files.

Function returns the formatted dictionary of data models

---

#### get_properties
Get all items/properties:
```python
get_properties(input_model_properties, current_model)
```

| Parameter | Type     | Description                       |
| :-------- | :------- | :-------------------------------- |
| `input_model_properties`      | `dictionary` | Dictionary of all properties included in the current model |
| `current_model`      | `string` | The name of the current model |

Get a formatted dictionary of data models with Zendro compatible data types and references.\n
Ready to be writen in json format.

Function returns a formatted dictionary of data models compatible to Zendro.

---

#### get_property_type
Check for Zendro compatible types:
```python
get_property_type(input_property)
```

| Parameter | Type     | Description                       |
| :-------- | :------- | :-------------------------------- |
| `input_property`      | `dictionary` | An item from a json file |

Function returns a Zendro compatible type or none

---

#### write_json
Writes the passed models to their own json file:

```python
write_json(output_models)
```

| Parameter | Type     | Description                       |
| :-------- | :------- | :-------------------------------- |
| `output_models`      | `dictionary` | Dictionary with model definitions compatible to Zendro |


This function doesn't return anything.

---

#### log
Log a message:
```python
log(msg)
```
| Parameter | Type     | Description                       |
| :-------- | :------- | :-------------------------------- |
| `msg`      | `String` | Message to log |

Writes the message to a log-file. and logs the date and time automatically.

This function doesn't return anything but will give out a text to your console.

---

## Support

In the current version, the converter does not support:
- Arrays of any kind, especially also not objects (the only exception are the associations)
- In the models generated by Zendro it is possible to assign the status `required` to attributes.
This addition cannot be made before Zendro generates the data models, so it is not possible to give this status to individual attributes using the converter.
