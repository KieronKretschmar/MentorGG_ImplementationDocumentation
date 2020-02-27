# Docker Registry

Mentor's Docker registry is hosted with Azure at `mentorgg.azurecr.io`

## Requirements

- [Docker](https://www.docker.com/)
- [Azure CLI](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli)

## Registry Interaction

### Authenticating

To interact with the Docker registry you must be authentication, running the command below authenticates your Docker instance with the Azure Container Registry.

```shell
$ az acr login --name mentorgg
```

[Source](https://docs.microsoft.com/en-us/azure/container-registry/container-registry-authentication)

### Building and tagging an image
When building an image, tag it using the following structure: `mentorgg.azurecr.io/[REPO][:VERSION]`. 
Build and tag in one step using this syntax:
```
docker build . -t mentorgg.azurecr.io/[REPO][:VERSION]
```


Example:

```shell
$ docker images
REPOSITORY                             TAG                 IMAGE ID            CREATED             SIZE
mentorgg.azurecr.io/mentorinterface    0.1.1	           45e385ace141        2 minutes ago       217MB
```

### Pushing an Image
After you have built your image and given it the correct tag, run `docker push

```shell
$ docker push mentorgg.azure.io/mentorinterface:0.1.1
```


