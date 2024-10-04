# Docker and Stand alone Springboot Example
A short tutorial for how to containerize a stand alone spring boot application 

1. Ensure that you have the application downloaded from this repositroy. 
2. Package your Spring Boot application into a JAR file, which is the executable version of your project. To do this, navigate to the Maven tool window, then go to Lifecycle → clean → Package. This process will generate a .jar file, which can be found in the newly created target folder.

![Maven tool](https://i.ibb.co/N2J54pj/Screenshot-2024-10-04-210239.png)

![The jar file](https://i.ibb.co/njj468x/Screenshot-2024-10-04-210322.png)

3. Make sure that the Docker desktop(Docker Engine) is running. by checking the status, it should say: Engine Running.

![The jar file](https://i.ibb.co/FqhCDcj/Screenshot-2024-10-04-213032.png)

4. Next, create a Docker image for your application by adding a Dockerfile to the root of your project. The Dockerfile is a file with no extension, simply named Dockerfile. It contains instructions for building the Docker image. Here is the code that you will have in the file, with an explanation of what each line is representing. 
```
# Use a base image with JDK, we need it to be able to run our spring boot application
FROM eclipse-temurin:21-jdk
# Set the working directory in the container
WORKDIR /src
# Copy the JAR file from the target folder to the container
COPY target/dockerandspring-0.0.1-SNAPSHOT.jar /src/dockerandspring.jar
# Expose the port your Spring Boot app runs on
EXPOSE 8080
# Command to run the JAR file
ENTRYPOINT ["java", "-jar", "dockerandspring.jar"]
```
5. Now, open the terminal, and run the command ``` docker build -t docker-and-spring . ``` to build the image of this application
Note:
You can use the terminal of your PC, or simply the one integrated in intellij, as in the screenshot below. You can open new terminals as you need by clicking on the + sign.  

![Maven tool](https://i.ibb.co/DwDnRrn/Screenshot-2024-10-04-210520.png)

6. Now the image should be build, you can double check that, either by accessing the Docker desktop, or running the command ``` docker images ``` in the terminal. 

![Image is built](https://i.ibb.co/yp9ydCD/Screenshot-2024-10-04-210624.png)

7. Make a continer to run the image in this conatiner, by running the command ``` docker run -d -p 8080:8080 docker-and-spring  ``` in the terminal.
   
  docker run: This part of the command tells Docker to create and start a new container.

  -d: This flag runs the container in detached mode, meaning the container will run in the background without blocking the terminal, so you can continue using your terminal while the application runs.

  -p 8080:8080: This maps port 8080 on your local machine to port 8080 inside the Docker container. Essentially, this means that if you access http://localhost:8080 on your machine, it will be forwarded to the     container’s port 8080, where your Spring Boot application is running.

  docker-and-spring: This is the name of the Docker image for your Spring Boot application. Docker will use this image to create the container.

  After excuting this command, you the container be uup and running and you can access the end point thorugh the container.
  
![Image is built](https://i.ibb.co/JFJYyTm/Screenshot-2024-10-04-211007.png)

8. Now you can access the endpoint.

![Image is built](https://i.ibb.co/55RRGtc/Screenshot-2024-10-04-211041.png)

