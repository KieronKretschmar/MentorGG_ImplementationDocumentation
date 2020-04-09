# Debugging Cheatsheet / Tools

## Tools

- [Kubebox](https://github.com/astefanutti/kubebox)
    Terminal Consone for Kubernetes, useful for quickly browsing logs.


## RabbitMq Admin CLI

- [Management-CLI](https://www.rabbitmq.com/management-cli.html)

### Install

```shell
# Firstly, portforward the rabbitmq instance to your localmachine
# This connection must remain open.
$ kubectl port-forward rabbitmq-0 15672:15672

# Download the executable
$ wget http://localhost:15672/cli/rabbitmqadmin

# Allow the file to be executable
$ chmod +x ./rabbitmqadmin

# Optionally, move the executable to a BIN folder for easier execution.
$ mv ./rabbitmqadmin ~/.local/bin/.

```

Optionally, add this line to your `~/.bash_profile` for bash completion
```bash
. <(rabbitmqadmin --bash-completion)
```

## Azure Container Registry

Put the following in your `~/.bash_profile`

```bash
# Get tags of a image inside ACR
alias acr-get="az acr repository show-tags --n mentorgg --repository"
# Get a list of images inside ACR
alias acr-list="az acr repository list --name mentorgg"

# docker aliases / functions
function dbuild(){ docker build . -t mentorgg.azurecr.io/"$@";}
function dpush(){ docker push mentorgg.azurecr.io/"$@";}
function dbpush(){
    dbuild "$@" && \
    dpush "$@" && \
    echo Pushed image to mentorgg.azurecr.io/"$@";
}
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

$ dbpush mentorinterface:1.1.1-pre
$ dbuild democentral:1.2.3-pre
$ dpush sharingcodegatherer:2.3.4-pre

```

### Notes

Please end all manually pushed images with `-pre` to signal the image is a pre-release.


## Commands

## Run an interactive shell inside a Docker image to confirm runtime files are present/

```shell
$ docker run --rm -it --entrypoint=bash mentorgg.azurecr.io/demofileworker:0.3.4^
```

## Get the current deployments with image information including tags.

```
$ kubectl get deployments -o wide
```


