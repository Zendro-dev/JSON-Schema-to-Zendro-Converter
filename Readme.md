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
python methods/converter.py -i [input-path] -o [output-path] [other command-line arguments]
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
1. **Flattened Structure**: Since Zendro does not support deeply nested objects, all complex structures should be flattened, with associations maintaining the relationships between entities.
1. **Enum Limitations**: The converter currently does not support for certain enums (e.g. LINK). If you'll use a enum regardless, you'll receive the following output:
    
    `[Model]	-	Is an enum and is not supported!`
1. **General structure**:
    - The schema must include the `$defs` key, where the entire model is defined.  
    - Inside `$defs`, the model name should be defined, and within the model name, there must be a key called `properties`.  
    - Inside `properties`, the fields, or attributes ("columns" in relational databases) of the model should be defined. It is not necessary to define an `id` as it can be specified using the `-p` and `-t` arguments of the converter. The data type must be specified, and ideally, a description should also be included so it can be used in the GraphQL schema in Zendro.  
    - Associations  
        **Required fields**
        - **description**: Description of the association. Ideally, a description should be included so it can be used in the GraphQL schema in Zendro.  
        - **relationshipType**:  The type of relationship between models. The possible values are:
            - one-to-many  
            - many-to-many  
            - one-to-one  
            - many-to-one  
        - **$ref**: The path to the related model's definition. This should follow JSON Schema reference syntax.

        **Optional fields**
        - **referencedAttribute**: The attribute in the related model that establishes the connection.
        - **type**: Defines the expected data type of the association.
            - Default value
            - Data type
1. **Primary key**
    The converter will handle the primary key if you define a custom one in the schemas or forgott it at all.
    - Custom

        Input: [RelationshipTest.json](unit-test/RelationshipTest.json)

        Output: [relationshiptest.json](unit-test-results/relationshiptest.json)

    - Forgotten

        Input: [TestAllOf.json](unit-test/TestAllOf.json)

        Output: [testallof.json](unit-test-results/testallof.json)

* Test files for a better overview: [Unit-test](unit-test)

* Output after using the converter: [Unit-test-results](unit-test-results)

```
{
    "$defs": {
        "RelationshipTest": {
            "properties": {
                "description": {
                    "description": "Lorem ipsum dolor sit amet, consetetur sadipscing elitr, sed diam nonumy eirmod tempor invidunt ut labore et dolore magna aliquyam erat, sed diam voluptua.",
                    "type": [
                        "null",
                        "string"
                    ]
                },
                "relationshipWithoutAnything": {
                    "description": "Lorem ipsum dolor sit amet, consetetur sadipscing elitr, sed diam nonumy eirmod tempor invidunt ut labore et dolore magna aliquyam erat, sed diam voluptua.",
                    "$ref": "NormalSchema"
                },
		"relationshipWithType": {
                    "description": "Lorem ipsum dolor sit amet, consetetur sadipscing elitr, sed diam nonumy eirmod tempor invidunt ut labore et dolore magna aliquyam erat, sed diam voluptua.",
                    "$ref": "NormalSchema",
                    "relationshipType": "one-to-one"
                },
		"relationshipWithTypeAndReference": {
                    "description": "Lorem ipsum dolor sit amet, consetetur sadipscing elitr, sed diam nonumy eirmod tempor invidunt ut labore et dolore magna aliquyam erat, sed diam voluptua.",
                    "$ref": "NormalSchema",
                    "referencedAttribute": "TestRelationship",
                    "relationshipType": "one-to-one"
                },
		"relationshipManyToMany": {
                    "description": "Lorem ipsum dolor sit amet, consetetur sadipscing elitr, sed diam nonumy eirmod tempor invidunt ut labore et dolore magna aliquyam erat, sed diam voluptua.",
                    "relationshipType": "many-to-many",
                    "items": {
                        "$ref": "TestAllOf"
                    },
                    "type": [
                        "null",
                        "array"
                    ]
                },
                "TestId": {
                    "description": "Lorem ipsum dolor sit amet, consetetur sadipscing elitr, sed diam nonumy eirmod tempor invidunt ut labore et dolore magna aliquyam erat, sed diam voluptua.",
                    "type": "string"
                }
            },
            "required": [
                "TestId"
            ],
            "title": "RelationshipTest",
            "type": "object"
        }
    }
}
```

Zendro compatible output:
```json
{
    "model": "relationshiptest",
    "storageType": "sql",
    "attributes": {
        "description": {
            "type": "String",
            "description": "Lorem ipsum dolor sit amet, consetetur sadipscing elitr, sed diam nonumy eirmod tempor invidunt ut labore et dolore magna aliquyam erat, sed diam voluptua."
        },
        "TestDbId": {
            "type": "String",
            "description": "Lorem ipsum dolor sit amet, consetetur sadipscing elitr, sed diam nonumy eirmod tempor invidunt ut labore et dolore magna aliquyam erat, sed diam voluptua."
        },
        "relationshipWithType_ID": "String",
        "relationshipWithTypeAndReference_ID": "String",
        "relationshipManyToMany_IDs": "[ String ]"
    },
    "associations": {
        "relationshipWithType": {
            "type": "one_to_one",
            "implementation": "foreignkeys",
            "reverseAssociation": "relationshipTest",
            "target": "normalschema",
            "targetKey": "relationshipTest_ID",
            "sourceKey": "relationshipWithType_ID",
            "keysIn": "relationshiptest",
            "targetStorageType": "sql"
        },
        "relationshipWithTypeAndReference": {
            "type": "one_to_one",
            "implementation": "foreignkeys",
            "reverseAssociation": "TestRelationship",
            "target": "normalschema",
            "targetKey": "TestRelationship_ID",
            "sourceKey": "relationshipWithTypeAndReference_ID",
            "keysIn": "relationshiptest",
            "targetStorageType": "sql"
        },
        "relationshipManyToMany": {
            "type": "many_to_many",
            "implementation": "foreignkeys",
            "reverseAssociation": "relationshipTest",
            "target": "testallof",
            "targetKey": "relationshipTest_IDs",
            "sourceKey": "relationshipManyToMany_IDs",
            "keysIn": "relationshiptest",
            "targetStorageType": "sql"
        }
    },
    "internalId": "TestDbId"
}
```
---

#### Regular example:
```bash
python methods/converter.py -i "BrAPI-brapi_input_example" -o "results"
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
python methods/converter.py -i "BrAPI-brapi_input_example" -o "results/" -p "github" -t "Int"
```

Zendro compatible output:
```json
{
    "model": "season",
    "storageType": "sql",
    "attributes": {
        "seasonDbId": {
            "type": "Int",
            "description": "The ID which uniquely identifies a season. For backward compatibility it can be a string like '2012', '1957-2004'"
        },
        "seasonName": {
            "type": "Int",
            "description": "Name of the season. ex. 'Spring', 'Q2', 'Season A', etc."
        },
        "year": {
            "type": "Int",
            "description": "The 4 digit year of the season."
        },
        "observation_IDs": "[ String ]"
    },
    "associations": {
        "observation": {
            "type": "one_to_many",
            "implementation": "foreignkeys",
            "reverseAssociation": "season",
            "target": "observation",
            "targetKey": "season_ID",
            "sourceKey": "observation_IDs",
            "keysIn": "season",
            "targetStorageType": "sql"
        }
    },
    "internalId": "github"
}
```
As you can see, the primary key (here `github`) is the custom primary key name `github`.

Each generated data model contains the custom primary key name.

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

## Program Workflow

#### Main Execution (`main()`)

This is the main function of the script.<br />
Calls functions to locate JSON schema files, extract models, process their properties, and generate output files.

---

#### File Handling (`get_files(input_path)`)

Recursively searches the input directory for .json files and returns a list of valid file paths.

---

#### Model Extraction (`get_models(input_files)`)

- Reads JSON files and extracts models from the `$defs` section.
- Filters out incompatible models, such as `enum` types.
- Stores model attributes, associations, and primary keys in dictionaries.

---

#### Property Conversion (`get_properties(models)`)

- Converts BrAPI properties into Zendro-compatible attributes and associations.
- Determines primary key settings based on user input or schema patterns (`DbId`).
- Handles `oneOf` and `allOf` definitions by integrating properties from referenced models.<br />Example `allOf`:<br />&nbsp;&nbsp;Input: [TestAllOf.json](unit-test/TestAllOf.json)<br />&nbsp;&nbsp;Output: [testallof.json](unit-test-results/testallof.json)

---

#### Association Handling (`get_reverse_association(association)`)

- Maps BrAPI relationship types (`one-to-many`, `many-to-one`, etc.) to Zendro format (`one_to_many`, `many_to_one`, etc).
- Ensures, that bidirectional associations are properly defined.

---

#### Property Type Conversion (`get_property_type(input_property)`)

- Matches BrAPI types (`string`, `integer`, `boolean`, `number`) with Zendro-compatible types (`String`, `Int`, `Boolean`, `Float`).
- Supports array definitions (and nested properties).

---

#### Output Generation (`write_json(output_models)`)

- Formats converted models into JSON files with a standard Zendro structure.
- Saves the files in the specified output directory.

---

#### Logging (`log(msg)`)

Logs errors and warnings in `Log.txt`, recording timestamps.

---

## Error Handling

- Uses `try-except` blocks to catch file access errors (`OSError`), JSON parsing errors, and unexpected exceptions.
- Logs relevant information to `Log.txt` for debugging (including timestamp)
- Exits program in case of critical failures using `sys.exit(1)`.

---

## Notes

- Excludes models with unsopported types (e.g. enum).
- Handles `oneOf` properties interactively, prompting user input.
- Supports multiple storage backends with configurable options.

---

## Possible Improvements

- Automatic handling of `oneOf` properties without user input. <br /> This can be implemented, for example, by using command line arguments to decide in advance whether the program should select automatically or whether user input should be required.
- Enhanced error reporting with detailed exception tracking.
