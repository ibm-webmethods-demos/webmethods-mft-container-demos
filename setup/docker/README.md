Docker Sample Deployments - webMethods Managed File Transfer
=================================================================================

Container-based deployment of webMethods Active Transfer solution, including:
 - Managed File Transfer Server (MFT Server)
 - Managed File Transfer Gateway (MFT Gateway)
 - Postgres DB

Requirements
------------------------------------------------

### Functional Docker environment

The server should have a functional Docker environment where you can run both "docker" and "docker compose" commands.

### Access/Login to webMethods Container Registries

Make sure you have access to the IBM webMEthods Container Registry portal at https://containers.webmethods.io and can access the2 "IBM webMethods Active Transfer Server" containers:
- ibmwebmethods.azurecr.io/webmethods-activetransfer:11.1
- ibmwebmethods.azurecr.io/webmethods-activetransfer-dcc:11.1

Login to the registry:

```bash
docker ibmwebmethods.azurecr.io
```

And pull the containers:

```bash
docker pull ibmwebmethods.azurecr.io/webmethods-activetransfer:11.1
docker pull ibmwebmethods.azurecr.io/webmethods-activetransfer-dcc:11.1
```

Start the stack - Steps by Steps (This is for first time only)
------------------------------------------------

### Database backend

NOTE: On first start, it's best to start the stack in controlled order so all the assets are created correctly
Since "docker compose" does not easily offer an easy way to start multiple components in a specific controlled order... let's perform several docker compose operations at first:

```
docker compose up -d postgres dbconfig
```

... wait for postgres healthy (docker ps | grep postgres) ...

NOTE 1: the container "dbconfig" is a job... it should have run, setup the tables needed in postgres, and terminated (it's expected)
If you want to check the status or logs for it, run:  
"docker ps -a | grep dbconfig", find the ID, and run "docker logs <dbconfig container id>"

NOTE 2: For postgres admin password, find it in the [.env](.env) file, in the variable POSTGRES_PASSWORD

### MFT Server

Then:
```
docker compose up -d mftserver
```

Start the stack Full (subsequent times)
------------------------------------------------

On subsequent starts though, and provided the data volumes were not cleared/deleted, it's no problem to start it all in short:

```bash
sh ./start-all.sh
```

Access the Admin MFT UI
------------------------------------------------

UIs:
- MFT Admin UI: http://localhost:9100/mft/ 
  - User = "Administrator"
  - Password = "SomeNewStrongPassword123!" (Note: find it or change it in the [.env](.env) file in the variable APPS_ADMIN_PASSWORD)
- MFT Web Upload (once configured below): http://localhost:5566 
  - User = "Administrator" (or others as configured)
  - Password = "SomeNewStrongPassword123!"

NOTE: replace localhost with the Server's IP if running this stack on a remote server...

Ports Exposed
------------------------------------------------


Configure the stack (with the MFT Gateway)
------------------------------------------------


Stop the stack
------------------------------------------------

### Destroy the stack + keep data

```bash
docker compose -f docker-compose.yml down
```

### Destroy the stack + destroy the data too

```bash
docker compose -f docker-compose.yml down -v
```