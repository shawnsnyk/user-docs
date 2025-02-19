# Set up Snyk Broker with Artifactory Repository

Follow the instructions on this page to set up Artifactory Repository with Snyk Broker. This integration is useful to ensure a secure connection with your on-premise Artifactory Repository  deployment.

{% hint style="info" %}
**Feature availability**\
Integration with Artifactory Repository is available with Enterprise plans. See [pricing plans](https://snyk.io/plans/) for more details.
{% endhint %}

{% hint style="info" %}
**Prerequisites**\
Ask your Snyk account team to provide you with a Broker token or generate it from the Snyk Web UI.

You need Docker or a way to run Docker Linux containers.

Some Docker deployments for Windows run only Windows containers. Ensure that your deployment is capable of running Linux containers.
{% endhint %}

{% hint style="info" %}
For information about non-brokered integration with Artifactory Repository see [Artifactory Repository setup](../../../integrations/private-registry-integrations/artifactory-repository-setup.md).
{% endhint %}

## Generate a Broker token from the Web UI

1. In the Artifactory integration settings, move the **Snyk Broker on/off** switch to **on** to display a form for generating a Broker token.
2. Select **Generate and Save.**
3. Copy the token that was generated to use when you set up the Broker Client.

## Configure Broker to be used for Artifactory Registry

To use the Broker client with an Artifactory Registry deployment, **run** `docker pull snyk/broker:artifactory`.

The following environment variables are needed to customize the Broker client:

`BROKER_TOKEN` - the Snyk Broker token, obtained from your Artifactory integration settings (**Integrations > Artifactory**).

`ARTIFACTORY_URL` - the URL of your Artifactory deployment, such as `<yourdomain>.artifactory.com/artifactory`.

The following fields are optional:

* _Port_: Omit if no port number is needed
* _Basic auth_: Omit if no basic auth required.\
  URL encode both username and password info to avoid errors that may prevent authentication.
* _Protocol_: Defaults to `https://`\
  This should only be specified when no certificate is present and `http://` is required instead for your instance

`ARTIFACTORY_URL` format with optional fields:\
`[http://][username:password@]hostname[:port]/artifactory`\
Example:\
`http://alice:mypassword@acme.com:8080/artifactory`

Optional. `RES_BODY_URL_SUB` - The URL of the Artifactory instance, including http:// and without basic auth credentials. **Required for npm/Yarn integrations only**.\
Example:\
`http://acme.com/artifactory`

## Docker run commands to set up a Broker Client for Artifactory Repository

**Copy the following command** to set up a fully configured Broker Client to use with Artifactory Registry. You can run the Docker container by providing the relevant configuration:

```console
docker run --restart=always \
           -p 8000:8000 \
           -e BROKER_TOKEN=secret-broker-token \
           -e ARTIFACTORY_URL=<yourdomain>.artifactory.com/artifactory \
       snyk/broker:artifactory
```

For an **npm or Yarn integration**, use the following **command**.

```
docker run --restart=always \
  -p 8000:8000 \
  -e BROKER_TOKEN=secret-broker-token \
  -e ARTIFACTORY_URL=acme.com/artifactory \
  -e RES_BODY_URL_SUB=http://acme.com/artifactory \ 
  snyk/broker:artifactory
```

As an **alternative to using the Docker run command**, you can use a derived Docker image to set up the Broker Client integration. See [Derived Docker images](derived-docker-images-for-broker-client-integrations-and-container-registry-agent.md) for the environment variables to override for the Artifactory integration.

## Start the Broker Client container and verify the connection with Artifactory Repository

Paste the Broker Client configuration to start the Broker Client container.

You can check the status of the connection by refreshing the Artifactory Integration Settings page. When the connection is set up correctly, there is no connection error.
