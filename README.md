# Nifi_Scrapper

This is a simple tutorial on setting up a web scrapper with using the Apache NiFi big data stack. 
I'll be using a docker container. 

## Apache NiFi
what is NiFi ? 
take a look at https://nifi.apache.org/

## Docker 

### Install Docker
For an easy and quick implementation of NiFi, Docker is a good way to get started without the hastle of installing the NiFi and Java environment. 

For installing Docker take a look at the docker quickstart https://docs.docker.com/v17.12/install/ 

### Pull NiFi docker image
After installing Docker, lets pull the NiFi official image from the docker hub https://hub.docker.com/r/apache/nifi/ by tapping the following command :

```
docker pull apache/nifi
```

### Run the NiFi container

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

#### First : getting the html source code
- Grab the ```invokeHTTP``` processor
![alt text](https://github.com/degharbi/Apache-Nifi-Web-Scrapper/blob/master/Screenshot%20from%202018-11-29%2022-26-27.png)
- Check all the Automatically Terminate Relationships boxes on the settings tab
- Select the running time and the yield time 
![qlt text](https://github.com/degharbi/Apache-Nifi-Web-Scrapper/blob/master/Screenshot%20from%202018-11-29%2022-27-38.png)
- Define a URL to scrape in the properties tab, for example https://fr.quora.com/search?q=nifi then apply

We can just run the processor for a few seconds and check data by left clicking the processor and checking View data provenance, but we want to store the html page in a file for further processing right ? 

#### Second : saving the html source code to a file
Grab the ```PutFile``` processor
Checking the Succes or failure relationship, for now just check sucess
Properties tab : specify a folder to store the file and apply

#### Third : setting a connection between the processors
- Just drag and drop the arrow from the first processor to the second
- Choose the desired relationship, Original and Response for now

That's it !
Now we can run and watch the bytes accumulating in and out. So let's check our new files.

## Results 
To check your files in NifI run the View Data Provenance command then look for desired output, click on the info icon then content and view to visualize the following by selecting hex in the menu :  
```
0x00000000	3C	21	44	4F	43	54	59	50	45	20	68	74	6D	6C	3E	3C	<!DOCTYPE html><
0x00000010	68	74	6D	6C	20	6C	61	6E	67	3D	66	72	3E	3C	68	65	html lang=fr><he
0x00000020	61	64	3E	3C	6C	69	6E	6B	20	72	65	6C	3D	27	69	63	ad><link rel='ic
0x00000030	6F	6E	27	20	68	72	65	66	3D	27	68	74	74	70	73	3A	on' href='https:
0x00000040	2F	2F	71	73	66	2E	66	73	2E	71	75	6F	72	61	63	64	//qsf.fs.quoracd
0x00000050	6E	2E	6E	65	74	2F	2D	33	2D	69	6D	61	67	65	73	2E	n.net/-3-images.
0x00000060	66	61	76	69	63	6F	6E	2E	69	63	6F	2D	32	36	2D	61	favicon.ico-26-a
0x00000070	65	37	37	62	36	33	37	62	31	65	37	65	64	32	63	2E	e77b637b1e7ed2c.
0x00000080	69	63	6F	27	20	2F	3E	3C	73	63	72	69	70	74	20	74	ico' /><script t
0x00000090	79	70	65	3D	22	74	65	78	74	2F	6A	61	76	61	73	63	ype="text/javasc
..........................
0x00000520	75	6E	63	74	69	6F	6E	28	65	29	7B	69	66	28	30	21	unction(e){if(0!
0x00000530	3D	3D	61	73	73	65	74	45	72	72	73	2E	6C	65	6E	67	==assetErrs.leng
0x00000540	74	68	29	7B	76	61	72	20	73	3D	22	61	73	73	65	74	th){var s="asset
0x00000550	73	3D	22	2B	65	6E	63	6F	64	65	55	52	49	43	6F	6D	s="+encodeURICom
0x00000560	70	6F	6E	65	6E	74	28	4A	53	4F	4E	2E	73	74	72	69	ponent(JSON.stri
0x00000570	6E	67	69	66	79	28	61	73	73	65	74	45	72	72	73	29	ngify(assetErrs)
0x00000580	29	2C	74	3D	6E	65	77	20	58	4D	4C	48	74	74	70	52	),t=new XMLHttpR
0x00000590	65	71	75	65	73	74	3B	74	2E	6F	70	65	6E	28	22	50	equest;t.open("P
0x000005A0	4F	53	54	22	2C	22	2F	61	6A	61	78	2F	6C	6F	67	5F	OST","/ajax/log_
0x000005B0	62	72	6F	77	73	65	72	5F	61	73	73	65	74	5F	6C	6F	browser_asset_lo
0x000005C0	61	64	5F	65	72	72	6F	72	5F	33	52	44	5F	50	41	52	ad_error_3RD_PAR
0x000005D0	54	59	5F	50	4F	53	54	22	2C	21	30	29	2C	74	2E	73	TY_POST",!0),t.s
0x000005E0	65	74	52	65	71	75	65	73	74	48	65	61	64	65	72	28	etRequestHeader(
0x000005F0	22	43	6F	6E	74	65	6E	74	2D	54	79	70	65	22	2C	22	"Content-Type","

```

Otherwise let's go to our docker container files and look for the newly created files.

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

## Upcoming

That's it for now, visit this repo to get updates about this subject. 

I'll be adding in the future :
- Data processing 
- Pyhton scripts in the flow
- Database storage 
- Setting up a cluster of nodes working in parallel 
- Kafka, Elastic connectors
- Machine learning implementation .... So exciting !!

Stay tuned... 




