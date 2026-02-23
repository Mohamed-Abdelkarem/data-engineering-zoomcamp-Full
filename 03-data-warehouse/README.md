- [Slides](https://docs.google.com/presentation/d/1a3ZoBAXFk8-EhUsd7rAZd-5p_HpltkzSeujjRGB2TAI/edit?usp=sharing)  
- [Big Query basic SQL](big_query.sql)

# Videos

## Data Warehouse

- Data Warehouse and BigQuery

[![](https://markdown-videos-api.jorgenkh.no/youtube/jrHljAoD6nM)](https://youtu.be/jrHljAoD6nM&list=PL3MmuxUbc_hJed7dXYoJw8DoCuVHhGEQb&index=34)

- When creating an **external table**, BigQuery can’t know the number of rows or estimate query cost in advance, because the data is not stored inside BigQuery.
The data lives in an external system, such as Google Cloud Storage.

- **Partitioned table**

  - Data is split into **separate partitions** (usually by date).

  - Queries scan only relevant partitions, not the whole table.

  - Used mainly to reduce scanned data and cost.

  - Common for time-based data (e.g. pickup_date).

- **Clustered table**

  - Data inside the table is **sorted** by one or more columns.

  - Helps BigQuery filter faster within partitions or tables.

  - Best for columns often used in WHERE, GROUP BY, or JOIN.

```
CREATE OR REPLACE TABLE `kestra-demo-486016.zoomcamp.yellow_tripdata_partitioned_clustered`
PARTITION BY DATE(tpep_pickup_datetime)
CLUSTER BY VendorID AS
SELECT * FROM `kestra-demo-486016.zoomcamp.external_yellow_tripdata`;
````
- you can view/confirm these details after creating the table from:
- (the explorer --> project name "kestra-demo-486016" --> dateset "zoomcamp" --> table "yellow_tripdata_partitioned_clustered" --> details --> "Partitioned on field" & "Partitioned on field")

## :movie_camera: Partitioning and clustering

- Partitioning vs Clustering

[![](https://markdown-videos-api.jorgenkh.no/youtube/-CqXf7vhhDs)](https://youtu.be/-CqXf7vhhDs?si=p1sYQCAs8dAa7jIm&t=193&list=PL3MmuxUbc_hJed7dXYoJw8DoCuVHhGEQb&index=35)

- Partitioning:
  - you can create partitions based on [days "default" - hours - months - years].
  - The number of partitions "limit" is 4000.

## :movie_camera: Best practices

[![](https://markdown-videos-api.jorgenkh.no/youtube/k81mLJVX08w)](https://youtu.be/k81mLJVX08w&list=PL3MmuxUbc_hJed7dXYoJw8DoCuVHhGEQb&index=36)

- Cost reduction
  - Avoid SELECT *
  - Price your queries before running them
  - Use clustered or partitioned tables
  - Use streaming inserts with caution
  - Materialize query results in stages
 
- Query performance
  - Filter on partitioned columns
  - Denormalizing data
  - Use nested or repeated columns
  - Use external data sources appropriately
  - Don't use it, in case u want a high query performance
  - Reduce data before using a JOIN
  - Do not treat WITH clauses as prepared statements
  - Avoid oversharding tables
  - Optimize your join patterns As a best practice, place the table with the largest number of rows first, followed by the table with the fewest rows, and then place the remaining tables by decreasing size.

## :movie_camera: Internals of BigQuery

[![](https://markdown-videos-api.jorgenkh.no/youtube/eduHi1inM4s)](https://youtu.be/eduHi1inM4s&list=PL3MmuxUbc_hJed7dXYoJw8DoCuVHhGEQb&index=37)

<img width="315" height="355" alt="image" src="https://github.com/user-attachments/assets/3b2600dd-dcf0-4f80-b8a1-224ab83cfe43" />

Purpose: You don’t need to know BigQuery internals to use it effectively, but understanding them helps when building advanced data products.

1. Storage Layer – Colossus
  - BigQuery stores data in Colossus, a cheap, columnar storage system.
  - Separation of storage and compute reduces costs:
  - Storage is cheap.
  - Most cost comes from compute (query execution).

2. Network Layer – Jupiter Network
  - Challenge: storage and compute are on different hardware.
  - Solution: Jupiter network inside Google data centers.
  - Provides ~1 TB/s network speed, enabling fast communication between compute and storage.

3. Query Execution – Dremel
  - BigQuery uses Dremel as its query engine.
  - Queries are broken into a tree structure:
  - Root server receives query.
  - Divides into smaller subqueries.
  - Mixers further split queries.
  - Leaf nodes fetch data from Colossus, process it, and return results upward.
  - This distributed execution makes BigQuery extremely fast.

4. Storage Format – Column-Oriented
  - Compared to row-oriented (like CSV), column-oriented storage is better for:
  - Aggregations on specific columns.
  - Queries that only need a subset of columns.
  - BigQuery uses columnar storage to optimize performance.

5. Example Query Flow
  - Query: SELECT a, COUNT(b) FROM table GROUP BY a.
  - Root server rewrites into smaller queries.
  - Leaf nodes execute on subsets of data.
  - Results flow back through mixers → root server → final aggregated output.

6. Why BigQuery is Fast
  - Distributed workers handle queries in parallel.
  - Avoids bottlenecks of single-node execution.
  - Scales efficiently with large datasets.

## Advanced topics

### :movie_camera: Machine Learning in Big Query

[![](https://markdown-videos-api.jorgenkh.no/youtube/B-WtpB0PuG4)](https://youtu.be/B-WtpB0PuG4&list=PL3MmuxUbc_hJed7dXYoJw8DoCuVHhGEQb&index=34)

* [SQL for ML in BigQuery](big_query_ml.sql)

**Important links**

- [BigQuery ML Tutorials](https://cloud.google.com/bigquery-ml/docs/tutorials)
- [BigQuery ML Reference Parameter](https://cloud.google.com/bigquery-ml/docs/analytics-reference-patterns)
- [Hyper Parameter tuning](https://cloud.google.com/bigquery-ml/docs/reference/standard-sql/bigqueryml-syntax-create-glm)
- [Feature preprocessing](https://cloud.google.com/bigquery-ml/docs/reference/standard-sql/bigqueryml-syntax-preprocess-overview)

### :movie_camera: Deploying Machine Learning model from BigQuery

[![](https://markdown-videos-api.jorgenkh.no/youtube/BjARzEWaznU)](https://youtu.be/BjARzEWaznU&list=PL3MmuxUbc_hJed7dXYoJw8DoCuVHhGEQb&index=39)

- [Steps to extract and deploy model with docker](extract_model.md)  



# Homework

* [2026 Homework](../cohorts/2026/03-data-warehouse/homework.md)


# Community notes

<details>
<summary>Did you take notes? You can share them here</summary>

* [Notes by Alvaro Navas](https://github.com/ziritrion/dataeng-zoomcamp/blob/main/notes/3_data_warehouse.md)
* [Isaac Kargar's blog post](https://kargarisaac.github.io/blog/data%20engineering/jupyter/2022/01/30/data-engineering-w3.html)
* [Marcos Torregrosa's blog post](https://www.n4gash.com/2023/data-engineering-zoomcamp-semana-3/) 
* [Notes by Victor Padilha](https://github.com/padilha/de-zoomcamp/tree/master/week3)
* [Notes from Xia He-Bleinagel](https://xiahe-bleinagel.com/2023/02/week-3-data-engineering-zoomcamp-notes-data-warehouse-and-bigquery/)
* [Bigger picture summary on Data Lakes, Data Warehouses, and tooling](https://medium.com/@verazabeida/zoomcamp-week-4-b8bde661bf98), by Vera
* [Notes by froukje](https://github.com/froukje/de-zoomcamp/blob/main/week_3_data_warehouse/notes/notes_week_03.md)
* [Notes by Alain Boisvert](https://github.com/boisalai/de-zoomcamp-2023/blob/main/week3.md)
* [Notes from Vincenzo Galante](https://binchentso.notion.site/Data-Talks-Club-Data-Engineering-Zoomcamp-8699af8e7ff94ec49e6f9bdec8eb69fd)
* [2024 videos transcript week3](https://drive.google.com/drive/folders/1quIiwWO-tJCruqvtlqe_Olw8nvYSmmDJ?usp=sharing) by Maria Fisher 
* [Notes by Linda](https://github.com/inner-outer-space/de-zoomcamp-2024/blob/main/3a-data-warehouse/readme.md)
* [Jonah Oliver's blog post](https://www.jonahboliver.com/blog/de-zc-w3)
* [2024 - steps to send data from Mage to GCS + creating external table](https://drive.google.com/file/d/1GIi6xnS4070a8MUlIg-ozITt485_-ePB/view?usp=drive_link) by Maria Fisher
* [2024 - mage dataloader script to load the parquet files from a remote URL and push it to Google bucket as parquet file](https://github.com/amohan601/dataengineering-zoomcamp2024/blob/main/week_3_data_warehouse/mage_scripts/green_taxi_2022_v2.py) by Anju Mohan
* [2024 - steps to send data from Mage to GCS + creating external table](https://drive.google.com/file/d/1GIi6xnS4070a8MUlIg-ozITt485_-ePB/view?usp=drive_link) by Maria Fisher 
* [Notes by HongWei](https://github.com/hwchua0209/data-engineering-zoomcamp-submission/blob/main/03-data-warehouse/README.md)
* [2025 Notes by Manuel Guerra](https://github.com/ManuelGuerra1987/data-engineering-zoomcamp-notes/blob/main/3_Data-Warehouse/README.md)
* [Notes from Horeb SEIDOU](https://spotted-hardhat-eea.notion.site/Week-3-Data-Warehouse-and-BigQuery-17c29780dc4a80c8a226f372543ae388)
* [2025 - Notes by Gabi Fonseca](https://github.com/fonsecagabriella/data_engineering/blob/main/03_data_warehouse/00_notes.md)
* [2025 Gitbook Notes Tinker0425](https://data-engineering-zoomcamp-2025-t.gitbook.io/tinker0425/module-3/introduction-to-module-3)
* [2025 Notes from Daniel Lachner](https://drive.google.com/file/d/105zjtLFi0sRqqFFgdMSCTzfcLPx2rfv4/view?usp=sharing)
* [2026 Notes from Catherine Frost](https://docs.google.com/document/d/1j3jeNnBI2fw1nq7JwEauPx2G8FybDfTqmMk7eRu0vSo/edit?tab=t.0)
* Add your notes here (above this line)

</details>
