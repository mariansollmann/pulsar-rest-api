[![Build Status](https://travis-ci.org/cargomedia/pulsar-rest-api.png?branch=master)](https://travis-ci.org/cargomedia/pulsar-rest-api)

(unstable, currently in development)

pulsar-rest-api
===============

## About
HTTP REST API for executing [pulsar](https://github.com/nebulab/pulsar) tasks.

## Server

### Installation
Package is in nodejs and is available through npm registry:
```
npm install pulsar-rest-api [-g]
```

### Running
You can run pulsar-rest-api using default arguments or specify them on your own.

`--config-repo` Specify pulsar configuration repository.

`--config-branch` Specify branch for pulsar configuration repository (default set to `master`).

`--port` Specify port where server listen for requests (default set to `8081`).

`--log-dir` Directory where log is stored. Script will try to create directory if needed. Defaults to `null` which means it will output to stdout.

`--ssl-key` Specify ssl private key file. Combine with `ssl-cert` option.

`--ssl-cert` Specify ssl public certificate file. Combine with `ssl-key` option. Append CA-chain within this file.

`--ssl-pfx` Specify ssl pfx file (key + cert). Overrides `ssl-key` and `ssl-cert` options.

`--ssl-passphrase` Specify file containing the ssl passphrase.

`--github-oauth-id` Specify github oauth `id`.

`--github-oauth-secret` Specify github oauth `secret`.

`--mongo-host` Specify mongoDB hostname (default set to `localhost`).

`--mongo-port` Specify mongoDB port (default set to `27017`).

`--mongo-db` Specify mongoDB database name (default set to `pulsar`).

## Development

### Run

By default server listens on port `8001` in SSL mode with certificates for domain `*.pulsar.local`. SSL certs are stored in `bin/ssl/*`.

### Test

For testing please modify your `/etc/hosts` file by adding `127.0.0.1 api.pulsar.local`.

Run in console `curl -k https://api.pulsar.local:8001/application/environment/task`.


## API documentation

`:app` - application name (e.g. foobar)

`:env` - environment name (e.g. production)

`:action` - pulsar action/task

`:id` - task ID

### Get tasks list

#### Request:
`GET /tasks`

#### Response on success:
HTTP response code `200`
```json
{
  "url": "http://api.pulsar.local:8001/pulsar/index.html",
  "tasks": {
    "task-id-1" : "{Object}",
    "task-id-n" : "{Object}"
  }
}
```

#### Response on timeout:
No new task created before the timeout
HTTP response code `200`
```json
{
  "changed": false
}
```


### Create Task

#### Request:
```
POST /:app/:env?task=:task
```

Optionally use the blocking behaviour:
```
POST /:app/:env?action=:action&wait=true
```

#### Response on success:
HTTP response code `200`
```json
{
  "id": "123",
  "url": "https://api.pulsar.local:8001/web/task/532c3240f8214f0000177376"
}
```

In case of a blocking execution the task's data will be returned:
```json
{
  "id": 123,
  "url": "https://api.pulsar.local:8001/web/task/532c3240f8214f0000177376",
  "data": {
    "id": 123,
    "status": "failed",
    "app": "fuboo",
    "env": "production",
    "action": "shell",
    "exitCode": null,
    "output": "Here comes the output",
    "pid": 48691
  }
}
```

### Get task data

Immediately returns all task data including output to date.

#### Request:
`GET /task/:id`

#### Response on success:
HTTP response code `200`
```json
{
  "id": 123,
  "status": "failed",
  "app": "fuboo",
  "env": "production",
  "action": "shell",
  "exitCode": null,
  "output": "Here comes the output",
  "pid": 48691
}
```

### Kill task

#### Request
`GET /task/:id/kill`

#### Response
HTTP response code `200`
