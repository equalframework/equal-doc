# Deployment



The latest build of the eQual Docker image is available at https://hub.docker.com/repository/docker/cedricfrancoys/equal

Here are the steps for getting the Docker image from Docker Hub and running it.



## 1. Pull Image from Docker Hub

```
docker pull cedricfrancoys/equal:latest
```



## 2. Run the downloaded Docker Image 

```
docker run --name equal.local -p 80:8080 -d cedricfrancoys/equal:latest 
```



## 3. Access the Instance

```
docker exec -ti equal.local /bin/bash
```