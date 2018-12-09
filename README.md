# WebLogic Docker sample with applications
This docker sample is built using the [WebLogic on Docker](https://github.com/oracle/docker-images/tree/master/OracleWebLogic) and the [12213-domain-home-in-image](https://github.com/oracle/docker-images/tree/master/OracleWebLogic/samples/12213-domain-home-in-image)

## Running the WebLogic sample
This is a quick instructions on running the weblogic servers in docker container. We will create the following servers:
* admin-server *(port 7001)*
* managed-server1 *(port 8001)*
* managed-server2 *(port 8002)*

### Prepare the configuration 
Git clone this repo or download the docker_run directory.

### Create new admin server
To start the containerized Administration Server, run:
```bash
docker run -d --name wlsadmin --hostname wlsadmin -p 7001:7001 \
      -v $PWD/docker_run:/u01/oracle/properties \
      jeejeejango/weblogic-docker-sample:12213
```
For windows users, you will need to specify the full path to the docker_run directory.

### Checking the Admin Server status
To check if the WebLogic container is running, run `docker ps` . You should see the container with name wlsadmin running. If it is not running, run `docker logs -f wlsadmin` to view the status.

### Adding Managed Servers
When wlsadmin is running, you can start the managed server 1:
```bash
docker run -d --name MS1 --link wlsadmin:wlsadmin -p 8001:8001 \
      -v $PWD/docker_run:/u01/oracle/properties \
      -e MANAGED_SERV_NAME=managed-server1 jeejeejango/weblogic-docker-sample:12213 startManagedServer.sh
```

and to start the managed server 2:
```bash
docker run -d --name MS2 --link wlsadmin:wlsadmin -p 8002:8001 \
      -v $PWD/docker_run:/u01/oracle/properties \
      -e MANAGED_SERV_NAME=managed-server2 jeejeejango/weblogic-docker-sample:12213 startManagedServer.sh
```

For windows users, you will need to specify the full path to the docker_run directory.

### Checking the servers status
You can check if the Admin Server and Managed Servers have started using `docker ps`:
```
CONTAINER ID        IMAGE                        COMMAND                  CREATED             STATUS              PORTS                                        NAMES
db914bcc09ae        jeejeejango/wls-demo:12213   "startManagedServer.…"   3 seconds ago       Up 2 seconds        7001/tcp, 8453/tcp, 0.0.0.0:8002->8001/tcp   MS2
194c279a785f        jeejeejango/wls-demo:12213   "startManagedServer.…"   47 seconds ago      Up 46 seconds       7001/tcp, 8453/tcp, 0.0.0.0:8001->8001/tcp   MS1
8cf32769dca4        jeejeejango/wls-demo:12213   "startAdminServer.sh"    18 minutes ago      Up 18 minutes       8001/tcp, 0.0.0.0:7001->7001/tcp, 8453/tcp   wlsadmin

```

You can also login to weblogic console [http://localhost:7001/console](http://localhost:7001/console) using weblogic/welcome1.

## Sample Rest/UI applications
In this docker image, there are 2 sample applications deployed automatically with the admin server:
* demo-service
* demo-web

### Demo Service
The demo service provides a simple REST API [http://localhost:7001/demo-service/api/hello](http://localhost:7001/demo-service/api/hello) to return a json response:
```json
{"message":"hello X"}
```
You can also execute from either MS1 or MS2 using port 8001 or 8002 instead of 7001.

### Demo Web
The demo web provides a simple UI to display the response from the demo-service REST API using [http://localhost:7001/demo-web/hello](http://localhost:7001/demo-web/hello)

## Clean-up
To remove the sample containers, run:
```bash
docker rm wlsadmin MS1 MS2
```

# Copyright
This is a sample to demo the Oracle WebLogic in Docker. Please refer to Oracle for all rights for the Oracle WebLogic.