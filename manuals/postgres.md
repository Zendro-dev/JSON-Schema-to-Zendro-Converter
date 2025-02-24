
# Postgres setup for the BrAPI schema


## Access PostgreSQL Shell
Use the `postgres` user and open the PostgreSQL interactive shell:
```bash
sudo -i -u postgres psql
```


## Create Database and User
Execute the following SQL commands inside `psql`:
```sql
CREATE DATABASE brapi_db;

CREATE USER <username> WITH PASSWORD '<psw>';
ALTER ROLE <username> SET client_encoding TO 'utf8';
ALTER ROLE <username> SET default_transaction_isolation TO 'read committed';
ALTER ROLE <username> SET timezone TO 'UTC';
GRANT ALL PRIVILEGES ON DATABASE brapi_db TO <username>;
\q
```

## Connect to database with the new username
```sql
psql -U <username> -d brapi_db
```


## Create Tables
Example table creation inside `brapi_db`:
```sql
--  additionalinfo
CREATE TABLE IF NOT EXISTS "additionalinfos" ("additionalInfoDbId" VARCHAR(255) PRIMARY KEY, "createdAt" TIMESTAMP, "updatedAt" TIMESTAMP, "additionalProperties" TEXT, "attribute_ID" TEXT, "variable_ID" TEXT, "list_ID" TEXT, "location_ID" TEXT, "person_ID" TEXT, "program_ID" TEXT, "study_ID" TEXT, "trial_ID" TEXT, "call_ID" TEXT, "callSet_ID" TEXT, "genomeMap_ID" TEXT, "markerPosition_ID" TEXT, "plate_ID" TEXT, "reference_ID" TEXT, "referenceSet_ID" TEXT, "sample_ID" TEXT, "variant_ID" TEXT, "variantSet_ID" TEXT, "cross_ID" TEXT, "crossingProject_ID" TEXT, "germplasm_ID" TEXT, "germplasmAttribute_ID" TEXT, "germplasmAttributeValue_ID" TEXT, "pedigreeNode_ID" TEXT, "plannedCross_ID" TEXT, "seedLot_ID" TEXT, "seedLotTransaction_ID" TEXT, "event_ID" TEXT, "image_ID" TEXT, "method_ID" TEXT, "observation_ID" TEXT, "observationUnit_ID" TEXT, "observationVariable_ID" TEXT, "ontology_ID" TEXT, "scale_ID" TEXT, "trait_ID" TEXT);

--  allelematrix
CREATE TABLE IF NOT EXISTS "allelematrices" ("alleleMatrixDbId" VARCHAR(255) PRIMARY KEY, "createdAt" TIMESTAMP, "updatedAt" TIMESTAMP, "expandHomozygotes" TEXT, "sepPhased" TEXT, "sepUnphased" TEXT, "unknownString" TEXT, "callSets_IDs" TEXT DEFAULT '[]', "dataMatrices_IDs" TEXT DEFAULT '[]', "pagination_IDs" TEXT DEFAULT '[]', "variantSets_IDs" TEXT DEFAULT '[]', "variants_IDs" TEXT DEFAULT '[]');

--  analysis
CREATE TABLE IF NOT EXISTS "analyses" ("analysisDbId" VARCHAR(255) PRIMARY KEY, "createdAt" TIMESTAMP, "updatedAt" TIMESTAMP, "analysisName" TEXT, "created" TEXT, "description" TEXT, "software" TEXT, "type" TEXT, "updated" TEXT, "variantSet_ID" TEXT);

--  attribute
CREATE TABLE IF NOT EXISTS "attributes" ("attributeDbId" VARCHAR(255) PRIMARY KEY, "createdAt" TIMESTAMP, "updatedAt" TIMESTAMP, "attributeCategory" TEXT, "attributeName" TEXT, "attributePUI" TEXT, "attributeDescription" TEXT, "commonCropName" TEXT, "contextOfUse" TEXT, "defaultValue" TEXT, "documentationURL" TEXT, "growthStage" TEXT, "institution" TEXT, "language" TEXT, "scientist" TEXT, "status" TEXT, "submissionTimestamp" TEXT, "synonyms" TEXT, "additionalInfo_ID" TEXT, "externalReferences_IDs" TEXT DEFAULT '[]', "method_ID" TEXT, "ontologyReference_ID" TEXT, "scale_ID" TEXT, "trait_ID" TEXT);

--  availableformat
CREATE TABLE IF NOT EXISTS "availableformats" ("availableFormatDbId" VARCHAR(255) PRIMARY KEY, "createdAt" TIMESTAMP, "updatedAt" TIMESTAMP, "dataFormat" TEXT, "expandHomozygotes" TEXT, "fileFormat" TEXT, "fileURL" TEXT, "sepPhased" TEXT, "sepUnphased" TEXT, "unknownString" TEXT, "variantSet_ID" TEXT);

--  breedingmethod
CREATE TABLE IF NOT EXISTS "breedingmethods" ("breedingMethodDbId" VARCHAR(255) PRIMARY KEY, "createdAt" TIMESTAMP, "updatedAt" TIMESTAMP, "abbreviation" TEXT, "breedingMethodName" TEXT, "description" TEXT, "germplasm_IDs" TEXT DEFAULT '[]', "pedigreeNodes_IDs" TEXT DEFAULT '[]');

--  call
CREATE TABLE IF NOT EXISTS "calls" ("callDbId" VARCHAR(255) PRIMARY KEY, "createdAt" TIMESTAMP, "updatedAt" TIMESTAMP, "genotypeValue" TEXT, "phaseSet" TEXT, "additionalInfo_ID" TEXT, "callSet_ID" TEXT, "genotypeMetadata_IDs" TEXT DEFAULT '[]', "variant_ID" TEXT, "variantSet_ID" TEXT);

--  callset
CREATE TABLE IF NOT EXISTS "callsets" ("callSetDbId" VARCHAR(255) PRIMARY KEY, "createdAt" TIMESTAMP, "updatedAt" TIMESTAMP, "callSetName" TEXT, "created" TEXT, "updated" TEXT, "additionalInfo_ID" TEXT, "alleleMatrices_IDs" TEXT DEFAULT '[]', "calls_IDs" TEXT DEFAULT '[]', "externalReferences_IDs" TEXT DEFAULT '[]', "sample_ID" TEXT, "study_ID" TEXT, "variantSets_IDs" TEXT DEFAULT '[]');

--  contact
CREATE TABLE IF NOT EXISTS "contacts" ("contactDbId" VARCHAR(255) PRIMARY KEY, "createdAt" TIMESTAMP, "updatedAt" TIMESTAMP, "email" TEXT, "instituteName" TEXT, "name" TEXT, "orcid" TEXT, "type" TEXT, "study_IDs" TEXT DEFAULT '[]', "trial_IDs" TEXT DEFAULT '[]');

--  contentmixture
CREATE TABLE IF NOT EXISTS "contentmixtures" ("contentMixtureDbId" VARCHAR(255) PRIMARY KEY, "createdAt" TIMESTAMP, "updatedAt" TIMESTAMP, "mixturePercentage" TEXT, "cross_ID" TEXT, "germplasm_ID" TEXT, "seedLot_ID" TEXT);

--  cross
CREATE TABLE IF NOT EXISTS "crosses" ("crossDbId" VARCHAR(255) PRIMARY KEY, "createdAt" TIMESTAMP, "updatedAt" TIMESTAMP, "crossName" TEXT, "additionalInfo_ID" TEXT, "crossAttributes_IDs" TEXT DEFAULT '[]', "crossingProject_ID" TEXT, "externalReferences_IDs" TEXT DEFAULT '[]', "plannedCross_ID" TEXT, "pollinationEvents_IDs" TEXT DEFAULT '[]', "observationUnits_IDs" TEXT DEFAULT '[]', "contentMixture_IDs" TEXT DEFAULT '[]');

--  crossattribute
CREATE TABLE IF NOT EXISTS "crossattributes" ("crossAttributeDbId" VARCHAR(255) PRIMARY KEY, "createdAt" TIMESTAMP, "updatedAt" TIMESTAMP, "crossAttributeName" TEXT, "crossAttributeValue" TEXT, "cross_ID" TEXT);

--  crossingproject
CREATE TABLE IF NOT EXISTS "crossingprojects" ("crossingProjectDbId" VARCHAR(255) PRIMARY KEY, "createdAt" TIMESTAMP, "updatedAt" TIMESTAMP, "commonCropName" TEXT, "crossingProjectDescription" TEXT, "crossingProjectName" TEXT, "additionalInfo_ID" TEXT, "externalReferences_IDs" TEXT DEFAULT '[]', "program_ID" TEXT, "crosses_IDs" TEXT DEFAULT '[]', "plannedCrosses_IDs" TEXT DEFAULT '[]', "pedigreeNodes_IDs" TEXT DEFAULT '[]');

--  crossparent
CREATE TABLE IF NOT EXISTS "crossparents" ("crossParentDbId" VARCHAR(255) PRIMARY KEY, "createdAt" TIMESTAMP, "updatedAt" TIMESTAMP, "germplasm_ID" TEXT, "observationUnit_ID" TEXT);

--  datalink
CREATE TABLE IF NOT EXISTS "datalinks" ("dataLinkDbId" VARCHAR(255) PRIMARY KEY, "createdAt" TIMESTAMP, "updatedAt" TIMESTAMP, "dataFormat" TEXT, "description" TEXT, "fileFormat" TEXT, "name" TEXT, "provenance" TEXT, "scientificType" TEXT, "url" TEXT, "version" TEXT);

--  datamatrix
CREATE TABLE IF NOT EXISTS "datamatrices" ("dataMatrixDbId" VARCHAR(255) PRIMARY KEY, "createdAt" TIMESTAMP, "updatedAt" TIMESTAMP, "dataMatrix" TEXT, "dataMatrixAbbreviation" TEXT, "dataMatrixName" TEXT, "dataType" TEXT, "alleleMatrix_ID" TEXT);

--  datasetauthorships
CREATE TABLE IF NOT EXISTS "datasetauthorships" ("datasetAuthorshipsDbId" VARCHAR(255) PRIMARY KEY, "createdAt" TIMESTAMP, "updatedAt" TIMESTAMP, "datasetPUI" TEXT, "license" TEXT, "publicReleaseDate" TEXT, "submissionDate" TEXT, "trial_ID" TEXT);

--  documentationlink
CREATE TABLE IF NOT EXISTS "documentationlinks" ("documentationLinkDbId" VARCHAR(255) PRIMARY KEY, "createdAt" TIMESTAMP, "updatedAt" TIMESTAMP, "URL" TEXT, "type" TEXT, "ontologyReference_ID" TEXT);

--  donor
CREATE TABLE IF NOT EXISTS "donors" ("donorDbId" VARCHAR(255) PRIMARY KEY, "createdAt" TIMESTAMP, "updatedAt" TIMESTAMP, "donorAccessionNumber" TEXT, "donorInstituteCode" TEXT, "germplasm_ID" TEXT);

--  environmentparameters
CREATE TABLE IF NOT EXISTS "environmentparameters" ("environmentParametersDbId" VARCHAR(255) PRIMARY KEY, "createdAt" TIMESTAMP, "updatedAt" TIMESTAMP, "description" TEXT, "parameterName" TEXT, "parameterPUI" TEXT, "unit" TEXT, "unitPUI" TEXT, "value" TEXT, "valuePUI" TEXT, "study_ID" TEXT);

--  event
CREATE TABLE IF NOT EXISTS "events" ("eventDbId" VARCHAR(255) PRIMARY KEY, "createdAt" TIMESTAMP, "updatedAt" TIMESTAMP, "eventDescription" TEXT, "eventType" TEXT, "eventTypeDbId" TEXT, "additionalInfo_ID" TEXT, "eventDateRange_IDs" TEXT DEFAULT '[]', "eventParameters_IDs" TEXT DEFAULT '[]', "observationUnits_IDs" TEXT DEFAULT '[]', "study_ID" TEXT);

--  eventdaterange
CREATE TABLE IF NOT EXISTS "eventdateranges" ("eventDateRangeDbId" VARCHAR(255) PRIMARY KEY, "createdAt" TIMESTAMP, "updatedAt" TIMESTAMP, "discreteDates" TEXT, "endDate" TEXT, "startDate" TEXT, "event_ID" TEXT);

--  eventparameter
CREATE TABLE IF NOT EXISTS "eventparameters" ("eventParameterDbId" VARCHAR(255) PRIMARY KEY, "createdAt" TIMESTAMP, "updatedAt" TIMESTAMP, "code" TEXT, "description" TEXT, "name" TEXT, "units" TEXT, "value" TEXT, "valueDescription" TEXT, "valuesByDate" TEXT, "event_ID" TEXT);

--  experimentaldesign
CREATE TABLE IF NOT EXISTS "experimentaldesigns" ("experimentalDesignDbId" VARCHAR(255) PRIMARY KEY, "createdAt" TIMESTAMP, "updatedAt" TIMESTAMP, "PUI" TEXT, "description" TEXT, "study_ID" TEXT);

--  externalreference
CREATE TABLE IF NOT EXISTS "externalreferences" ("referenceDbId" VARCHAR(255) PRIMARY KEY, "createdAt" TIMESTAMP, "updatedAt" TIMESTAMP, "attribute_ID" TEXT, "referenceSource" TEXT, "variable_ID" TEXT, "list_ID" TEXT, "location_ID" TEXT, "person_ID" TEXT, "program_ID" TEXT, "study_ID" TEXT, "trial_ID" TEXT, "callSet_ID" TEXT, "plate_ID" TEXT, "reference_ID" TEXT, "referenceSet_ID" TEXT, "sample_ID" TEXT, "variant_ID" TEXT, "variantSet_ID" TEXT, "cross_ID" TEXT, "crossingProject_ID" TEXT, "germplasm_ID" TEXT, "germplasmAttribute_ID" TEXT, "germplasmAttributeValue_ID" TEXT, "pedigreeNode_ID" TEXT, "plannedCross_ID" TEXT, "seedLot_ID" TEXT, "seedLotTransaction_ID" TEXT, "image_ID" TEXT, "method_ID" TEXT, "observation_ID" TEXT, "observationUnit_ID" TEXT, "observationVariable_ID" TEXT, "scale_ID" TEXT, "trait_ID" TEXT);

--  genomemap
CREATE TABLE IF NOT EXISTS "genomemaps" ("mapDbId" VARCHAR(255) PRIMARY KEY, "createdAt" TIMESTAMP, "updatedAt" TIMESTAMP, "comments" TEXT, "commonCropName" TEXT, "documentationURL" TEXT, "linkageGroupCount" TEXT, "mapName" TEXT, "mapPUI" TEXT, "markerCount" TEXT, "publishedDate" TEXT, "scientificName" TEXT, "type" TEXT, "unit" TEXT, "additionalInfo_ID" TEXT, "markerPositions_IDs" TEXT DEFAULT '[]');

--  genotypemetadata
CREATE TABLE IF NOT EXISTS "genotypemetadata" ("genotypeMetadataDbId" VARCHAR(255) PRIMARY KEY, "createdAt" TIMESTAMP, "updatedAt" TIMESTAMP, "dataType" TEXT, "fieldAbbreviation" TEXT, "fieldName" TEXT, "fieldValue" TEXT, "call_ID" TEXT);

--  geon
CREATE TABLE IF NOT EXISTS "geons" ("geONDbId" VARCHAR(255) PRIMARY KEY, "createdAt" TIMESTAMP, "updatedAt" TIMESTAMP, "type" TEXT, "image_ID" TEXT, "observation_ID" TEXT, "observationUnit_ID" TEXT, "germplasmOrigin_ID" TEXT, "location_ID" TEXT);

--  geongeometry
CREATE TABLE IF NOT EXISTS "geongeometries" ("geONGeometryDbId" VARCHAR(255) PRIMARY KEY, "createdAt" TIMESTAMP, "updatedAt" TIMESTAMP, "coordinates" TEXT, "type" TEXT);

--  germplasm
CREATE TABLE IF NOT EXISTS "germplasms" ("germplasmDbId" VARCHAR(255) PRIMARY KEY, "createdAt" TIMESTAMP, "updatedAt" TIMESTAMP, "crossParent_ID" TEXT, "accessionNumber" TEXT, "acquisitionDate" TEXT, "biologicalStatusOfAccessionCode" TEXT, "biologicalStatusOfAccessionDescription" TEXT, "collection" TEXT, "commonCropName" TEXT, "countryOfOriginCode" TEXT, "defaultDisplayName" TEXT, "documentationURL" TEXT, "genus" TEXT, "germplasmName" TEXT, "germplasmPUI" TEXT, "germplasmPreprocessing" TEXT, "instituteCode" TEXT, "instituteName" TEXT, "pedigree" TEXT, "seedSource" TEXT, "seedSourceDescription" TEXT, "species" TEXT, "speciesAuthority" TEXT, "subtaxa" TEXT, "subtaxaAuthority" TEXT, "additionalInfo_ID" TEXT, "breedingMethod_ID" TEXT, "pedigreeNode_ID" TEXT, "donors_IDs" TEXT DEFAULT '[]', "externalReferences_IDs" TEXT DEFAULT '[]', "germplasmOrigin_IDs" TEXT DEFAULT '[]', "storageTypes_IDs" TEXT DEFAULT '[]', "synonyms_IDs" TEXT DEFAULT '[]', "taxonIds_IDs" TEXT DEFAULT '[]', "samples_IDs" TEXT DEFAULT '[]', "attributeValues_IDs" TEXT DEFAULT '[]', "progenyPedigreeNodes_IDs" TEXT DEFAULT '[]', "parentPedigreeNodes_IDs" TEXT DEFAULT '[]', "siblingPedigreeNodes_IDs" TEXT DEFAULT '[]', "observations_IDs" TEXT DEFAULT '[]', "observationUnits_IDs" TEXT DEFAULT '[]', "reference_ID" TEXT, "referenceSet_ID" TEXT, "contentMixture_IDs" TEXT DEFAULT '[]');

--  germplasmattribute
CREATE TABLE IF NOT EXISTS "germplasmattributes" ("germplasmAttributeDbId" VARCHAR(255) PRIMARY KEY, "createdAt" TIMESTAMP, "updatedAt" TIMESTAMP, "attributeCategory" TEXT, "attributeDbId" TEXT, "attributeName" TEXT, "attributePUI" TEXT, "attributeDescription" TEXT, "commonCropName" TEXT, "contextOfUse" TEXT, "defaultValue" TEXT, "documentationURL" TEXT, "growthStage" TEXT, "institution" TEXT, "language" TEXT, "scientist" TEXT, "status" TEXT, "submissionTimestamp" TEXT, "synonyms" TEXT, "attributeValues_IDs" TEXT DEFAULT '[]', "additionalInfo_ID" TEXT, "externalReferences_IDs" TEXT DEFAULT '[]', "method_ID" TEXT, "ontologyReference_ID" TEXT, "scale_ID" TEXT, "trait_ID" TEXT);

--  germplasmattributevalue
CREATE TABLE IF NOT EXISTS "germplasmattributevalues" ("attributeValueDbId" VARCHAR(255) PRIMARY KEY, "createdAt" TIMESTAMP, "updatedAt" TIMESTAMP, "determinedDate" TEXT, "value" TEXT, "additionalInfo_ID" TEXT, "attribute_ID" TEXT, "externalReferences_IDs" TEXT DEFAULT '[]', "germplasm_ID" TEXT);

--  germplasmchild
CREATE TABLE IF NOT EXISTS "germplasmchildren" ("germplasmChildDbId" VARCHAR(255) PRIMARY KEY, "createdAt" TIMESTAMP, "updatedAt" TIMESTAMP, "childGermplasm_ID" TEXT, "pedigreeNode_ID" TEXT);

--  germplasmorigin
CREATE TABLE IF NOT EXISTS "germplasmorigins" ("germplasmOriginDbId" VARCHAR(255) PRIMARY KEY, "createdAt" TIMESTAMP, "updatedAt" TIMESTAMP, "coordinateUncertainty" TEXT, "coordinates_IDs" TEXT DEFAULT '[]', "germplasm_ID" TEXT);

--  germplasmparent
CREATE TABLE IF NOT EXISTS "germplasmparents" ("germplasmParentDbId" VARCHAR(255) PRIMARY KEY, "createdAt" TIMESTAMP, "updatedAt" TIMESTAMP, "parentGermplasm_ID" TEXT, "pedigreeNode_ID" TEXT);

--  growthfacility
CREATE TABLE IF NOT EXISTS "growthfacilities" ("growthFacilityDbId" VARCHAR(255) PRIMARY KEY, "createdAt" TIMESTAMP, "updatedAt" TIMESTAMP, "PUI" TEXT, "description" TEXT, "study_ID" TEXT);

--  image
CREATE TABLE IF NOT EXISTS "images" ("imageDbId" VARCHAR(255) PRIMARY KEY, "createdAt" TIMESTAMP, "updatedAt" TIMESTAMP, "copyright" TEXT, "description" TEXT, "descriptiveOntologyTerms" TEXT, "imageFileName" TEXT, "imageFileSize" TEXT, "imageHeight" TEXT, "imageName" TEXT, "imageTimeStamp" TEXT, "imageURL" TEXT, "imageWidth" TEXT, "mimeType" TEXT, "additionalInfo_ID" TEXT, "externalReferences_IDs" TEXT DEFAULT '[]', "imageLocation_ID" TEXT, "observationUnit_ID" TEXT, "observations_IDs" TEXT DEFAULT '[]');

--  lastupdate
CREATE TABLE IF NOT EXISTS "lastupdates" ("lastUpdateDbId" VARCHAR(255) PRIMARY KEY, "createdAt" TIMESTAMP, "updatedAt" TIMESTAMP, "timestamp" TEXT, "version" TEXT, "study_ID" TEXT);

--  list
CREATE TABLE IF NOT EXISTS "lists" ("listDbId" VARCHAR(255) PRIMARY KEY, "createdAt" TIMESTAMP, "updatedAt" TIMESTAMP, "data" TEXT, "dateCreated" TEXT, "dateModified" TEXT, "listDescription" TEXT, "listName" TEXT, "listOwnerName" TEXT, "listSize" TEXT, "listSource" TEXT, "additionalInfo_ID" TEXT, "externalReferences_IDs" TEXT DEFAULT '[]', "listOwnerPerson_ID" TEXT);

--  location
CREATE TABLE IF NOT EXISTS "locations" ("locationDbId" VARCHAR(255) PRIMARY KEY, "createdAt" TIMESTAMP, "updatedAt" TIMESTAMP, "abbreviation" TEXT, "coordinateDescription" TEXT, "coordinateUncertainty" TEXT, "countryCode" TEXT, "countryName" TEXT, "documentationURL" TEXT, "environmentType" TEXT, "exposure" TEXT, "instituteAddress" TEXT, "instituteName" TEXT, "locationName" TEXT, "locationType" TEXT, "siteStatus" TEXT, "slope" TEXT, "topography" TEXT, "additionalInfo_ID" TEXT, "coordinates_ID" TEXT, "externalReferences_IDs" TEXT DEFAULT '[]', "parentLocation_ID" TEXT, "childLocations_IDs" TEXT DEFAULT '[]', "studies_IDs" TEXT DEFAULT '[]', "seedLots_IDs" TEXT DEFAULT '[]', "observationUnits_IDs" TEXT DEFAULT '[]');

--  markerposition
CREATE TABLE IF NOT EXISTS "markerpositions" ("markerPositionDbId" VARCHAR(255) PRIMARY KEY, "createdAt" TIMESTAMP, "updatedAt" TIMESTAMP, "linkageGroupName" TEXT, "position" TEXT, "additionalInfo_ID" TEXT, "map_ID" TEXT, "variant_ID" TEXT);

--  metadatafield
CREATE TABLE IF NOT EXISTS "metadatafields" ("metadataFieldDbId" VARCHAR(255) PRIMARY KEY, "createdAt" TIMESTAMP, "updatedAt" TIMESTAMP, "dataType" TEXT, "fieldAbbreviation" TEXT, "fieldName" TEXT, "variantSet_ID" TEXT);

--  method
CREATE TABLE IF NOT EXISTS "methods" ("methodDbId" VARCHAR(255) PRIMARY KEY, "createdAt" TIMESTAMP, "updatedAt" TIMESTAMP, "attribute_ID" TEXT, "variable_ID" TEXT, "germplasmAttribute_ID" TEXT, "bibliographicalReference" TEXT, "description" TEXT, "formula" TEXT, "methodClass" TEXT, "methodName" TEXT, "methodPUI" TEXT, "additionalInfo_ID" TEXT, "externalReferences_IDs" TEXT DEFAULT '[]', "ontologyReference_ID" TEXT, "observationVariable_ID" TEXT);

--  observation
CREATE TABLE IF NOT EXISTS "observations" ("observationDbId" VARCHAR(255) PRIMARY KEY, "createdAt" TIMESTAMP, "updatedAt" TIMESTAMP, "collector" TEXT, "observationTimeStamp" TEXT, "uploadedBy" TEXT, "value" TEXT, "additionalInfo_ID" TEXT, "externalReferences_IDs" TEXT DEFAULT '[]', "geoCoordinates_ID" TEXT, "germplasm_ID" TEXT, "observationUnit_ID" TEXT, "observationVariable_ID" TEXT, "season_ID" TEXT, "study_ID" TEXT, "images_IDs" TEXT DEFAULT '[]');

--  observationunit
CREATE TABLE IF NOT EXISTS "observationunits" ("observationUnitDbId" VARCHAR(255) PRIMARY KEY, "createdAt" TIMESTAMP, "updatedAt" TIMESTAMP, "crossParent_IDs" TEXT DEFAULT '[]', "observationUnitName" TEXT, "observationUnitPUI" TEXT, "additionalInfo_ID" TEXT, "cross_ID" TEXT, "externalReferences_IDs" TEXT DEFAULT '[]', "germplasm_ID" TEXT, "location_ID" TEXT, "observationUnitPosition_IDs" TEXT DEFAULT '[]', "program_ID" TEXT, "seedLot_ID" TEXT, "study_ID" TEXT, "treatments_IDs" TEXT DEFAULT '[]', "trial_ID" TEXT, "samples_IDs" TEXT DEFAULT '[]', "events_IDs" TEXT DEFAULT '[]', "images_IDs" TEXT DEFAULT '[]', "observations_IDs" TEXT DEFAULT '[]', "observationUnitLevelRelationship_ID" TEXT);

--  observationunithierarchylevel
CREATE TABLE IF NOT EXISTS "observationunithierarchylevels" ("observationUnitHierarchyLevelDbId" VARCHAR(255) PRIMARY KEY, "createdAt" TIMESTAMP, "updatedAt" TIMESTAMP, "levelName" TEXT, "levelOrder" TEXT, "study_ID" TEXT);

--  observationunitlevel
CREATE TABLE IF NOT EXISTS "observationunitlevels" ("observationUnitLevelDbId" VARCHAR(255) PRIMARY KEY, "createdAt" TIMESTAMP, "updatedAt" TIMESTAMP, "observationUnitPosition_ID" TEXT, "levelCode" TEXT, "levelName" TEXT, "levelOrder" TEXT, "observationUnitHierarchyLevelDbId" TEXT);

--  observationunitlevelrelationship
CREATE TABLE IF NOT EXISTS "observationunitlevelrelationships" ("observationUnitLevelRelationshipDbId" VARCHAR(255) PRIMARY KEY, "createdAt" TIMESTAMP, "updatedAt" TIMESTAMP, "observationUnitPosition_ID" TEXT, "levelCode" TEXT, "observationUnitLevelDbId" TEXT, "levelName" TEXT, "levelOrder" TEXT, "observationUnitHierarchyLevelDbId" TEXT, "observationUnit_ID" TEXT);

--  observationunitposition
CREATE TABLE IF NOT EXISTS "observationunitpositions" ("observationUnitPositionDbId" VARCHAR(255) PRIMARY KEY, "createdAt" TIMESTAMP, "updatedAt" TIMESTAMP, "entryType" TEXT, "positionCoordinateX" TEXT, "positionCoordinateXType" TEXT, "positionCoordinateY" TEXT, "positionCoordinateYType" TEXT, "geoCoordinates_IDs" TEXT DEFAULT '[]', "observationLevel_ID" TEXT, "observationLevelRelationships_IDs" TEXT DEFAULT '[]', "observationUnit_ID" TEXT);

--  observationvariable
CREATE TABLE IF NOT EXISTS "observationvariables" ("observationVariableDbId" VARCHAR(255) PRIMARY KEY, "createdAt" TIMESTAMP, "updatedAt" TIMESTAMP, "observationVariableName" TEXT, "observationVariablePUI" TEXT, "commonCropName" TEXT, "contextOfUse" TEXT, "defaultValue" TEXT, "documentationURL" TEXT, "growthStage" TEXT, "institution" TEXT, "language" TEXT, "scientist" TEXT, "status" TEXT, "submissionTimestamp" TEXT, "synonyms" TEXT, "variableDbId" TEXT, "studies_IDs" TEXT DEFAULT '[]', "observations_IDs" TEXT DEFAULT '[]', "additionalInfo_ID" TEXT, "externalReferences_IDs" TEXT DEFAULT '[]', "method_ID" TEXT, "ontologyReference_ID" TEXT, "scale_ID" TEXT, "trait_ID" TEXT);

--  ontology
CREATE TABLE IF NOT EXISTS "ontologies" ("ontologyDbId" VARCHAR(255) PRIMARY KEY, "createdAt" TIMESTAMP, "updatedAt" TIMESTAMP, "authors" TEXT, "copyright" TEXT, "description" TEXT, "documentationURL" TEXT, "licence" TEXT, "ontologyName" TEXT, "version" TEXT, "additionalInfo_ID" TEXT, "ontologyReference_ID" TEXT);

--  ontologyreference
CREATE TABLE IF NOT EXISTS "ontologyreferences" ("ontologyReferenceDbId" VARCHAR(255) PRIMARY KEY, "createdAt" TIMESTAMP, "updatedAt" TIMESTAMP, "attribute_ID" TEXT, "variable_ID" TEXT, "germplasmAttribute_ID" TEXT, "method_ID" TEXT, "observationVariable_ID" TEXT, "version" TEXT, "documentationLinks_IDs" TEXT DEFAULT '[]', "ontology_ID" TEXT, "scale_ID" TEXT, "trait_ID" TEXT);

--  ontologyterm
CREATE TABLE IF NOT EXISTS "ontologyterms" ("ontologyTermDbId" VARCHAR(255) PRIMARY KEY, "createdAt" TIMESTAMP, "updatedAt" TIMESTAMP, "term" TEXT, "termURI" TEXT, "reference_ID" TEXT, "referenceSet_ID" TEXT);

--  pagination
CREATE TABLE IF NOT EXISTS "paginations" ("paginationDbId" VARCHAR(255) PRIMARY KEY, "createdAt" TIMESTAMP, "updatedAt" TIMESTAMP, "dimension" TEXT, "page" TEXT, "pageSize" TEXT, "totalCount" TEXT, "totalPages" TEXT, "alleleMatrix_ID" TEXT);

--  pedigreenode
CREATE TABLE IF NOT EXISTS "pedigreenodes" ("pedigreeNodeDbId" VARCHAR(255) PRIMARY KEY, "createdAt" TIMESTAMP, "updatedAt" TIMESTAMP, "crossingYear" TEXT, "defaultDisplayName" TEXT, "familyCode" TEXT, "pedigreeString" TEXT, "additionalInfo_ID" TEXT, "breedingMethod_ID" TEXT, "crossingProject_ID" TEXT, "externalReferences_IDs" TEXT DEFAULT '[]', "germplasm_ID" TEXT, "parents_IDs" TEXT DEFAULT '[]', "progeny_IDs" TEXT DEFAULT '[]');

--  person
CREATE TABLE IF NOT EXISTS "people" ("personDbId" VARCHAR(255) PRIMARY KEY, "createdAt" TIMESTAMP, "updatedAt" TIMESTAMP, "description" TEXT, "emailAddress" TEXT, "firstName" TEXT, "lastName" TEXT, "mailingAddress" TEXT, "middleName" TEXT, "phoneNumber" TEXT, "userID" TEXT, "additionalInfo_ID" TEXT, "externalReferences_IDs" TEXT DEFAULT '[]', "lists_IDs" TEXT DEFAULT '[]', "programs_IDs" TEXT DEFAULT '[]');

--  plannedcross
CREATE TABLE IF NOT EXISTS "plannedcrosses" ("plannedCrossDbId" VARCHAR(255) PRIMARY KEY, "createdAt" TIMESTAMP, "updatedAt" TIMESTAMP, "plannedCrossName" TEXT, "status" TEXT, "additionalInfo_ID" TEXT, "crossingProject_ID" TEXT, "externalReferences_IDs" TEXT DEFAULT '[]', "crosses_IDs" TEXT DEFAULT '[]');

--  plate
CREATE TABLE IF NOT EXISTS "plates" ("plateDbId" VARCHAR(255) PRIMARY KEY, "createdAt" TIMESTAMP, "updatedAt" TIMESTAMP, "plateBarcode" TEXT, "plateFormat" TEXT, "plateName" TEXT, "sampleType" TEXT, "additionalInfo_ID" TEXT, "externalReferences_IDs" TEXT DEFAULT '[]', "program_ID" TEXT, "study_ID" TEXT, "trial_ID" TEXT, "samples_IDs" TEXT DEFAULT '[]');

--  pollinationevent
CREATE TABLE IF NOT EXISTS "pollinationevents" ("pollinationEventDbId" VARCHAR(255) PRIMARY KEY, "createdAt" TIMESTAMP, "updatedAt" TIMESTAMP, "pollinationNumber" TEXT, "pollinationSuccessful" TEXT, "pollinationTimeStamp" TEXT, "cross_ID" TEXT);

--  program
CREATE TABLE IF NOT EXISTS "programs" ("programDbId" VARCHAR(255) PRIMARY KEY, "createdAt" TIMESTAMP, "updatedAt" TIMESTAMP, "abbreviation" TEXT, "commonCropName" TEXT, "documentationURL" TEXT, "fundingInformation" TEXT, "objective" TEXT, "programName" TEXT, "programType" TEXT, "additionalInfo_ID" TEXT, "externalReferences_IDs" TEXT DEFAULT '[]', "leadPerson_ID" TEXT, "trials_IDs" TEXT DEFAULT '[]', "plates_IDs" TEXT DEFAULT '[]', "samples_IDs" TEXT DEFAULT '[]', "crossingProjects_IDs" TEXT DEFAULT '[]', "seedLots_IDs" TEXT DEFAULT '[]', "observationUnits_IDs" TEXT DEFAULT '[]');

--  publication
CREATE TABLE IF NOT EXISTS "publications" ("publicationDbId" VARCHAR(255) PRIMARY KEY, "createdAt" TIMESTAMP, "updatedAt" TIMESTAMP, "publicationPUI" TEXT, "publicationReference" TEXT, "trial_ID" TEXT);

--  reference
CREATE TABLE IF NOT EXISTS "references" ("referenceDbId" VARCHAR(255) PRIMARY KEY, "createdAt" TIMESTAMP, "updatedAt" TIMESTAMP, "commonCropName" TEXT, "isDerived" TEXT, "length" TEXT, "md5checksum" TEXT, "referenceName" TEXT, "sourceAccessions" TEXT, "sourceDivergence" TEXT, "sourceURI" TEXT, "additionalInfo_ID" TEXT, "externalReferences_IDs" TEXT DEFAULT '[]', "referenceSet_ID" TEXT, "sourceGermplasm_IDs" TEXT DEFAULT '[]', "species_ID" TEXT, "variants_IDs" TEXT DEFAULT '[]');

--  referenceset
CREATE TABLE IF NOT EXISTS "referencesets" ("referenceSetDbId" VARCHAR(255) PRIMARY KEY, "createdAt" TIMESTAMP, "updatedAt" TIMESTAMP, "assemblyPUI" TEXT, "commonCropName" TEXT, "description" TEXT, "isDerived" TEXT, "md5checksum" TEXT, "referenceSetName" TEXT, "sourceAccessions" TEXT, "sourceURI" TEXT, "additionalInfo_ID" TEXT, "externalReferences_IDs" TEXT DEFAULT '[]', "sourceGermplasm_IDs" TEXT DEFAULT '[]', "species_ID" TEXT, "references_ID" TEXT, "variants_IDs" TEXT DEFAULT '[]', "variantSets_IDs" TEXT DEFAULT '[]');

--  sample
CREATE TABLE IF NOT EXISTS "samples" ("sampleDbId" VARCHAR(255) PRIMARY KEY, "createdAt" TIMESTAMP, "updatedAt" TIMESTAMP, "column" TEXT, "row" TEXT, "sampleBarcode" TEXT, "sampleDescription" TEXT, "sampleGroupId" TEXT, "sampleName" TEXT, "samplePUI" TEXT, "sampleTimestamp" TEXT, "sampleType" TEXT, "takenBy" TEXT, "tissueType" TEXT, "well" TEXT, "additionalInfo_ID" TEXT, "callSets_IDs" TEXT DEFAULT '[]', "externalReferences_IDs" TEXT DEFAULT '[]', "germplasm_ID" TEXT, "observationUnit_ID" TEXT, "plate_ID" TEXT, "program_ID" TEXT, "study_ID" TEXT, "trial_ID" TEXT);

--  scale
CREATE TABLE IF NOT EXISTS "scales" ("scaleDbId" VARCHAR(255) PRIMARY KEY, "createdAt" TIMESTAMP, "updatedAt" TIMESTAMP, "attribute_ID" TEXT, "variable_ID" TEXT, "germplasmAttribute_ID" TEXT, "observationVariable_ID" TEXT, "dataType" TEXT, "decimalPlaces" TEXT, "scaleName" TEXT, "scalePUI" TEXT, "units" TEXT, "additionalInfo_ID" TEXT, "externalReferences_IDs" TEXT DEFAULT '[]', "ontologyReference_ID" TEXT, "validValues_ID" TEXT);

--  season
CREATE TABLE IF NOT EXISTS "seasons" ("seasonDbId" VARCHAR(255) PRIMARY KEY, "createdAt" TIMESTAMP, "updatedAt" TIMESTAMP, "seasonName" TEXT, "year" TEXT, "observation_IDs" TEXT DEFAULT '[]');

--  seedlot
CREATE TABLE IF NOT EXISTS "seedlots" ("seedLotDbId" VARCHAR(255) PRIMARY KEY, "createdAt" TIMESTAMP, "updatedAt" TIMESTAMP, "amount" TEXT, "createdDate" TEXT, "lastUpdated" TEXT, "seedLotDescription" TEXT, "seedLotName" TEXT, "sourceCollection" TEXT, "storageLocation" TEXT, "units" TEXT, "additionalInfo_ID" TEXT, "contentMixture_IDs" TEXT DEFAULT '[]', "externalReferences_IDs" TEXT DEFAULT '[]', "location_ID" TEXT, "program_ID" TEXT, "fromSeedLotTransactions_IDs" TEXT DEFAULT '[]', "toSeedLotTransactions_IDs" TEXT DEFAULT '[]', "observationUnits_IDs" TEXT DEFAULT '[]');

--  seedlottransaction
CREATE TABLE IF NOT EXISTS "seedlottransactions" ("transactionDbId" VARCHAR(255) PRIMARY KEY, "createdAt" TIMESTAMP, "updatedAt" TIMESTAMP, "amount" TEXT, "transactionDescription" TEXT, "transactionTimestamp" TEXT, "units" TEXT, "additionalInfo_ID" TEXT, "externalReferences_IDs" TEXT DEFAULT '[]', "fromSeedLot_ID" TEXT, "toSeedLot_ID" TEXT);

--  storagetype
CREATE TABLE IF NOT EXISTS "storagetypes" ("storageTypeDbId" VARCHAR(255) PRIMARY KEY, "createdAt" TIMESTAMP, "updatedAt" TIMESTAMP, "code" TEXT, "description" TEXT, "germplasm_ID" TEXT);

--  study
CREATE TABLE IF NOT EXISTS "studies" ("studyDbId" VARCHAR(255) PRIMARY KEY, "createdAt" TIMESTAMP, "updatedAt" TIMESTAMP, "active" TEXT, "commonCropName" TEXT, "culturalPractices" TEXT, "documentationURL" TEXT, "endDate" TEXT, "license" TEXT, "observationUnitsDescription" TEXT, "seasons" TEXT, "startDate" TEXT, "studyCode" TEXT, "studyDescription" TEXT, "studyName" TEXT, "studyPUI" TEXT, "studyType" TEXT, "additionalInfo_ID" TEXT, "contacts_IDs" TEXT DEFAULT '[]', "environmentParameters_IDs" TEXT DEFAULT '[]', "experimentalDesign_ID" TEXT, "externalReferences_IDs" TEXT DEFAULT '[]', "growthFacility_ID" TEXT, "lastUpdate_ID" TEXT, "location_ID" TEXT, "observationLevels_IDs" TEXT DEFAULT '[]', "observationVariables_IDs" TEXT DEFAULT '[]', "trial_ID" TEXT, "callSets_IDs" TEXT DEFAULT '[]', "plates_IDs" TEXT DEFAULT '[]', "samples_IDs" TEXT DEFAULT '[]', "variantSets_IDs" TEXT DEFAULT '[]', "events_IDs" TEXT DEFAULT '[]', "observations_IDs" TEXT DEFAULT '[]', "observationUnits_IDs" TEXT DEFAULT '[]');

--  synonym
CREATE TABLE IF NOT EXISTS "synonyms" ("synonymDbId" VARCHAR(255) PRIMARY KEY, "createdAt" TIMESTAMP, "updatedAt" TIMESTAMP, "synonym" TEXT, "type" TEXT, "germplasm_ID" TEXT);

--  taxonid
CREATE TABLE IF NOT EXISTS "taxonids" ("taxonDbId" VARCHAR(255) PRIMARY KEY, "createdAt" TIMESTAMP, "updatedAt" TIMESTAMP, "sourceName" TEXT, "germplasm_ID" TEXT);

--  trait
CREATE TABLE IF NOT EXISTS "traits" ("traitDbId" VARCHAR(255) PRIMARY KEY, "createdAt" TIMESTAMP, "updatedAt" TIMESTAMP, "variable_ID" TEXT, "germplasmAttribute_ID" TEXT, "observationVariable_ID" TEXT, "alternativeAbbreviations" TEXT, "attribute" TEXT, "attributePUI" TEXT, "entity" TEXT, "entityPUI" TEXT, "mainAbbreviation" TEXT, "status" TEXT, "synonyms" TEXT, "traitClass" TEXT, "traitDescription" TEXT, "traitName" TEXT, "traitPUI" TEXT, "additionalInfo_ID" TEXT, "externalReferences_IDs" TEXT DEFAULT '[]', "ontologyReference_ID" TEXT);

--  treatment
CREATE TABLE IF NOT EXISTS "treatments" ("treatmentDbId" VARCHAR(255) PRIMARY KEY, "createdAt" TIMESTAMP, "updatedAt" TIMESTAMP, "factor" TEXT, "modality" TEXT, "observationUnit_ID" TEXT);

--  trial
CREATE TABLE IF NOT EXISTS "trials" ("trialDbId" VARCHAR(255) PRIMARY KEY, "createdAt" TIMESTAMP, "updatedAt" TIMESTAMP, "active" TEXT, "commonCropName" TEXT, "documentationURL" TEXT, "endDate" TEXT, "startDate" TEXT, "trialDescription" TEXT, "trialName" TEXT, "trialPUI" TEXT, "additionalInfo_ID" TEXT, "contacts_IDs" TEXT DEFAULT '[]', "datasetAuthorships_IDs" TEXT DEFAULT '[]', "externalReferences_IDs" TEXT DEFAULT '[]', "program_ID" TEXT, "studies_IDs" TEXT DEFAULT '[]', "publications_IDs" TEXT DEFAULT '[]', "plates_IDs" TEXT DEFAULT '[]', "samples_IDs" TEXT DEFAULT '[]', "observationUnits_IDs" TEXT DEFAULT '[]');

--  validvalues
CREATE TABLE IF NOT EXISTS "validvalues" ("validValuesDbId" VARCHAR(255) PRIMARY KEY, "createdAt" TIMESTAMP, "updatedAt" TIMESTAMP, "maximumValue" TEXT, "minimumValue" TEXT, "categories_IDs" TEXT DEFAULT '[]', "scale_ID" TEXT);

--  validvaluescategory
CREATE TABLE IF NOT EXISTS "validvaluescategories" ("validValuesCategoryDbId" VARCHAR(255) PRIMARY KEY, "createdAt" TIMESTAMP, "updatedAt" TIMESTAMP, "label" TEXT, "value" TEXT, "validValues_ID" TEXT);

--  variable
CREATE TABLE IF NOT EXISTS "variables" ("variableDbId" VARCHAR(255) PRIMARY KEY, "createdAt" TIMESTAMP, "updatedAt" TIMESTAMP, "commonCropName" TEXT, "contextOfUse" TEXT, "defaultValue" TEXT, "documentationURL" TEXT, "growthStage" TEXT, "institution" TEXT, "language" TEXT, "scientist" TEXT, "status" TEXT, "submissionTimestamp" TEXT, "synonyms" TEXT, "additionalInfo_ID" TEXT, "externalReferences_IDs" TEXT DEFAULT '[]', "method_ID" TEXT, "ontologyReference_ID" TEXT, "scale_ID" TEXT, "trait_ID" TEXT);

--  variant
CREATE TABLE IF NOT EXISTS "variants" ("variantDbId" VARCHAR(255) PRIMARY KEY, "createdAt" TIMESTAMP, "updatedAt" TIMESTAMP, "analysis" TEXT, "ciend" TEXT, "cipos" TEXT, "created" TEXT, "end" TEXT, "filtersApplied" TEXT, "filtersFailed" TEXT, "filtersPassed" TEXT, "referenceBases" TEXT, "start" TEXT, "svlen" TEXT, "updated" TEXT, "variantNames" TEXT, "variantType" TEXT, "additionalInfo_ID" TEXT, "markerPositions_IDs" TEXT DEFAULT '[]', "alleleMatrices_IDs" TEXT DEFAULT '[]', "calls_IDs" TEXT DEFAULT '[]', "externalReferences_IDs" TEXT DEFAULT '[]', "reference_ID" TEXT, "referenceSet_ID" TEXT, "variantSet_ID" TEXT);

--  variantset
CREATE TABLE IF NOT EXISTS "variantsets" ("variantSetDbId" VARCHAR(255) PRIMARY KEY, "createdAt" TIMESTAMP, "updatedAt" TIMESTAMP, "callSetCount" TEXT, "variantCount" TEXT, "variantSetName" TEXT, "additionalInfo_ID" TEXT, "alleleMatrices_IDs" TEXT DEFAULT '[]', "calls_IDs" TEXT DEFAULT '[]', "callSets_IDs" TEXT DEFAULT '[]', "analysis_IDs" TEXT DEFAULT '[]', "availableFormats_IDs" TEXT DEFAULT '[]', "externalReferences_IDs" TEXT DEFAULT '[]', "metadataFields_IDs" TEXT DEFAULT '[]', "referenceSet_ID" TEXT, "study_ID" TEXT, "variants_IDs" TEXT DEFAULT '[]');

```

## Verify all tables were created

After creating the tables, verify that they exist using the `\dt` command. Make sure you are connected to the database with your user before running this command.
```
\dt
```