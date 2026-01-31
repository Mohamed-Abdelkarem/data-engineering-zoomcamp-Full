# pgAdmin - Database Management Tool

**[↑ Up](README.md)** | **[← Previous](06-ingestion-script.md)** | **[Next →](08-dockerizing-ingestion.md)**

`pgcli` is a handy tool but it's cumbersome to use for complex queries and database management. [`pgAdmin` is a web-based tool](https://www.pgadmin.org/) that makes it more convenient to access and manage our databases --> **target:** manage your Postgres database using a web UI (pgAdmin) instead of typing SQL commands in the terminal

It's possible to run pgAdmin as a container along with the Postgres container, but both containers will have to be in the same _virtual network_ so that they can find each other.

### How it works (high level – pgAdmin vs pgcli)

* Postgres runs in one Docker container (the database itself)
--> (same database pgcli connects to)

* pgAdmin runs in a separate Docker container (a web-based management tool)
--> (pgcli runs directly in your terminal, not in a container)

* A Docker network connects the two containers so they can communicate by name
--> (pgcli connects via localhost and exposed ports instead)

* You open pgAdmin in your browser and connect to Postgres using the container name
--> (pgcli connects from the terminal using host/port credentials)

* pgAdmin lets you browse, query, and manage the database visually
--> (pgcli is text-only and query-focused)

## Run pgAdmin Container

```bash
docker run -it \
  -e PGADMIN_DEFAULT_EMAIL="admin@admin.com" \
  -e PGADMIN_DEFAULT_PASSWORD="root" \
  -v pgadmin_data:/var/lib/pgadmin \
  -p 8085:80 \
  dpage/pgadmin4
```

The `-v pgadmin_data:/var/lib/pgadmin` volume mapping saves pgAdmin settings (server connections, preferences) so you don't have to reconfigure it every time you restart the container.

### Parameters Explained

* The container needs 2 environment variables: a login email and a password. We use `admin@admin.com` and `root` in this example.
* pgAdmin is a web app and its default port is 80; we map it to 8085 in our localhost to avoid any possible conflicts.
* The actual image name is `dpage/pgadmin4`.

**Note:** This won't work yet because pgAdmin can't see the PostgreSQL container. They need to be on the same Docker network!

## Docker Networks

Let's create a virtual Docker network called `pg-network`:

```bash
docker network create pg-network
```

> You can remove the network later with the command `docker network rm pg-network`. You can look at the existing networks with `docker network ls`.

### Run Containers on the Same Network

Stop both containers and re-run them with the network configuration:

```bash
# Run PostgreSQL on the network
docker run -it \
  -e POSTGRES_USER="root" \
  -e POSTGRES_PASSWORD="root" \
  -e POSTGRES_DB="ny_taxi" \
  -v ny_taxi_postgres_data:/var/lib/postgresql \
  -p 5432:5432 \
  --network=pg-network \
  --name pgdatabase \
  postgres:18

# In another terminal, run pgAdmin on the same network
docker run -it \
  -e PGADMIN_DEFAULT_EMAIL="admin@admin.com" \
  -e PGADMIN_DEFAULT_PASSWORD="root" \
  -v pgadmin_data:/var/lib/pgadmin \
  -p 8085:80 \
  --network=pg-network \
  --name pgadmin \
  dpage/pgadmin4
```

* Just like with the Postgres container, we specify a network and a name for pgAdmin.
* The container names (`pgdatabase` and `pgadmin`) allow the containers to find each other within the network.

  
* **What the volumes do:**
  
-`ny_taxi_postgres_data` (in pgdatabase container) → keeps database data safe

-`pgadmin_data` (in pgadmin container) → saves pgAdmin settings

-You can stop containers and nothing is lost

## Connect pgAdmin to PostgreSQL

You should now be able to load pgAdmin on a web browser by browsing to `http://localhost:8085`. Use the same email and password you used for running the container to log in.

1. Open browser and go to `http://localhost:8085`
2. Login with email: `admin@admin.com`, password: `root`
3. Right-click "Servers" → Register → Server
4. Configure:
   - **General tab**: Name: `Local Docker`
   - **Connection tab**:
     - Host: `pgdatabase` (the container name)
     - Port: `5432`
     - Username: `root`
     - Password: `root`
5. Save

Now you can explore the database using the pgAdmin interface!

**[↑ Up](README.md)** | **[← Previous](06-ingestion-script.md)** | **[Next →](08-dockerizing-ingestion.md)**
