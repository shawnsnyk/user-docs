# Understanding Snyk Container CLI results

## **Vulnerability information**

When Snyk Container detects vulnerabilities they are presented in the output:

![Vulnerabilities detected with Snyk Container](../../.gitbook/assets/clivulnerabiilities.png)

Each vulnerability is shown with the following information:

| **Field**              | **Description**                                                                                                           |
| ---------------------- | ------------------------------------------------------------------------------------------------------------------------- |
| **Severity**           | The importance of the specific vulnerability. See Snyk Container severity for more information.                           |
| **A clear heading**    | Summary of the vulnerability and the affected package.                                                                    |
| **Description**        | A brief description of the type of issue or Common Vulnerabilities and Exposure (CVE) reference if a CVE exists.          |
| **Info**               | A link to more details about the vulnerability, including links to upstream sources and global vulnerabilities databases. |
| **Introduced through** | The top-level package names affected by the vulnerability.                                                                |
| **From**               | How the affected packages came to be in the image.                                                                        |
| **Introduced by**      | Whether the vulnerability is in the base image, or which line in the Dockerfile introduced the vulnerability.             |
| **Fixed in**           | If available, the version of the package which has a fix for the vulnerability.                                           |

Vulnerabilities appear in reverse severity order to limit scrolling to see the most important issues.

Snyk also reports the total dependencies tested for known vulnerabilities and the total number of vulnerabilities.

![Total dependencies tested and issues fount](../../.gitbook/assets/clisummary.png)

{% hint style="info" %}
Note\
Snyk groups the same vulnerability discovered in multiple different packages together. This helps you focus on the number of vulnerabilities, not just the instances.
{% endhint %}

## Base image recommendations

If Snyk determines the base image used, and the image uses an [Official Docker image](https://docs.docker.com/docker-hub/official\_images/), the output includes recommendations for upgrades to resolve some of the discovered vulnerabilities.

![Recommendations for base image upgrade](../../.gitbook/assets/clirecommendations.png)

This provides a level of situational awareness, showing the vulnerability counts in minor and major upgrades or in alternative base images which might have fewer vulnerabilities.
