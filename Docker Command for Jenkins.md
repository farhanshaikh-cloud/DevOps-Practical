If you're using **Docker Desktop on Windows** and **PowerShell**, follow these steps to run Jenkins in a Docker container.

---

# Step 1: Verify Docker is Running

Open **PowerShell** and run:

```powershell
docker --version
docker ps
```

If Docker is running, you'll see the version and an empty or populated container list.

---

# Step 2: Pull the Jenkins Image

Download the latest Jenkins LTS image:

```powershell
docker pull jenkins/jenkins:lts
```

Verify it:

```powershell
docker images
```

---

# Step 3: Create a Docker Volume

This stores Jenkins data permanently.

```powershell
docker volume create jenkins_home
```

Check it:

```powershell
docker volume ls
```

---

# Step 4: Run Jenkins Container

Execute:

```powershell
docker run -d `
--name jenkins `
-p 8080:8080 `
-p 50000:50000 `
-v jenkins_home:/var/jenkins_home `
jenkins/jenkins:lts
```

> **Note:** The backtick (`) is PowerShell's line continuation character. If you prefer a single line, use:

```powershell
docker run -d --name jenkins -p 8080:8080 -p 50000:50000 -v jenkins_home:/var/jenkins_home jenkins/jenkins:lts
```

---

# Step 5: Verify the Container

```powershell
docker ps
```

Example:

```
CONTAINER ID   IMAGE                  STATUS
abc123         jenkins/jenkins:lts    Up 20 seconds
```

---

# Step 6: View Jenkins Logs

```powershell
docker logs jenkins
```

To follow logs continuously:

```powershell
docker logs -f jenkins
```

---

# Step 7: Get the Initial Admin Password

Run:

```powershell
docker exec jenkins cat /var/jenkins_home/secrets/initialAdminPassword
```

Copy the password displayed.

---

# Step 8: Open Jenkins

In your browser, visit:

```
http://localhost:8080
```

Paste the password from Step 7.

---

# Step 9: Complete the Setup

* Select **Install suggested plugins**.
* Wait for the installation to finish.
* Create your first admin user.
* Start using Jenkins.

---

# Useful Docker Commands

Stop Jenkins:

```powershell
docker stop jenkins
```

Start Jenkins:

```powershell
docker start jenkins
```

Restart Jenkins:

```powershell
docker restart jenkins
```

View running containers:

```powershell
docker ps
```

View all containers:

```powershell
docker ps -a
```

Remove the container:

```powershell
docker rm -f jenkins
```

> The `jenkins_home` volume keeps your Jenkins configuration and jobs, so deleting the container does **not** delete your Jenkins data.

---

## Common Issues

**Port 8080 already in use**

Run:

```powershell
netstat -ano | findstr :8080
```

Or start Jenkins on a different host port:

```powershell
docker run -d --name jenkins -p 9090:8080 -p 50000:50000 -v jenkins_home:/var/jenkins_home jenkins/jenkins:lts
```

Then access:

```
http://localhost:9090
```

**Container exited immediately**

Check the logs:

```powershell
docker logs jenkins
```

If you encounter any errors, share the output and I can help troubleshoot them.
