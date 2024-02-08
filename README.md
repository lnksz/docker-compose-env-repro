# Reproducer for docker compose environment reusal issue.

From the docs:

https://docs.docker.com/compose/environment-variables/set-environment-variables/#additional-information

> If you define an environment variable in your .env file, you can reference it directly in your compose.yml with the environment attribute. For example, if your .env file contains the environment variable DEBUG=1 and your compose.yml file looks like this:
> 
>   services:
>     webapp:
>       image: my-webapp-image
>       environment:
>         - DEBUG=${DEBUG}

So let's reproduce this:

```bash
docker compose version
docker compose config
docker compose run repro
```

version output 
```
Docker Compose version v2.24.5
```

config output
```yaml
name: docker-compose-env-repro
services:
  repro:
    command:
      - env
    environment:
      MYPORT: "2"
      PORT: "1"
    image: busybox
    networks:
      default: null
networks:
  default:
    name: docker-compose-env-repro_default
```

run output
```
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
HOSTNAME=99b32ae95945
TERM=xterm
PORT=1
MYPORT=2
HOME=/root
```

## ???

So for a service, which defines a non-default env file via `env_file:`,
the `.env` default file is still read and used. And a variable defined in
the `envrionment:` attribute can be initialized from the `.env` file.
BUT not from the `env_file:` file.

Maybe this sentence from the docs is trying to say this?

https://docs.docker.com/compose/environment-variables/set-environment-variables/#additional-information-2

> Environment variables declared in the .env file cannot then be referenced again separately in the Compose file.