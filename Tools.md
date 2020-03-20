# Debuging Cheatsheet

## Tools

- [Kubebox](https://github.com/astefanutti/kubebox)
    Terminal Consone for Kubernetes, useful for quickly browsing logs.




## Azure Container Registry

Put the following in your `~/.bashrc`

```bash
# Get tags of a image inside ACR
alias acr-get="az acr repository show-tags --n mentorgg --repository"
# Get a list of images inside ACR
alias acr-list="az acr repository list --name mentorgg"
```

### Usage

```shell
$ acr-list
[
  "democentral",
  "demodownloader",
  "demofileworker",
  "faceitmatchgatherer",
  "manualdemodownloader",
  "matchretriever",
  "matchwriter",
  "mentorinterface",
  "sharingcodegatherer",
  "steamuseroperator"
]

$ acr-get demodownloader
[
  "0.1.0",
  "0.1.1",
  "0.2.1",
  "0.2.2",
  "0.2.3"
]

```

## Commands

## Run an interactive shell inside a Docker image to confirm runtime files are present/

```shell
$ docker run --rm -it --entrypoint=bash mentorgg.azurecr.io/demofileworker:0.3.4^
```

## Get the current deployments with image information including tags.

```
$ kubectl get deployments -o wide
```


