
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