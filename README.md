# Nifi_Scraper

This is a simple application on setting up a web scrapper with Apache NiFi. 

## Apache NiFi
https://nifi.apache.org/

## Docker 

### Install Docker
For an easy and quick implementation of NiFi, Docker is a good way to get started without the hastle of installing the NiFi and Java environment. 

For installing Docker take a look at the docker quickstart https://docs.docker.com/v17.12/install/ 

### Pulling NiFi docker image
After installing Docker, lets pull the NiFi official image from the docker hub https://hub.docker.com/r/apache/nifi/ by tapping the following command :

```
docker pull apache/nifi
```

### Running the NiFi container

First make sure that docker service is running, for example by running the ```docker ps``` command, if not run the following command in sudo mode to start docker :
``` service docker start ```

The minimum to run a NiFi instance is as follows:

```
docker run --name nifi -p 8080:8080 -d apache/nifi:latest
```

This will provide a running instance, exposing the instance UI to the host system on at port 8080,viewable at http://localhost:8080/nifi.

For more options on the environment variables to change the NiFi communication ports and hostname check the NiFi docker image page https://hub.docker.com/r/apache/nifi/

Now if you run ``` docker ps``` you should see the NiFi container up and running, it looks something like : 
```
[root@ip]# docker ps
CONTAINER ID        IMAGE                COMMAND                 CREATED             STATUS              PORTS                                         NAMES
1e0d6d5e1781        apache/nifi:latest   "../scripts/start.sh"   10 hours ago        Up 10 hours         8443/tcp, 
0.0.0.0:8080->8080/tcp, 10000/tcp   nifi
```

So if you enter http://localhost:8080/nifi in your favorite browser you should get the nice NiFi UI.  

Remember that docker is a confined environment, so your work won't be saved on your local file system but inside the docker instance instead, if you want to store files locally you can run the docker container with a volume open between the docker container and your system with the -v flag as follows : 

```
# where '/local/file/name' represent your local project's folder
# and '/home/nifi/work' represent the docker project's folder

docker run -v /local/file/name:/home/nifi/work --name nifi -p 8080:8080 -d apache/nifi:latest
```

If you want to enter into the container in bash mode:

```
# CONTAINER ID is the id you get by running docker ps
docker exec -it [CONTAINER ID] bash
```





