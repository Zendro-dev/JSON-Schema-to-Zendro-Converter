# JSON-Schema to Zendro Converter

[JSON-Schema](https://json-schema.org/) is a declarative language for defining the structure, constraints, and validation rules of JSON data. It allows developers to enforce data integrity, document APIs, and enable automated validation of JSON objects. Common use cases include API request validation, configuration file validation, and ensuring data consistency in distributed systems. JSON Schema supports data types, required fields, pattern matching, and complex structures like nested objects and arrays.

[Zendro](https://zendro-dev.github.io/) is a software tool (code generator) to quickly create a data warehouse tailored to your specifications. You tell Zendro what the structure of your data is, in the form of models, and where the data is or shall be stored.

The **JSON-Schema to Zendro Converter** automatically transforms data-models schema defined in JSON-Schema into Zendro definition files (Zendro code generator input files). This allows users to leverage JSON-Schema to seamlessly generate a fully functional GraphQL server with complete [CRUD](https://en.wikipedia.org/wiki/Create,_read,_update_and_delete) support, distributed (cloud) deployment capabilities, full [OAuth2-compliant](https://oauth.net/2/) authentication and authorization, and an optional administrative graphical user interface.  

**Notes:**

- The converter applies certain expectations and minor restrictions to the JSON-Schema input to ensure a successful transformation into Zendro data model definitions.
- The converter was originally developed for and tested on the full [**Breeding-API (BrApi) JSON-Schema (v2.1)**](https://brapi.org/) definitions. The **Breeding API** provides a standardized interface for managing, analyzing, and sharing breeding data, including genotypic and phenotypic information.   

Chapters:
- [Installation](#installation)
- [Usage/Examples](#usageexamples)
  - [General Usage](#general-usage)
  - [Regular example](#regular-example)
  - [Custom primary key and type](#custom-primary-key-and-type)
  - [Associations](#associations)
- [API Reference](#api-reference)
  - [Packages used](#packages-used)
  - [get_files](#get_files)
  - [get_models](#get_models)
  - [get_properties](#get_properties)
  - [get_property_type](#get_property_type)
  - [write_json](#write_json)
  - [log](#log)
- [Support](#support)

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

#### Regular example:
```bash
python convert.py -i "../BrAPI-Schema" -o "../results"
```

JSON Schema input:

```json
{
    "$defs": {
        "Study": {
            "properties": {
                "active": {
                    "description": "A flag to indicate if a Study is currently active and ongoing",
                    "type": [
                        "null",
                        "boolean"
                    ]
                },
                "additionalInfo": {
                    "additionalProperties": {
                        "type": "string"
                    },
                    "description": "A free space containing any additional information related to a particular object. A data source may provide any JSON object, unrestricted by the BrAPI specification.",
                    "type": [
                        "null",
                        "object"
                    ]
                },
                "commonCropName": {
                    "description": "Common name for the crop associated with this study",
                    "type": [
                        "null",
                        "string"
                    ]
                },
                "contacts": {
                    "description": "List of contact entities associated with this study",
                    "items": {
                        "properties": {
                            "contactDbId": {
                                "description": "The ID which uniquely identifies this contact\n\nMIAPPE V1.1 (DM-33) Person ID - An identifier for the data submitter. If that submitter is an individual, ORCID identifiers are recommended.",
                                "type": "string"
                            },
                            "email": {
                                "description": "The contacts email address\n\nMIAPPE V1.1 (DM-32) Person email - The electronic mail address of the person.",
                                "type": [
                                    "null",
                                    "string"
                                ]
                            },
                            "instituteName": {
                                "description": "The name of the institution which this contact is part of\n\nMIAPPE V1.1 (DM-35) Person affiliation - The institution the person belongs to",
                                "type": [
                                    "null",
                                    "string"
                                ]
                            },
                            "name": {
                                "description": "The full name of this contact person\n\nMIAPPE V1.1 (DM-31) Person name - The name of the person (either full name or as used in scientific publications)",
                                "type": [
                                    "null",
                                    "string"
                                ]
                            },
                            "orcid": {
                                "description": "The Open Researcher and Contributor ID for this contact person (orcid.org)\n\nMIAPPE V1.1 (DM-33) Person ID - An identifier for the data submitter. If that submitter is an individual, ORCID identifiers are recommended.",
                                "type": [
                                    "null",
                                    "string"
                                ]
                            },
                            "type": {
                                "description": "The type of person this contact represents (ex: Coordinator, Scientist, PI, etc.)\n\nMIAPPE V1.1 (DM-34) Person role - Type of contribution of the person to the investigation",
                                "type": [
                                    "null",
                                    "string"
                                ]
                            }
                        },
                        "required": [
                            "contactDbId"
                        ],
                        "type": "object"
                    },
                    "type": [
                        "null",
                        "array"
                    ]
                }
            }
        }
    },
    "$id": "https://brapi.org/Specification/BrAPI-Schema/BrAPI-Core/Study.json",
    "$schema": "http://json-schema.org/draft/2020-12/schema"
}
```

Zendro compatible output:
```json
{
    "model": "Study",
    "storageType": "sql",
    "attributes": {
        "study_ID": "String",
        "active": {
            "type": "Boolean",
            "description": "A flag to indicate if a Study is currently active and ongoing"
        },
        "commonCropName": {
            "type": "String",
            "description": "Common name for the crop associated with this study"
        }
    }
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
    "model": "Study",
    "storageType": "sql",
    "attributes": {
        "study_github_ID": "Int",
        "active": {
            "type": "Boolean",
            "description": "A flag to indicate if a Study is currently active and ongoing"
        },
        "commonCropName": {
            "type": "String",
            "description": "Common name for the crop associated with this study"
        }
    }
}
```
As you can see, the primary key (here `study_github_ID`) contains the custom primary key name `github` and the data type is Integer.
Each generated data model contains the custom primary key name and is of the specified type.

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
