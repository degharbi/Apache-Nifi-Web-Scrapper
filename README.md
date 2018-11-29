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
CONTAINER ID  IMAGE COMMAND CREATED STATUS  PORTS NAMES
1e0d6d5e1781  apache/nifi:latest   "../scripts/start.sh"   10 hours ago        Up 10 hours         8443/tcp, 
0.0.0.0:8080->8080/tcp, 10000/tcp   nifi
CONTAINER ID        IMAGE                COMMAND                 CREATED             STATUS              PORTS                                         NAMES
1e0d6d5e1781        apache/nifi:latest   "../scripts/start.sh"   11 hours ago        Up 11 hours         8443/tcp, 0.0.0.0:8080->8080/tcp, 10000/tcp   nifi

```

So if you enter http://localhost:8080/nifi in your favorite browser you should get the nice NiFi UI.  

Remember that docker is a confined environment, so your work won't be saved on your local file system but inside the docker instance instead, if you want to store files locally you can run the docker container with a volume open between the docker container and your system with the -v flag as follows : 

```
# where '/local/file/name' represent your local project's folder
# and '/home/nifi/work' represent the docker project's folder

docker run -v [path to your local directory]:/home/nifi/scrapper --name nifi -p 8080:8080 -d apache/nifi:latest
```

If you want to enter into the container in bash mode:

```
# CONTAINER ID is the id you get by running docker ps
docker exec -it [CONTAINER ID] bash
```
Now you should see the directory ```/home/nifi/scrapper``` you specified in the container instance, so any work stored in this directory is stored in the local directory attached 


## NiFi UI
Familiarze with the NiFi simple UI here https://nifi.apache.org/docs/nifi-docs/html/getting-started.html

## The scrapping
Now that our NiFi is up and running, let's do some scrapping.

####First : getting the html source code
- Grab the ```invokeHTTP``` processor
- Check all the Automatically Terminate Relationships boxes on the settings tab
- Select the running time and the yield time 
- Define a URL to scrape in the properties tab, for example https://fr.quora.com/search?q=nifi then apply

We can just run the processor for a few seconds and check data by left clicking the processor and checking View data provenance, but we want to store the html page in a file for further processing right ? 

####Second : saving the html source code to a file
Grab the ```PutFile``` processor
Checking the Succes or failure relationship, for now just check sucess
Properties tab : specify a folder to store the file and apply

####Third : setting a connection between the processors
- Just drag and drop the arrow from the first processor to the second
- Choose the desired relationship, Original and Response for now

That's it !
Now we can run and watch the bytes accumulating in and out. So let's check our new files.

## Results 
To check your files in NifI run the View Data Provenance command then look for desired output, otherwise let's go to our docker container files and look for the newly created files. 

so run 
```
# CONTAINER ID is the id you get by running docker ps
docker exec -it [CONTAINER ID] bash
```
then go to 
```
/home/nifi/scrapper/
```
and you should see some files, so just browse one by running 
```
cat <file name>
```
and you should have the html source code of the requested page !!

You may wonder why there are many files created ? well if you specified 0 seconds in the settings menu, the processor will send requests non stop to the requested page and store the result everytime. So be nice and specify a long duration like 60 seconds and stop your flow as soon as you have some data flowing. 

That's it for now, visit this repo to get updates about this subject. 

I'll be adding in the future :
- Data processing 
- Pyhton scripts in the flow
- Database storage 
- Setting up a cluster of nodes working in parallel 
- Kafka, Elastic connectors
- Machine learning implementation .... So exciting !!

Stay tuned... 




