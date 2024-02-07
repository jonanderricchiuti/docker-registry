# Private docker registry with Basic Auth

Configuration here is for a private docker registry behind a reverse proxy.

## Setting up

Create a username and password.

```
docker run --entrypoint htpasswd httpd:2 -Bbn username password >> auth/registry.password
```

## Running the registry

```
docker compose up -d
```

## Managing commands

List all repositories

```
curl -u username:password https://dregistry.dojob.dev/v2/_catalog
```

List tags in repository

```
curl -u username:password https://dregistry.dojob.dev/v2/your_repository/tags/list
```

### Deleting images

Get the sha256 digest

```
curl -u username:password -v --silent -H "Accept: application/vnd.docker.distribution.manifest.v2+json" 2>&1 -X GET https://dregistry.dojob.dev/v2/your_repository/manifests/your_tag | grep Docker-Content-Digest | awk '{print ($3)}'
```

Remove the manifest with the output of the previous command

```
curl -u username:password -v -X DELETE https://dregistry.dojob.dev/v2/your_repository/manifests/<sha256.value_from_previous_command>
```

Run the garbage collector

```
docker exec docker-registry registry garbage-collect  /etc/docker/registry/config.yml --delete-untagged
```
