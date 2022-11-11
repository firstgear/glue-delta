At the time of writing
- AWS Glue 3.0 supports up to Spark 3.1 ([AWS Glue release notes](https://docs.aws.amazon.com/glue/latest/dg/release-notes.html))
- OSS Delta Lake 1.0.1 released for Spark 3.1 ([Delta Lake release notes](https://github.com/delta-io/delta/releases))

# What works ?

AWS managed serverless spark service called AWS Glue supports up to Spark 3.1 and is compatible with OSS Delta Lake release 1.0.1 for Spark 3.1. OSS Delta Lake 1.0.1 was released in Feb 2022. Tested and working in Spark SQL with custom OSS Delta Lake 1.0.1 connector for Spark 3.1.

# What doesn't work?

Later OSS Delta Lake releases such as 1.2.0, 1.2.1, 2.0.0, 2.1.0, 2.1.1, 2.0.1 are either released for Spark 3.2 or 3.3 which is not supported by AWS Glue.

OSS Delta Lake 1.2 brings:
- S3 multi-cluster writes
- OPTIMIZE compaction
- Table Restore
- Column Renames
- Automatic filtering on generated columns
- Data Skipping via column stats

[OSS Delta Lake 2.0](https://delta.io/blog/2022-08-02-delta-2-0-the-foundation-of-your-data-lake-is-open/) brings:
- OPTIMIZE ZORDER
- Z-Order clustering
- Idempotent writes to Delta Tables
- Dynamic Partition Overwrite
- Multi Part checkpoint writes
- Column Drops

# Howto setup Glue with Delta

1. Upload [delta-core_2.12-1.0.1.jar](https://repo1.maven.org/maven2/io/delta/delta-core_2.12/1.0.1/) to S3
2. Create custom connector/connection using that jar
3. Use class name org.apache.spark.sql.delta.sources.DeltaDataSource
4. AWS Glue > Jobs > Jupyter Notebook > Upload notebook > Create
5. Adjust initialization

```sql
%session_id_prefix delta-sql-
%glue_version 3.0
%idle_timeout 30
%connections custom_connection_101
%number_of_workers 2
%%configure 
{
  "--conf": "spark.sql.extensions=io.delta.sql.DeltaSparkSessionExtension --conf spark.sql.catalog.spark_catalog=org.apache.spark.sql.delta.catalog.DeltaCatalog"
}
```

6. Run notebook