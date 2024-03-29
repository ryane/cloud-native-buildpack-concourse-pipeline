# cloud-native-buildpack-concourse-pipeline

## Requirements

* [docker-compose](https://docs.docker.com/compose/)
* [fly-cli](https://github.com/concourse/concourse/releases/tag/v5.3.0)
* [jq](https://stedolan.github.io/jq/) (optional)

## Install the Fly CLI ###

### OSX

```shell
curl -SLO https://github.com/concourse/concourse/releases/download/v5.3.0/fly-5.3.0-darwin-amd64.tgz
tar xzvf fly-5.3.0-darwin-amd64.tgz
mv fly /usr/local/bin/fly
rm fly-5.3.0-darwin-amd64.tgz
```

## Update config.yaml

The `analyze` and `export` tasks must be configured with a Docker Hub repository that you have access to. In addition, the `export` task needs a Docker Hub token so that it can push an image. Both of these settings are configured in the `config.yaml` file.

The Docker Hub auth token is just a base64 encoding of the string "<docker_user_name>:<docker_password>". You can generate it by running:

```shell
echo -n "<docker_user_name>:<docker_password>" | base64
```

You can copy that output into `config.yaml`.

## Start Concourse

```shell
docker-compose up
```

## Authenticate to Concourse

```shell
fly --target bptest login --concourse-url http://127.0.0.1:8080 -u admin -p admin
```

"bptest" is an arbitrary name that identifies a particular Concourse target. You can use whatever name you want but the following examples all use "bptest".

## Access the Concourse UI

[http://127.0.0.1:8080/](http://127.0.0.1:8080/)

Login with "admin:admin". I intermittently get an error when logging in. I don't know why. It usually works on the next attempt so keep trying.

## Create (or Update) the Pipeline

```shell
fly -t bptest set-pipeline -p buildpack -c cloud-native-buildpack.yaml -l config.yaml
```

Confirm the change when prompted.

## Unpause the Pipeline

```shell
fly -t bptest unpause-pipeline -p buildpack
```

## Trigger the Job

```shell
fly -t bptest trigger-job --job buildpack/buildpack
```
