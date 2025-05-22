
                                                                Day1 Questions: Docker-compose

1-1 For which reason is it better to run the container with a flag -e to give the environment variables rather than put them directly in the Dockerfile?
R: Passing environment variables with `-e` at runtime keeps sensitive data out of the image and makes the container more flexible and secure.

1-2 Why do we need a volume to be attached to our postgres container?
R: We need to attach a volume to our Postgres container to ensure that all the data ( modifications) is saved outside the container, so if the container is deleted or recreated, the data remains safe and persistent.

1-3 Document your database container essentials: commands and Dockerfile.
R: • Dockerfile essentiel: Uses the  PostgreSQL 17.2- Alpine image as base, Sets environment variables to create a database (the name: db), user (usr), and password (pwd) at container startup, Copies SQL scripts into /docker-entrypoint-initdb.d/ which are automatically executed when the container is initialized for the first time
   • Important Docker Commands: (docker build -t wendy2001/myimage-2 .) to create the image build with files inside the local folder, (docker run --name my-postgres-2 -e POSTGRES_PASSWORD=wendy2001 --network app-network -d -p 9994:5000 -v my_db_data:/var/lib/postgresql/data wendy2001/myimage-2) to create a container based on ths image and a volume to save data, ( docker exec -it my-postgres-2 psql -U usr -d db) to execute the database db and do some command, so that we can check if the volume works.

1-4 Why do we need a multistage build? And explain each step of this dockerfile.
R:We use a multistage build to separate the build environment from the runtime environment, which makes the final Docker image smaller. Explication: (FROM eclipse-temurin:21-jdk-alpine AS myapp-build): Starts from a lightweight image with the JDK AS myapp-build names this stage for reuse later. (ENV MYAPP_HOME=/opt/myapp WORKDIR $MYAPP_HOME) Sets and moves to a working directory inside the container., (RUN apk add --no-cache maven) Installs Maven to build the project, (COPY pom.xml . COPY src ./src) Copies the Maven config and source code into the container, (RUN mvn package -DskipTests) Compiles the code and builds the JAR file, skipping tests, (FROM eclipse-temurin:21-jre-alpine) Starts a new lightweight image with only the Java runtime (JRE), not the full JDK, (ENV MYAPP_HOME=/opt/myapp WORKDIR $MYAPP_HOME) Same working directory as before, (COPY --from=myapp-build $MYAPP_HOME/target/*.jar $MYAPP_HOME/myapp.jar) Copies only the final JAR file from the build stage into the new image, (ENTRYPOINT ["java", "-jar", "myapp.jar"]) Runs the JAR when the container starts.

1-5 Why do we need a reverse proxy?
R: A reverse proxy is used to receive client requests and forward them to the appropriate backend server. It helps route traffic, improve security, enable load balancing, and centralize access control

1-6 Why is docker-compose so important?
R: Docker Compose is important because it allows us to define and manage multiple containers and their configurations automatically in one file. It helps to build, run, and link containers together easily with a single command. 


1-7 Document docker-compose most important commands
R:      docker-compose up : Builds, (re)creates, starts, and attaches to containers defined in the docker-compose.yml file.

        docker-compose down : Stops and removes containers, networks, and volumes created by up.

        docker-compose build : Builds or rebuilds services defined in the compose file.

        docker-compose stop : Stops running containers without removing them.

        docker-compose start : Starts existing containers that were stopped.

        docker-compose logs : Shows logs from running containers.

        docker-compose exec <service> <command> : Executes a command inside a running container of a specific service.

        docker-compose ps : Lists containers managed by Compose.

1-8 Document your docker-compose file.
R: Done 

1-9 Document your publication commands and published images in dockerhub.
R: 
        docker login -u  ( to connect to the docker hub)
        (in our case docker-compose have already build all the images and containers, so we will be concentrate inthe publish aspect)
        docker tag nom-image wendy2001/nom-image : version to add a tag 
        docker push username/nom-image:version : to push the image to docker hub



1-10 Why do we put our images into an online repo?
R:We put our images into an online repository to enable reuse across different environments and machines, and to allow other team members or users to easily access and use them as base images for their own projects.


                                                                Day2: Questions : GIT ACTIONS


2-1 What are Testcontainers?
Testcontainers are Java tools that let you use Docker containers during testing. For example, they can start a PostgreSQL container automatically when you run your tests. It is helpful to test with real services without installing them manually.

 2-2 Why do we need to use secured variables?
We use secured variables to keep secret information (like passwords or API keys) safe. Instead of writing them in your code, you store them safely in GitHub, so others can’t see or steal them.


2-3 Why did we put needs: build-and-test-backend on this job? 
We put needs: build-and-test-backend on this job to make sure the build-and-push-docker-image job only runs after the backend build and tests have successfully completed.This way, If the tests fail, the Docker image won’t be built or pushed. It ensures the pipeline runs in the right order, preventing unnecessary steps when the code isn’t ready.

2-4 For what purpose do we need to push docker images?
To make our application available remotely, on any environment.
By pushing the image to Docker Hub, we can then easily pull it to a production server, a cloud platform, or any other environment to quickly deploy and run the application, without having to rebuild the entire project.




                                Day3 : ANSILE

3-1 Document your inventory and base commands:
R: In the setup.yml file, we define the static inventory for our Ansible project. The root key all groups all the hosts, and under vars, we declare two global variables: ansible_user: admin, which specifies the SSH user used to connect to remote hosts, and ansible_ssh_private_key_file: ~/.ssh/id_rsa, which indicates the path to the private key used for passwordless authentication. Next, we create a group named prod under children to organize production hosts. In this group, we list the hosts under hosts, in this case wendyelvira.tchelongtchuidop.takima.cloud, which is the DNS name of the remote server. To test connectivity, we use the command ansible all -i inventories/setup.yml -m ping, which sends an Ansible ping to all hosts. To retrieve system information such as the Linux distribution, we run ansible all -i inventories/setup.yml -m setup -a "filter=ansible_distribution*", which uses the setup module to collect filtered system facts. If we want to uninstall Apache2, we use the command ansible all -i inventories/setup.yml -m apt -a "name=apache2 state=absent" --become, where the apt module manages packages, state=absent indicates the package should be removed, and --become elevates privileges (sudo). Finally, a minimal playbook in playbook.yml contains a single task: it disables automatic fact gathering with gather_facts: false, enables sudo privileges with become: true, and runs a task named "Test connection" which uses the ping module to check if each host is reachable. This playbook is executed with the command ansible-playbook -i inventories/setup.yml playbook.yml.

3-2 Document your playbook
R:  The main playbook file, install_docker.yml, simply declares the target hosts and includes the docker role, which encapsulates all tasks. Within the roles/docker directory, tasks/main.yml contains the full sequence of tasks to install Docker and its dependencies, such as required packages, GPG key, Docker repository, Docker CE, Python, and the Docker SDK in a virtual environment. Default variables, like the virtual environment path, are defined in defaults/main.yml to allow flexibility and overrides. Any service actions, such as restarting Docker after installation, are managed through handlers/main.yml, ensuring actions only trigger when needed.

3-3 Document your docker_container tasks configuration.

R:  The deployment process is managed through a main Ansible playbook that includes five key roles: docker, network, db, app, and proxy. The docker role installs Docker CE and its dependencies on the target hosts, ensures the Docker service is started, and installs the Python Docker SDK required for Ansible Docker modules. The network role creates a Docker network named app-network to enable inter-container communication. The db role launches a PostgreSQL container configured with environment variables (POSTGRES_USER, POSTGRES_PASSWORD, POSTGRES_DB) and connects it to the app-network. The app role deploys the backend Spring Boot container using the image wendy2001/tp-devops-simpleapi, setting environment variables for database connectivity (SPRING_DATASOURCE_URL, SPRING_DATASOURCE_USERNAME, SPRING_DATASOURCE_PASSWORD), and attaches it to the same network. Finally, the proxy role starts an Apache HTTPD container configured as a reverse proxy, forwarding requests to the backend through the shared Docker network. These roles are executed in this order within the main playbook to ensure Docker is installed, the network is created, the database container is running before the backend application and proxy containers start, thereby providing a coordinated and functional deployment of the services.

Is it really safe to deploy automatically every new image on the hub ? explain. What can I do to make it more secure?

R: Automatically deploying every new image from Docker Hub can be risky if not properly secured and controlled. to make it more secure, we should:
Use image versioning: Don’t always use :latest. Use specific tags like v1.2.3 and deploy only tested versions.

Implement approvals :Use GitHub Actions environments or protected branches to require human approval before deploying to production.

Enable image signing (Docker Content Trust): Use Docker Content Trust (DCT) to ensure only signed and trusted images are deployed.

Deploy only from trusted sources: Ensure the images are built and pushed only through your CI (not manually pushed by developers).

Scan images for vulnerabilities: Integrate security scanners like Trivy, Snyk, or Docker Hub’s vulnerability scanner into your CI pipeline.

Use secrets securely: Never hardcode secrets in images. Use Docker secrets, Ansible Vault, or environment variables from secure sources.