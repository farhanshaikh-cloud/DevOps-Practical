
### Step 1: Check if the container exited

Run:

```bash
docker ps -a
```

If you don't see it in `docker ps`, it may have exited immediately.

### Step 2: Check the logs

Find the container ID:

```bash
docker ps -a
```

Then view its logs:

```bash
docker logs <container_id>
```

You'll likely see an error similar to:

```
Error: Database is uninitialized and password option is not specified.
You need to specify one of the following:
- MYSQL_ROOT_PASSWORD
- MYSQL_ALLOW_EMPTY_PASSWORD
- MYSQL_RANDOM_ROOT_PASSWORD
```

### Step 3: Run MySQL correctly

Use:

```bash
docker run -d \
  --name mysql-container \
  -e MYSQL_ROOT_PASSWORD=root123 \
  -p 3306:3306 \
  mysql:latest
```

Or in one line:

```bash
docker run -d --name mysql-container -e MYSQL_ROOT_PASSWORD=root123 -p 3306:3306 mysql:latest
```

### Step 4: Verify the container is running

```bash
docker ps
```

Expected output:

```
CONTAINER ID   IMAGE          STATUS         PORTS
abcd1234       mysql:latest   Up 10 seconds  0.0.0.0:3306->3306/tcp
```

### Step 5: Connect to MySQL

From your host:

```bash
docker exec -it mysql-container mysql -uroot -p
```

Enter:

```
root123
```

### If you don't see any container at all

Run these commands and share the output:

```bash
docker images
docker ps -a
docker run -d -p 3000:80 mysql:latest
docker ps -a
docker logs $(docker ps -aq | head -1)
```

Also, from an earlier message I noticed you had a port binding issue on port **2000**. If you're seeing a similar error for port **3000**, paste the exact error message and I'll help diagnose it.
