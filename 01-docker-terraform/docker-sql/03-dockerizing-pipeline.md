# Dockerizing the Pipeline

**[↑ Up](README.md)** | **[← Previous](02-virtual-environment.md)** | **[Next →](04-postgres-docker.md)**

Now let's containerize the script. Create the following `Dockerfile` file:

## Simple Dockerfile with pip

```dockerfile
# base Docker image that we will build on
FROM python:3.13.11-slim

# set up our image by installing prerequisites; pandas in this case
RUN pip install pandas pyarrow

# set up the working directory inside the container
WORKDIR /app
# copy the script to the container. 1st name is source file, 2nd is destination
COPY pipeline.py pipeline.py

# define what to do first when the container runs by default .. (docker run --entrypoint=bash) temporary override it if used.
# in this example, we will just run the script
ENTRYPOINT ["python", "pipeline.py"]
```

**Explanation:**

- `FROM`: Base image (Python 3.13)
- `RUN`: Execute commands during build
- `WORKDIR`: Set working directory
- `COPY`: Copy files into the image
- `ENTRYPOINT`: Default command to run

### Build and Run

Let's build the image:

```bash
docker build -t test:pandas .
```

* The image name will be `test` and its tag will be `pandas`. If the tag isn't specified it will default to `latest`.
* it means to use the (DOCKERFILE) in the current directory (specified with '.'). "-t" → tag the image (give it a name and optional version). test:pandas → (test): image name, (pandas): tag/label/not a dependency. 
* Running docker build -t test:pandas . **twice** rebuilds the image using cache if nothing changed (very fast), and updates the changes (if found), and the tag test:pandas is updated to point to the latest build.

We can now run the container and pass an argument to it, so that our pipeline will receive it (because we wrote ENTRYPOIN in the DOCKERFILE, we by default run the python code, so we give 'some_number' as a parameter directly):

```bash
docker run -it test:pandas some_number
```
You should get the same output you did when you ran the pipeline script by itself.

> Note: these instructions assume that `pipeline.py` and `Dockerfile` are in the same directory. The Docker commands should also be run from the same directory as these files.

or (from video) to enter the container (and not to excute the .py file by default) --> will be in teriminal inside the :/code# (the working directory)

*entrypoint: is the command that runs when a container starts (overriders the ENTRYPOINT in the DOCKERFILE)
```bash
docker run -it --entrypoint=bash --rm test:pandas
```
## Dockerfile with uv

What about uv? Let's use it instead of using pip:

```dockerfile
# Start with slim Python 3.13 image
FROM python:3.13.10-slim

# Copy uv binary from official uv docker image (multi-stage build pattern).. take (/uv) from there, and put it in (/bin/)
COPY --from=ghcr.io/astral-sh/uv:latest /uv /bin/

# Set working directory
WORKDIR /app

# Add virtual environment to PATH so we can use installed packages
# Because /app/.venv/bin is first in PATH.. any [python- pip- installed tool run inside the container] will use the virtual environment by default, unless you explicitly call a system binary.
# so no need now to write (rv run python pipeline.py (in the ENTRYPOINT))
ENV PATH="/app/.venv/bin:$PATH"

# Copy dependency files first (better layer caching).. and copy them to the current directory (WORKDIR /app)
COPY "pyproject.toml" "uv.lock" ".python-version" ./
# Install dependencies from lock file (ensures reproducible builds). "uv.lock": is automatically created and updated by uv when you add or sync dependencies.
RUN uv sync --locked

# Copy application code
COPY pipeline.py pipeline.py

# Set entry point
ENTRYPOINT ["python", "pipeline.py"]
```

**[↑ Up](README.md)** | **[← Previous](02-virtual-environment.md)** | **[Next →](04-postgres-docker.md)**
