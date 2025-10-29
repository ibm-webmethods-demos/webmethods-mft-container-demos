Docker Sample Deployments - webMethods Managed File Transfer
=================================================================================

REF Documentation for Docker deployment is at: https://www.ibm.com/docs/en/webmethods-activetransfer/11.1.0?topic=server-using-activetransfer-docker

Container-based deployment of webMethods Active Transfer solution, including:
 - Managed File Transfer Server (MFT Server)
 - Postgres DB (NFT core)
 - Postgres DB (MFT archive)

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

Start the stack in order to setup the DB (first time only)
------------------------------------------------------------------------------------------------

### Database backend

NOTE: On first start, it's best to start the stack in controlled order so all the assets are created correctly
Since "docker compose" does not easily offer an easy way to start multiple components in a specific controlled order... let's perform several docker compose operations at first:

```bash
docker compose up -d database database-archive
```

### Database Configuration

```bash
docker compose -f docker-compose-dbconfig.yml up dbconfig
```

### MFT Server

Then:

```bash
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
- MFT Admin UI: http://localhost:5555/WmMFT/
  - User = "Administrator"
  - Password = "SomeNewStrongPassword123!" (Note: find it or change it in the [.env](.env) file in the variable APPS_ADMIN_PASSWORD)

NOTE: replace localhost with the Server's IP if running this stack on a remote server...

Ports Exposed
------------------------------------------------

- 5555
- 5543

Configure the stack (with the MFT Gateway)
------------------------------------------------

TBD

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
