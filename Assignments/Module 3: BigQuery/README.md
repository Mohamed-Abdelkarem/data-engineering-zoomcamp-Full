## **Question 1**
What is count of records for the **2024 Yellow Taxi Data**?

- 65,623  
- 840,402  
- **20,332,093 ← correct**  
- 85,431,289  

### **Answer 1**

**Total records: 20,332,093**

````sql
SELECT COUNT(*) 
FROM `bigqrery-demo.zoomcamp.external_yellow_tripdata`;
````

---

## **Question 2**
Write a query to count the distinct number of **PULocationIDs** for the entire dataset on both the tables.  
What is the estimated amount of data that will be read when this query is executed on the External Table and the Table?

- 18.82 MB for the External Table and 47.60 MB for the Materialized Table  
- **0 MB for the External Table and 155.12 MB for the Materialized Table ← correct**  
- 2.14 GB for the External Table and 0MB for the Materialized Table  
- 0 MB for the External Table and 0MB for the Materialized Table  

### **Answer 2**

````sql
-- External Table
SELECT COUNT(DISTINCT PULocationID)
FROM `bigqrery-demo.zoomcamp.external_yellow_tripdata`;

-- Native Table
SELECT COUNT(DISTINCT PULocationID)
FROM `bigqrery-demo.zoomcamp.yellow_tripdata_native`;
````

---

## **Question 3**
Write a query to retrieve the **PULocationID** from the table (not the external table) in BigQuery.  
Now write a query to retrieve the **PULocationID** and **DOLocationID** on the same table.  
Why are the estimated number of Bytes different?

- **BigQuery is a columnar database, and it only scans the specific columns requested in the query. Querying two columns requires reading more data than querying one column. ← correct**  
- BigQuery duplicates data across multiple storage partitions  
- BigQuery automatically caches the first queried column  
- BigQuery performs an implicit join operation  

### **Answer 3**

````sql
SELECT PULocationID
FROM `bigqrery-demo.zoomcamp.yellow_tripdata_native`;

SELECT PULocationID, DOLocationID
FROM `bigqrery-demo.zoomcamp.yellow_tripdata_native`;
````

---

## **Question 4**
How many records have a **fare_amount = 0**?

- 128,210  
- 546,578  
- 20,188,016  
- **8,333 ← correct**  

### **Answer 4**

````sql
SELECT COUNT(*) 
FROM `bigqrery-demo.zoomcamp.yellow_tripdata_native`
WHERE fare_amount = 0;
````

---

## **Question 5**
What is the best strategy to make an optimized table in BigQuery if your query will always filter based on **tpep_dropoff_datetime** and order the results by **VendorID**?

- **Partition by tpep_dropoff_datetime and Cluster on VendorID ← correct**  
- Cluster on by tpep_dropoff_datetime and Cluster on VendorID  
- Cluster on tpep_dropoff_datetime Partition by VendorID  
- Partition by tpep_dropoff_datetime and Partition by VendorID  

### **Answer 5**

````sql
CREATE OR REPLACE TABLE `bigqrery-demo.zoomcamp.yellow_tripdata_partitioned`
PARTITION BY DATE(tpep_dropoff_datetime)
CLUSTER BY VendorID AS
SELECT *
FROM `bigqrery-demo.zoomcamp.yellow_tripdata_native`;
````

---

## **Question 6**
Write a query to retrieve the distinct **VendorIDs** between **2024-03-01** and **2024-03-15** (inclusive).  
Use the materialized table first, then the partitioned table. What are the estimated bytes processed?

- 12.47 MB for non-partitioned table and 326.42 MB for the partitioned table  
- **310.24 MB for non-partitioned table and 26.84 MB for the partitioned table ← correct**  
- 5.87 MB for non-partitioned table and 0 MB for the partitioned table  
- 310.31 MB for non-partitioned table and 285.64 MB for the partitioned table  

### **Answer 6**

Partitioning limits the scan to only the relevant date partitions.

````sql
SELECT DISTINCT VendorID
FROM `bigqrery-demo.zoomcamp.yellow_tripdata_partitioned`
WHERE DATE(tpep_dropoff_datetime) BETWEEN '2024-03-01' AND '2024-03-15';
````

---

## **Question 7**
Where is the data stored in the **External Table** you created?

- BigQuery  
- Container Registry  
- **GCP Bucket ← correct**  
- Big Table  

### **Answer 7**

External tables reference files stored outside BigQuery, typically in **Google Cloud Storage (GCS)**.
and I specified it while creating the external table here:
```
CREATE OR REPLACE EXTERNAL TABLE `bigqrery-demo.zoomcamp.external_yellow_tripdata`
OPTIONS (
  format = 'PARQUET',
  uris = ['gs://zoomcamp-module3-yellow-taxi/yellow_tripdata_2024-*.parquet']
);
```

---

## **Question 8**
It is best practice in BigQuery to always cluster your data:

- True  
- **False ← correct**  

### **Answer 8**

Because clustering only improves performance when queries filter or sort by the clustered columns; otherwise it adds cost with no benefit.

---

## **Question 9**
No Points: Write a `SELECT COUNT(*)` query FROM the materialized table you created.  
How many bytes does it estimate will be read? Why?

### **Answer 9**

````sql
SELECT COUNT(*)
FROM `bigqrery-demo.zoomcamp.yellow_tripdata_native`;
````

**Estimated bytes read: 0 MB**

BigQuery can answer COUNT(*) from table metadata without scanning any columns.
