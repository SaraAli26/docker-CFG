
# CFG Docker Example: Implementing MySQL with Library Service

## Overview
This example demonstrates how to connect a **Library Service project** to a **MySQL database** using Docker. The Library Service will send data to the database via endpoints.

### Example Flow
The process consists of three parts:
1. **Creating a MySQL Image**  
2. **Creating an Image for the Library Service Application**  
3. **Running the `docker-compose.yml` file** to allow the two images to communicate.

## Steps to Implement the Example

### Part 1: Download the MySQL Image
1. Download the MySQL image using Docker Desktop or the terminal. 
   - **Terminal**: Run the command:  
     ```bash
     docker pull mysql
     ```
   - **Docker Desktop**: Search for "MySQL" in the search bar and download the verified image.
  
   ![mysql image using docker desktop](https://i.ibb.co/R6bmpbv/Screenshot-2024-10-05-020241.png)

2. Verify the image installation using:  
   ```bash
   docker images
   ```
   Or check the **Images** tab in Docker Desktop.
   
---

### Part 2: Create an Image for the Library Service App
1. **Create the JAR file** for your application, following the steps from the **dockerandspring** tutorial.
   
   **Note**:  
    You might encounter some issues during this step, with the most common being related to the database connection. To save time and avoid diving into complex workarounds, ensure that the application.yml or     application.properties files contain the correct database configuration used for this app previously. Confirm that the application can successfully connect to the same database and that the MySQL server is running.

Alternatively, you could mock the dependencies to bypass the database connection, but this can be time-consuming. That's why it's recommended to follow the first approach for a easier process.

2. **Test the JAR file** by running:  
   ```bash
   java -jar C:\Users\...\Code\library-service\target\library-service-0.0.1-SNAPSHOT.jar
   ```
**Note**:
   Make sure to replace the path with the correct one from your computer to the .jar file.
   
   If everything is correct, the program should run, and you’ll be able to access your endpoints through a browser or Postman without needing to run the program from IntelliJ. Since you're now running it from the executable file, once you're done testing, you can safely close it.
   
---

### Part 3: Build the Docker Image for the Library Service
1. Create the **Dockerfile** for your project and place it in the root directory. The content of the file should be:
   ```
    FROM eclipse-temurin:21-jdk
    WORKDIR /src
    COPY target/library-service-0.0.1-SNAPSHOT.jar /src/library-service.jar
    EXPOSE 8080
    ENTRYPOINT ["java", "-jar", "library-service.jar"]
   ```

2. Build the Docker image using the command:
   ```bash
   docker build -t library-service .
   ```
- **docker build**: This command is used to create a Docker image from a Dockerfile.
- **-t library-service**: The `-t` flag allows you to name the image you’re creating. In this case, the image will be named `library-service`.
- **.**: This specifies the location of the Dockerfile and other necessary files for building the image. The `.` refers to the current directory, so Docker will look for the Dockerfile in the current folder where you run the command.

3. Check Docker Desktop to ensure the image has been created successfully. or you can use the command ``` docker images```

---

### Part 4: Running the two Services (MySql and Library service) with Docker Compose
1. Create the `docker-compose.yml` file in the root directory of the project.
2. Add the following code to the file:
   
   ```yaml
    version: '3.8'  # Specifies the version of Docker Compose syntax to use.

    services:
      db:  # Defines the database service for MySQL.
        image: mysql:latest  # Uses the latest MySQL image from Docker Hub.
        ports:
          - "3306:3306"  # Maps port 3306 on the host to port 3306 in the container, allowing external access to the MySQL server.
        environment:
          MYSQL_DATABASE: library  # Sets the name of the default database that will be created.
          MYSQL_ROOT_PASSWORD: 12345aA*  # Defines the root password for the MySQL instance.
        volumes:
          - D:\CFG\library-service-main\src\main\resources\database:/docker-entrypoint-initdb.d
          # Maps a local directory on your Windows machine to the /docker-entrypoint-initdb.d directory in the container.
          # This allows you to initialize the database with SQL scripts or data on container startup.
          # Ensure the path (D:\CFG\...) is absolute and correct for your Windows system.
    
      library-service:  # Defines the Spring Boot application service.
        image: df789035dd9cb92d05095849b77fdc979ebcb03297e756fc605dff4f381a0ec6  # Uses the image for the Spring Boot application.
        depends_on:
          - db  # Ensures that the database service is started before this application.
        ports:
          - "8080:8080"  # Maps port 8080 on the host to port 8080 in the container for the Spring Boot application.
        environment:
          SPRING_DATASOURCE_URL: jdbc:mysql://db:3306/library  # Specifies the JDBC URL to connect to the MySQL database running in the 'db' container.
          SPRING_DATASOURCE_DRIVER_CLASS_NAME: com.mysql.cj.jdbc.Driver  # Sets the MySQL JDBC driver class.
          SPRING_DATASOURCE_USERNAME: root  # Username for connecting to the MySQL database.
          SPRING_DATASOURCE_PASSWORD: 12345aA*  # Password for connecting to the MySQL database.
   ```

3. To get the image ID (The id that you will be using to define the library service image)run the command, and copy it form the result:
   
   ```bash
   docker images
   ```
   Or simply, you can get it in Docker Desktop:
     
   ![(image-placeholder-docker-id.png)](https://i.ibb.co/yVTvK1V/Screenshot-2024-10-04-232520.png)

**Note**:
For the image value, you are adding the ID of the library-service image. When you copy it, you will get something like this:
```sha256:86360ae467e0d31e091ff44125da25bba9a59d6edbacdfa6f653eb6c8c2f8dfe```
Remove the "sha256:" part so it becomes:
```86360ae467e0d31e091ff44125da25bba9a59d6edbacdfa6f653eb6c8c2f8dfe```

4. Run the services with the command:
   
   ```bash
   docker-compose up
   ```

   If successful, you should see something like this:  
   ![(image-placeholder-compose-success.png)](https://i.ibb.co/wdJqcTc/Screenshot-2024-10-05-021320.png)

6. Now access the endpoints, and the services should be running without needing IntelliJ or MySQL Workbench.  
   ![(image-placeholder-endpoints.png)](https://i.ibb.co/zs2WTdh/Screenshot-2024-10-04-234516.png)


