# Advanced configuration for Snyk Broker Docker installation

## **HTTPS for Broker Client**

The Broker Client runs an HTTP server by default. It can be configured to run an HTTPS server for local connections. This requires an SSL certificate and a private key to be provided to the Docker container at runtime.

For example, if your certificate files are found locally at `./private/broker.crt` and `./private/broker.key`, provide these files to the Docker container by mounting the folder and using the `HTTPS_CERT` and `HTTPS_KEY` environment variables:

```
docker run --restart=always \
           -p 8000:8000 \
           -e BROKER_TOKEN=secret-broker-token \
           -e GITHUB_TOKEN=secret-github-token \
           -e PORT=8000 \
           -e HTTPS_CERT=/private/broker.crt \
           -e HTTPS_KEY=/private/broker.key \
           -e BROKER_CLIENT_URL=https://my.broker.client:8000 \
           -v /local/path/to/private:/private \
       snyk/broker:github-com
```

Note that `BROKER_CLIENT_URL` now has the HTTPS scheme.

## **Backend requests with an internal certificate**

By default, the Broker Client establishes HTTPS connections to the backend system, GitHub, BitBucket, Jira, or other. If your backend system is serving an internal certificate (signed by your own certificate authority (CA)), you can provide the CA certificate to the Broker Client.

For example, if your CA certificate is at `./private/ca.cert.pem`, provide it to the Docker container by mounting the folder and using the `CA_CERT` environment variable:

```
docker run --restart=always \
           -p 8000:8000 \
           -e BROKER_TOKEN=secret-broker-token \
           -e BITBUCKET_USERNAME=username \
           -e BITBUCKET_PASSWORD=password \
           -e BITBUCKET=your.bitbucket-server.domain.com \
           -e BITBUCKET_API=your.bitbucket-server.domain.com/rest/api/1.0 \
           -e PORT=8000 \
           -e CA_CERT=/private/ca.cert.pem \
           -v /local/path/to/private:/private \
       snyk/broker:bitbucket-server
```

Note that this completely replaces the default CA Certificate List for any requests made to your backend system, so this must be the complete chain required by the certificate used by the backend system.

It must be `PEM`-formatted; `DER` is not supported. Supported certificate types are:

* `TRUSTED CERTIFICATE`
* `X509 CERTIFICATE`
* `CERTIFICATE`

An example follows.

```
-----BEGIN CERTIFICATE-----
<base64-encoded certificate>
-----END CERTIFICATE----
-----BEGIN CERTIFICATE-----
<base64-encoded certificate>
-----END CERTIFICATE-----
-----BEGIN CERTIFICATE-----
<base64-encoded certificate>
-----END CERTIFICATE-----
```

## Proxy support

For proxy configuration see [Configure Docker to use a proxy server](https://docs.docker.com/network/proxy/).

```
 -e HTTP_PROXY=http://my.proxy.address:8080
 -e HTTPS_PROXY=http://my.proxy.address:8080
 -e NO_PROXY=*.test.example.com,.example2.com,127.0.0.0/8
```

If your proxy requires username and password authentication, provide the following additional environment variable:

```
-e PROXY_AUTH=userID:userPass
```

## Disable certificate verification

To disable certificate verification, for example, in the case of self-signed certificates, add the following to the **docker run** command:

```
-e NODE_TLS_REJECT_UNAUTHORIZED=0
```

## Custom approved-listing filter

The default approved-listing filter supports the bare minimum to operate on all repositories supported by Snyk. In order to customize the approved-listing filter, create the default filter locally by installing `snyk-broker` and running `broker init [Git type]`. The created `accept.json` is the default filter for the chosen Git type. Place the file in a separate folder such as `./private/accept.json`, and provide it to the Docker container by mounting the folder and using the `ACCEPT` environment variable:

```
docker run --restart=always \
           -p 8000:8000 \
           -e BROKER_TOKEN=secret-broker-token \
           -e GITHUB_TOKEN=secret-github-token \
           -e PORT=8000 \
           -e BROKER_CLIENT_URL=https://my.broker.client:8000 \
           -e ACCEPT=/private/accept.json
           -v /local/path/to/private:/private \
       snyk/broker:github-com
```

**To filter by header**, add a validation block with the following key and values:

| Key    | Value                                                                                                                                                     | Value Type     | Example                             |
| ------ | --------------------------------------------------------------------------------------------------------------------------------------------------------- | -------------- | ----------------------------------- |
| header | The name of the header you wish to filter on. If this is defined then the named header must explicitly exist on the request; otherwise it will be blocked | String         | `accept`                            |
| values | The header value must match one of the defined strings                                                                                                    | Array\<String> | `["application/vnd.github.v4.sha"]` |

Example:\
To only allow the SHA Media Type accept header for requests to the GitHub Commits API, you would add the following:

```
{
    "method": "GET",
    "path": "/repos/:name/:repo/commits/:ref",
    "origin": "https://${GITHUB_TOKEN}@${GITHUB_API}",
    "valid": [
        {
            "header": "accept",
            "values": ["application/vnd.github.v4.sha"]
        }
    ]
}
```

For examples of custom approved-listing filters for each SCM see [Snyk Broker integration setups](../snyk-broker-set-up-examples/) and [Adding custom accept.json for Docker installation](adding-custom-accept.json-for-docker-installation.md).

## Mounting Secrets

Sometimes it is required to load sensitive configurations, the GitHub or Snyk token, from a file instead of from environment variables. Broker is using [dotenv](https://www.npmjs.com/package/dotenv) to load the config, so the process is relatively simple:

* Create a file named `.env` and put your sensitive configuration there.
* Mount this file, for example, using a [Kubernetes secret](https://kubernetes.io/docs/tasks/inject-data-application/distribute-credentials-secure/#create-a-pod-that-has-access-to-the-secret-data-through-a-volume)). Mount the file to be somewhere like `/broker`.
* Change the workdir of the docker image to be `/broker/`. An example of such file is located in your broker container at $HOME/.env.
