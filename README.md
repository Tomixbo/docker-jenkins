# Purpose of This Repository

The goal of this repository is to set up a Jenkins container capable of executing Docker commands. This setup allows the creation of agents using Docker images within a Jenkins pipeline.

This guide is inspired by the official Jenkins documentation: [Jenkins Pipeline - Docker](https://www.jenkins.io/doc/book/installing/docker/).

---

## Installation Steps

### 1. Build a Custom Jenkins Image with Docker CLI
- Extend the `jenkins/jenkins` image to include the `docker-cli` by creating a custom `Dockerfile`.
- Build the new image using the following command:
```bash
docker build -t myjenkins .
```

### 2. Run the Jenkins Container
Run the container with CLI:
```bash
docker run --name jenkins --restart=on-failure --detach ^
  --privileged ^
  --env DOCKER_HOST=tcp://host.docker.internal:2375 ^
  --volume jenkins-data:/var/jenkins_home ^
  --publish 8080:8080 --publish 50000:50000 myjenkins
```
Alternative with docker compose:
```bash
docker compose up -d
```

### 3. Host-Specific Configuration
- **For Windows Users**: Enable **Expose daemon on tcp://localhost:2375 without TLS** in Docker Desktop -> General settings.
- **For Linux Users**: 
    1. Create `daemon.json` file in `/etc/docker`:
    ```bash
    {"hosts": ["tcp://0.0.0.0:2375", "unix:///var/run/docker.sock"]}
    ```
    2. Add `/etc/systemd/system/docker.service.d/override.conf`
    ```bash
    [Service]
    ExecStart=
    ExecStart=/usr/bin/dockerd
    ```

    3. Reload the systemd daemon:
    ```bash
    systemctl daemon-reload
    ```     
    4. Restart docker:
    ```bash
    systemctl restart docker.service
    ```      

### 4. Access Jenkins
- Open Jenkins in your web browser: [http://localhost:8080/](http://localhost:8080/).
- Retrieve the initial administrator password using the following command:
```bash
docker exec <CONTAINER_ID_OR_NAME> cat /var/jenkins_home/secrets/initialAdminPassword
```

### 5. Initial Configuration
- Log in to Jenkins using the retrieved password.
- Create the first superuser account.
- Install the required plugins. Make sure to install the **Docker** plugin, as it will automatically include its dependencies.

### 6. [Optional] Create a Docker Cloud
- In the Jenkins administration settings, you can create a new cloud of type **Docker** for further customization.

---

## Enjoy Your Setup!
You can now execute Docker commands directly within your Jenkins job pipelines. 🎉

---
