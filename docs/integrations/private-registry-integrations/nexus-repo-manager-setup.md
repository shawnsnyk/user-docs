# Nexus Repository Manager setup

{% hint style="info" %}
**Feature availability**\
This feature is available with Enterprise plans. See [pricing plans](https://snyk.io/plans/) for more details.
{% endhint %}

{% hint style="info" %}
**Supported Projects**\
The Nexus Repository Manager integration currently supports [Node.js](../../scan-application-code/snyk-open-source/snyk-open-source-supported-languages-and-package-managers/snyk-for-javascript/) (npm and Yarn) and [Maven](../../scan-application-code/snyk-open-source/snyk-open-source-supported-languages-and-package-managers/snyk-for-java-gradle-maven.md) projects. Gradle projects are not currently supported.
{% endhint %}

Connecting Nexus Repository Manager enables Snyk to resolve all direct and transitive dependencies of packages hosted on the Nexus registry and calculate a more complete, accurate dependency graph and related vulnerabilities.

You can configure these types of Nexus Repository Manager:

* Publicly accessible instances protected by basic authentication
* Instances on a private network accessed through Snyk Broker (with or without basic authentication).

{% hint style="info" %}
**Versions supported**

* Nexus Repository Manager version 3.x is fully supported.
* Nexus Repository Manager version 2.15+ is in Beta
{% endhint %}

These instructions apply to configuring publicly accessible instances. For instructions on configuring a brokered instance see [Set up Snyk Broker with Nexus Repository Manager](../../snyk-admin/snyk-broker/snyk-broker-set-up-examples/set-up-snyk-broker-with-nexus-repository-manager.md).

## Obtain Broker token for Nexus integration

1. Go to settings <img src="../../.gitbook/assets/cog_icon.png" alt="Settings icon" data-size="line"> > **Integrations > Package Repositories > Nexus**
2. Verify that you see the screen to configure Nexus.

<figure><img src="../../.gitbook/assets/Screenshot 2022-07-15 at 15.15.11.png" alt="Configure Nexus"><figcaption><p>Configure Nexus</p></figcaption></figure>

{% hint style="info" %}
If you do not see the **Snyk Broker** switch, you do not have the necessary permissions and can only add a publicly accessible instance.

Submit a request to [Snyk Support](https://support.snyk.io/hc/en-us/requests/new) if you want to add a private registry.
{% endhint %}

When you have permissions to add a private registry, continue with the instructions on [Set up Snyk Broker with Nexus Repository Manager](../../snyk-admin/snyk-broker/snyk-broker-set-up-examples/set-up-snyk-broker-with-nexus-repository-manager.md).

## Set up publicly accessible instances

On the page to configure Nexus, enter the information for the version you are using.

{% tabs %}
{% tab title="Nexus 3" %}
* Enter the URL of your Nexus instance; this **must** end with `/repository`
* Enter Username
* Enter Password
* Click **Save**
{% endtab %}

{% tab title="Nexus 2" %}
* Enter URL of your Nexus instance, this **must** end with `/nexus/content`
* Enter Username
* Enter Password
* Click **Save**
{% endtab %}
{% endtabs %}

## Nexus behind reverse proxy

If your Nexus server is running behind a reverse proxy, for example, Nginx, the URL might not end with the default `/repository` for Nexus 3 or `/nexus/content` for Nexus 2, depending on what routes have been configured in the reverse proxy. If this is the case, make sure to use the URL configured in the reverse proxy.

Example: for Nexus 3: if `http://nexus.company.io/repository` is mapped to `http://nexus.company.io/my-company/my-repository`, use `http://nexus.company.io/my-company/my-repository`.

Example: for Nexus 2: if `http://nexus.company.io/nexus/content` is mapped to `http://nexus.company.io/my-nexus-content`, use `http://nexus.company.io/my-nexus-content`.

{% hint style="success" %}
A green success message appears if Snyk can contact your repository.

If you see a yellow warning message, check your URL and credentials and try again.
{% endhint %}

## Nexus user permissions

The Nexus user needs the following privileges, either as part of Role or added individually:

{% tabs %}
{% tab title="Nexus 3" %}
* `nx-metrics-all` (for the [system status check endpoint](https://support.sonatype.com/hc/en-us/articles/226254487-System-Status-and-Metrics-REST-API))
* `nx-repository-view-[*-* | <ecosystem-repo-name>]-read`
* `nx-repository-view-[*-* | <ecosystem-repo-name>]-browse`
{% endtab %}

{% tab title="Nexus 2" %}
* `Status - Read`
* `All [<ecosystem>] Repositories - (read)`
* `[All Repositories | <repoName>] - (view)`
{% endtab %}
{% endtabs %}
