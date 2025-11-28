# Section 1: Getting Started

-  Docker is a container technology: A tool for creating and managing containers.
- The same container always yields the exact same application and execution behavior ! No matter where  or by who it might be executed.
-  commands
	* ```docker build . ```	
		* Create a new image. 
	* ``` docker images ```
		* List all images.
	* ``` docker rmi <id-iamge>```
		* Delete the image selected.
			* The container that uses the image should be stopped.
	* ``` docker images prune```
		* Delete all images do not using
	* ``` docker run <id-image or name-image>```
		* Execute a container.
	* ``` docker ps ```
	* ``` docker ps -a ```
		* List all docker containers. 
	*   ``` docker stop <id-container or name-container> ```
		* Stop a running container.
	* ``` docker container prune```
		* Remove all stopped containers at once.
	* ``` docker run -p 3000:80 --rm <id-image>```
		* **--rm**  - Remove the container  when it is stopped.
* #18. Images vs Containers
	* Images
		* Templates/Blueprints for containers.
		* Contains code + required tools/runtimes.
	* Containers
		* The running "unit pf software".
		* These are the running applications.
	* NOTE
		* We run image-based containers.

# Section 3: Managing data & Working with Volumes

* #36. Naming & tagging.
	* ``` docker build -t <name-image>:<tag-image> .```
	* ``` docker run -p -d --rm 3000:80 --name <name-container>```
* #43. Understanding data categories different kind of data.
* #44. Analyzing a real app.
* #45. Build & understanding the demo app.
	* ``` docker built -t feedback-node .```
	* ``` docker run -d --rm -p 3000:80 --name feedback-app feedback-node ```
* #46. Understanding the problem.
* #47. Introducing **VOLUMES**.
	* Volumes are folders on your host machine hard drive which are mounted ("made available, mapped" ) into containers.
* #48. A first, unsuccessful try.
	* ``` docker volume ls```
		* List all volumes
	* ``` docker rm <id-volume>```
	* ``` docker volume prune ```
		* Remove unused local volumes
* #49. Named volumes to the rescue.
	* ``` docker run -d -p 3000:80 --rm --name feedback-app -v feedback:/app/feedback feedback-node:volumes```
		* -**v** Create volumes  ```<name-volume>:<path>```
			* Without name, will create a anonymous volume.
* #50. Removing anonymous volumes
	* ``` docker volume rm <vol_name>``` 
	* ``` docker volume prune``` 
* #51. Getting started with bind mounts (code sharing).
* #52. Combing & merging different volumes	 
	 ```shell
		    docker run \
			 -p 3000:80 \
			 -d \
			 --rm \
			 --name feedback-app \
			 -v feedback:/app/feedback \
			 -v "$HOME/PATH:app" \
			 -v /app/node_modules feedback-node:volumes					 

			* -d   - Release the terminal.
			* --rm - Remove the unused container.
			* -v   - "$HOME/PATH"
				* Create a Bind to a real path on your host machine.
				* Allowing synchronization with changes to your code in real-time, without having to restart  the container every time you change the code. 
			* -v   - /app/node_modules
				* Create an anonymous volume with project dependencies.
		```
* #54. A NodeJS-specific adjustment: Using node mon in a container.
- #55. Volumes & bind mounts: Summary.
* #56 A look at Read-Only volumes.
* #57. Managing Docker volumes.
* #58. Using "COPY" vs Bind Mounts.
* #59.  Don't copy everything: Using ".dockerignore" file.
* #60. Adding more to the .dockerignore file.
* #61. Working with environment variable & ".env" file.
* #63.  Using build arguments (ARG).
	* ``` docker build -t feedback-node:web-app .```
	* ``` docker build -t feedback-node:dev --build-arg DEFAULT_PORT=8000 .```
		* We have two configurations for the same dockerfile.
		* **--build-arg**
			* 'DEFAULT_PORT' Is a variable created in the .env file.
* #64. Module Summary.

# Section 4: Networking  (cross-) container communications

- #66. Module Introduction
- #67. Case 1: Container to www communication.
- #68. Case 2: Container to host machine communication.
- #69. Case 3: Container to container communication.
- #70. Analyzing the demo app.
	- Star wars
	- MongoDB
	- Postman
- #71. Creating container communicating to the web(www)
- #72. Making container to host communication work.
	- host.docker.internal
		- Ip of host machine
- #73. Container to container communication a basis solution.
	- ```docker run mongo```
		- Download and running MongoDB
	- ```docker container inspect mongo```
- #74. Introducing docker networks: Elegant container to container communication.
	- ```docker run -d --rm --name mongodb --network favorites-net mongo```
	- ```docker network create favorites-net```
	- ```docker network ls```
		- List all network
- #75. How docker resolves IP addresses.
- #77. Module summary 
# Section 5: Building multi-container applications with docker
- #79. Module Introductions.
- #80. Our target app & Setup.
- #81. Dockerizing the MongoDB Service.
- #82. Dockerizing the Node App.
- #83. Moving the React SAP into a container.
	- ```-it .: interativo```
- #84. Adding docker networks for efficient cross-container communication.
- #85. Fixing MongoDB authentication errors
- #86. Adding data persistence to MongoDB with volumes.
	- Security → Password  
- #87. Volumes, Bind mounts & polishing for the NodeJS container
	- Logs
	- ENV
	- USER
	- PASSWORD
	 - ```Synchronization with the changed code```
	- dockerignore
- #88. Live source code update for the react container.
- #89. Summary.

# Section 6: Docker Compose Elegant Muti-Container orchestration

- #91. Module Introduction.
- #92. Docker- compose: What & Why ?
- #93. Creating a compose fiel.
- #94. Diving into the compose file configuration
	- Volumes
	- Environment
- #95.  Installing docker compose on Linux.
- #96.  Docker compose Up & Down.
- #97. Working with multiple containers.
	- Backend
		- Depends_on
- #98. Adding another container.
- #99. Building images & understanding container names.
	- Build.
	- Names
		- Container_name: {name}
   - #100. Module Summary.

# Section 7: Working with "Utility containers" & Executing commands in containers

- #102. Module Introduction & What are utility containers ?
- #103. Utility containers> Why you use them ?
- #104. Different ways of running command in containers.
- #105. Building a first utility container.
- #106. Utilizing ENTRYPOINT
- #107. Using docker compose.
- #108. Utility containers, permission & Linux.
- #109. Module summary.

# Section 8: A more complex setup A Laravel & PHP dokerized project

- #112. The target setup.
- #113. Adding a Nginx (Web server) container
	- Is it worth implementing Nginx in Parquet docker.
- #114. Adding a PHP container.
	- One folder to all dockerfile.
	- Github - docker - library
- #115. Adding a MySQL, container.
- #116. Adding a compose utility.
- #117. Creating a Laravel app via the composer utility container.
- #119. Launching only some docker compose services.
	- Docker-compose up -d server php mysql
- #120. Adding more utility containers.
- #121. Docker compose with and without dockerfiles.
- #122. Bind mounts and copy: When to use what.

# Section 9: Deploying docker containers

- #124. Module Introduction
- #125. From development to Production.
- #126. Deployment process & providers.
	- Docker hosting providers.
		- Google
	- ```AWS```
- #127. Getting started with an example.
- #128. Bind mounts in production.
- #129. Introducing AWS & EC2. 
- #130. Connecting to an EC2 instance
	- ```SSH```
- #131. Connecting to an EC2 Instance
	-  Amazon-linux-extras install docker		
		-  
		 ```shell
			 sudo yum update -y
			 sudo yum -y install docker
			 sudo servie docker start
			 sudo usermod -a -G docker ec2-user
			 sudo systemctl enable docker		
			 docker version
		 ```
- #132. Important: Installing Docker on a Virtual Machine
- #133. Installing docker on Linux in General.
- #134. Pushing our local image to the cloud.
- #135. Running  publishing the app on EC2.
	- Enable app to the world.
	- Out bound rules.
		- All traffic.
	- HTTP enable on EC2.
- #136. Managing & updating the container image.
- #137. Disadvantages of our current approach.
- #138. From manual deployment to managed services.
- #139. Important : **AWS, pricing and ECS**.
- #140. Deploying with AWS ECS: A managed docker container service.
# Tips
* Mounting Docker volumes with Docker Toolbox for Windows
	* https://headsigned.com/posts/mounting-docker-volumes-with-docker-toolbox-for-windows/


