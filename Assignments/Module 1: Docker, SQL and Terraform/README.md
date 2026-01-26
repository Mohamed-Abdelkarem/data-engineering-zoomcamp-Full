# Docker + Postgres + Taxi Data + Terraform Assignment

---

## **Question 1**  
What's the version of pip in the `python:3.13` image?

- *25.3* **← correct**
- 24.3.1
- 24.2.1
- 23.3.1

### Answer 1

    docker run -it --entrypoint bash python:3.13
    pip --version

Result: 25.3

---

## **Question 2**  
Given the docker-compose.yaml, what is the hostname and port that pgadmin should use to connect to the postgres database?

- postgres:5433
- *localhost:5432* **← correct**
- db:5433
- postgres:5432
- db:5432

### Answer 2  
Because both containers are inside the same private network (created by docker compose).. so containers communicate using their host names [postgres - pgadmin], with the port number of the destination/client container.

---

## **Question 3**  
For the trips in November 2025, how many trips had a trip_distance of less than or equal to 1 mile?

- 7,853
- *8,007* **← correct**
- 8,254
- 8,421

### Answer 3

    select count(*) from green_taxi_data
    where lpep_pickup_datetime >= '2025-11-01'
      and lpep_pickup_datetime < '2025-12-01'
      and trip_distance <= 1

---

## **Question 4**  
Which was the pick up day with the longest trip distance? Only consider trips with trip_distance less than 100 miles.

- *2025-11-14* **← correct**
- 2025-11-20
- 2025-11-23
- 2025-11-25

### Answer 4

    select date(lpep_pickup_datetime) from green_taxi_data
    where trip_distance < 100
    order by trip_distance desc
    limit 1

---

## **Question 5**  
Which was the pickup zone with the largest total_amount (sum of all trips) on November 18th, 2025?

- *East Harlem North* **← correct**
- East Harlem South
- Morningside Heights
- Forest Hills

### Answer 5

    select z."Zone"
    from green_taxi_data t join taxi_zones z on t."PULocationID" = z."LocationID"
    where date(lpep_pickup_datetime) = '2025-11-18'
    group by z."Zone"
    order by sum(total_amount) desc
    limit 1

---

## **Question 6**  
For the passengers picked up in the zone named "East Harlem North" in November 2025, which was the drop off zone that had the largest tip?

- JFK Airport
- *Yorkville West* **← correct**
- East Harlem North
- LaGuardia Airport

### Answer 6

    select z."Zone"
    from green_taxi_data t join taxi_zones z on t."DOLocationID" = z."LocationID"
    where t."PULocationID" =
    (select "LocationID" from taxi_zones where "Zone" = 'East Harlem North')
    and lpep_pickup_datetime >= '2025-11-01'
      and lpep_pickup_datetime < '2025-12-01'
    group by z."Zone"
    order by max(t.tip_amount) desc
    limit 1

---

## **Question 7**  
Which of the following sequences describes the Terraform workflow for: 1) Downloading plugins and setting up backend, 2) Generating and executing changes, 3) Removing all resources?

- terraform import, terraform apply -y, terraform destroy
- teraform init, terraform plan -auto-apply, terraform rm
- terraform init, terraform run -auto-approve, terraform destroy
- *terraform init, terraform apply -auto-approve, terraform destroy* **← correct**
- terraform import, terraform apply -y, terraform rm
