# Kestra + Taxi Data Assignment

---

## **Question 1**
Within the execution for **Yellow Taxi data** for the year **2020** and month **12**, what is the **uncompressed file size** (i.e. the output file `yellow_tripdata_2020-12.csv` of the extract task)?

- 128.3 MiB
- **134.5 MiB ← correct**
- 364.7 MiB
- 692.6 MiB

### **Answer 1**

can be found in the "Buckets" list in the GCS:

**134.5 MiB**

---

## **Question 2**
What is the rendered value of the variable **`file`** when:
- `taxi = green`
- `year = 2020`
- `month = 04`

during execution?

- `{{inputs.taxi}}_tripdata_{{inputs.year}}-{{inputs.month}}.csv`
- **green_tripdata_2020-04.csv ← correct**
- green_tripdata_04_2020.csv
- green_tripdata_2020.csv

### **Answer 2**

Rendered value:

````text
green_tripdata_2020-04.csv
````

Based on the file creation format:

````yaml
file: "{{inputs.taxi}}_tripdata_{{trigger.date | date('yyyy-MM')}}.csv"
````

---

## **Question 3**
How many rows are there for the **Yellow Taxi data** for **all CSV files in the year 2020**?

- 13,537,299
- **24,648,499 ← correct**
- 18,324,219
- 29,430,127

### **Answer 3**

**Total rows: 24,648,499**

````sql
SELECT COUNT(*)
FROM `kestra-demo-486016.zoomcamp.yellow_tripdata`
WHERE tpep_pickup_datetime >= '2020-01-01'
  AND tpep_pickup_datetime <  '2021-01-01';
````

---

## **Question 4**
How many rows are there for the **Green Taxi data** for **all CSV files in the year 2020**?

- 5,327,301
- 936,199
- **1,734,051 ← correct**
- 1,342,034

### **Answer 4**

**Total rows: 1,734,051**

````sql
SELECT COUNT(*)
FROM `kestra-demo-486016.zoomcamp.green_tripdata`
WHERE lpep_pickup_datetime >= '2020-01-01'
  AND lpep_pickup_datetime <  '2021-01-01';
````

---

## **Question 5**
How many rows are there for the **Yellow Taxi data** for the **March 2021 CSV file**?

- 1,428,092
- 706,911
- **1,925,152 ← correct**
- 2,561,031

### **Answer 5**

**Total rows: 1,925,152**

````sql
SELECT COUNT(*)
FROM `kestra-demo-486016.zoomcamp.yellow_tripdata`
WHERE TIMESTAMP_TRUNC(tpep_pickup_datetime, MONTH) = TIMESTAMP("2021-03-01");
````

---

## **Question 6**
How would you configure the **timezone to New York** in a **Schedule trigger**?

- Add a timezone property set to `EST`
- **Add a timezone property set to `America/New_York` ← correct**
- Add a timezone property set to `UTC-5`
- Add a location property set to `New_York`

### **Answer 6**

Add the following to the **Schedule trigger configuration**:

````yaml
timezone: "America/New_York"
````
