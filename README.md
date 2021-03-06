#### Swarm Deploy Wrapper

This application makes managing docker configs/secrets more bearable.

The wrapper reads the configs and secrets section of the yaml file and creates environment variables based on the SHA256
of the referenced files (truncated to 16 characters). For example:

```yaml
configs:
  my_config:
    name: my_config.${MYFILE_XML}
    file: ./myfile.xml

secrets:
  my_secret:
    name: service.${DATA_CREDENTIALS_JSON}
    file: ./data.credentials.json
```

Will create two environment variables `MYFILE_XML` and `DATA_CREDENTIALS_JSON` with the truncated sha256sum of their
respective files and pass them to the `docker stack deploy` command.

The stack name can be ommited, in that case the current directory name will be used instead.

## Changes to codestation/docker-deploy

- Paths to files are resolved correctly even when docker-deploy is called from an other directory. E.g.: `docker-deploy -c /home/user/deployment/app/app.yml app`

- If there is a variable in the filename such as below, the resulting environment variable will be created based on the variable.

```yaml
secrets:
  secrets.env:
    name: ${APP_NAME}.env.${APP_NAME_ENV}
    file: ../secrets/${APP_NAME}.env
```

## Options

* `--compose-file, -c` Path to a Compose file, or "-" to read from stdin.
* `--with-registry-auth, -a` Send registry authentication details to Swarm agents.
* `--prune, -p` Prune services that are no longer referenced.
* `--host, -H` Daemon socket(s) to connect to.

## Installation

```sh
docker run --rm -v "$PWD":/go/bin golang:1.14 /bin/bash -c "go get github.com/matemoln/docker-deploy"
sudo mv docker-deploy /usr/local/bin
```
