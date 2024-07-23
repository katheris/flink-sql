# Examples Data Generator

This module contains an application that generates Kafka records for the examples 
listed in the `examples` module.
It provides the option to structure the data as CSVs or as an Avro record.

## Adding new data sources

To add a new data source:
1. Add a new class in the `com.github.streamshub.kafka.data.generator.examples package` 
   that implements `com.github.streamshub.kafka.data.generator.Data`.
2. Implement the required methods:
   1. `topic()` returns the name of the topic the data should be sent to.
   2. `schema()` returns an Avro schema (leave this as `null` for now).
   3. `generate()` returns a single record.
      When producing record values structured as CSVs, only the values in the Map are used.
      When producing Avro records both the key and value in the Map are used.
3. Create a file in the `src/main/resources` directory with the `.avsc` extension containing an
   Avro schema for the data.
4. Build the project using `mvn package -pl examples-data-generator`.
   This generates a Java class based on the schema and places it in `com.github.streamshub.kafka.data.generator.schema`.
5. Update the `schema()` method in your `Data` class to return the `SCHEMA$` field from the generated class.
6. Update the `Main` class to add a new case for your new `Data` class.
7. Include the String you provided in the switch statement in the `DATA` env var when running the data generator app to 
   get it to instantiate your new `Data` class.