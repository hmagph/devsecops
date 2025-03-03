---

copyright:
  years: 2021, 2022
lastupdated: "2022-08-05"

keywords: DevSecOps, IBM Cloud, maximum retry time, scans

subcollection: devsecops

---

{{site.data.keyword.attribute-definition-list}}

# Pipeline parameters
{: #cd-devsecops-pipeline-parm}

Parameters are provided out of the box for the pull request, continuous integration, and continuous deployment pipelines.
{: shortdesc}

You can add parameters to the pipelines on the pipeline UI and access them from the [custom scripts](/docs/devsecops?topic=devsecops-custom-scripts).
{: tip}

## Pull request parameters
{: #cd-pr-parameters}

The following table lists and describes the pull request parameters for pipelines:

|Name |Type	|Description |Required or Optional |
|:----------|:------------------------------|:------------------|:----------|
|artifactory-dockerconfigjson 		|SECRET 		|The base64-encoded Docker `config.json` file that stores credential information for artifactory.	 			|Optional			|
|baseimage-auth-user		|text		|The user credential for the base image of the application Dockerfile, required by the Code Risk Analyzer scan.			|Optional			|
|baseimage-auth-email		|text 		|The e-mail credential for the base image of the application Dockerfile, required by the Code Risk Analyzer scan.		|Optional			|
|baseimage-auth-host		|text		|The host credential for the base image of the application Dockerfile, required by the Code Risk Analyzer scan.	|Optional			|
|baseimage-auth-password		|SECRET		|The password credential for the base image of the application Dockerfile, required by the Code Risk Analyzer scan. |Optional			|
| cra-custom-script-path  | text   | Path to a custom script to be run before CRA scanning. This script is sourced to provide the option to set ENV variables in the context of the CRA BOM tool. | Optional |
|cra-cveignore-path             |text   |File path to the cveignore, relative to the application repository root. Default file path is `.cra/.cveignore`, if value is not provided.   |Optional    |
|cra-docker-build-context     |text   |If this flag is specified, Code Risk Analyzer uses the directory in the path parameter as the Docker build context. The default value is `false`. |Optional |
|cra-docker-buildflags          |text   |Customize docker build command for build stage scanning. The parameter is empty by default.    |Optional    |
|cra-dockerfile-pattern             | text   | Ability to add Dockerfiles with a different naming convention, such as `enterprise-linux.Dockerfile` | optional |
|cra-exclude-devdependencies		|text		|Specifies whether to exclude dev dependencies from scanning (`true` or `false`). The default value is `false`.	|Optional			|
|cra-gradle-exclude-configs     |text   |Specifies which Gradle configurations to exclude dependencies in scanning. Example: `runtimeClasspath,testCompileClasspath`. The parameter is empty by default.   |Optional   |
|cra-maven-exclude-scopes       |text   |Specifies which maven scopes to exclude dependencies in scanning. Example: `test,compile`. The parameter is empty by default.  |Optional   |
|cra-nodejs-create-package-lock		|text		|Enable Code Risk Analyzer discovery to build the `package-lock.json` file for node.js repos. This parameter is set to false by default.	|Optional			|
|cra-python-create-requirements-txt		|text		|Deprecated. The new CRA tools don't use this parameter anymore. Enable Code Risk Analyzer discovery to build the `requirements.txt` file for Python repos. This parameter is set to false by default.	|Optional			|
|git-token		|SECRET		|The Git repo access token.	|Optional			|
|github-token		|SECRET		|The GitHub repo access token.	|Optional			|
|grit-token		|SECRET		|The {{site.data.keyword.gitrepos}} access token.	|Optional			|
|iam_retry_count		|text		|The number of retries to wait for fetching the IAM token.	|Optional			|
|iam_retry_sleep		|text		|The amount of wait time for fetching the IAM token.	|Optional			|
|ibmcloud-api-key		|SECRET		|The {{site.data.keyword.cloud}} API key that interacts with the `ibmcloud` CLI tool.	|Required			|
|pipeline-config		|text		|The configuration file that customizes pipeline behavior.	|Optional			|
|pipeline-config-branch		|text		|The branch of the DevSecOps pipeline configuration.	|Optional			|
|pipeline-config-repo		|text		|The repo URL of the DevSecOps pipeline configuration location.	|Optional			|
|pipeline-dockerconfigjson		|SECRET		|The base64-encoded Docker `config.json` file that pulls images from a private registry.	|Optional			|
|onepipeline-dockerconfigjson		|SECRET		|Deprecated. The base64-encoded Docker `config.json` file that pulls images from a private registry.	|Optional			|
|[pipeline-debug](#pipeline-parm-pipeline-debug)		|select		|The pipeline debug mode switch.  |Optional			|
|slack-notifications		|text		|The switch that turns the Slack integration on or off.	|Optional			|	|[slack-notifications](#pipeline-parm-slack-notifications)		|text		|The switch that turns the Slack integration on or off |Optional		|
{: caption="Table 1. Pull request parameters" caption-side="top"}

## Continuous integration parameters
{: #cd-ci-parameters}

The following table lists and describes the continuous integration parameters for pipelines:

|Name |Type	|Description |Required or Optional |
|:----------|:------------------------------|:------------------|:----------|
|app-name 		|text 		|The name of your application that is specified in the toolchain settings.			|Required			|
|artifactory-dockerconfigjson		|SECRET		|The base64-encoded Docker `config.json` file that stores credential information for artifactory.			|Optional			|
|baseimage-auth-user		|text		|The user credential for the base image of the application Dockerfile, required by the Code Risk Analyzer scan.			|Optional			|
|baseimage-auth-email		|text		|The credentials for the application Dockerfile base image that is required by the Code Risk Analyzer scan.	|Optional			|	|baseimage-auth-email		|text 		|The e-mail credential for the base image of the application Dockerfile, required by the Code Risk Analyzer scan.		|Optional			|
|baseimage-auth-host		|text		|The credentials for the application Dockerfile base image that is required by the Code Risk Analyzer scan. |Optional			|	|baseimage-auth-host		|text		|The host credential for the base image of the application Dockerfile, required by the Code Risk Analyzer scan.	|Optional			|
|baseimage-auth-password		|SECRET		|The credentials for the application Dockerfile base image that is required by the Code Risk Analyzer scan.	|Optional			|	|baseimage-auth-password		|SECRET		|The password credential for the base image of the application Dockerfile, required by the Code Risk Analyzer scan. |Optional			|
|[branch](#pipeline-parm-branch)		|text		|The Git repo branch.	|Optional			|
|[cluster-name](#pipeline-parm-cluster-name)		|text		|The name of the Docker build cluster.	|Required			|
|[compliance-baseimage](#pipeline-parm-compliance-baseimage)	|text		|The baseimage for running the built-in pipeline code.	|Optional	
|cos-api-key		|text		|The Cloud Object Storage API key.	|Optional			|
|cos-bucket-name		|text		|The name of the bucket in your Cloud Object Storage instance that is used as an evidence locker.	|Optional			|
|cos-endpoint		|text		|The endpoint of your Cloud Object Storage instance that is used as an evidence locker.   |Optional			|
|custom-image-tag		|text		|The custom tag for the image in a comma-separated list.	|Optional			|
|[custom-exempt-label](#pipeline-parm-custom-exempt-label)  |text   |Defines the custom label with which the incident issue has been marked as exempted.	|Optional			|
| cra-custom-script-path  | text   | Path to a custom script to be run before CRA scanning. This script is sourced to provide the option to set ENV variables in the context of the CRA BOM tool. | Optional |
|cra-cveignore-path     |text   |File path to the cveignore, relative to the application repository root. Default file path is `.cra/.cveignore`, if value is not provided.   |Optional    |
|cra-docker-build-context     |text   |If this flag is specified, Code Risk Analyzer uses the directory in the path parameter as the Docker build context. The default value is `false`. |Optional |
|cra-docker-buildflags   |text   |Customize docker build command for build stage scanning. The parameter is empty by default.    |Optional    |
|cra-dockerfile-pattern             | text   | Ability to add Dockerfiles with a different naming convention, such as `enterprise-linux.Dockerfile` | optional |
|cra-exclude-devdependencies		|text		|Specifies whether to exclude dev dependencies from scanning (`true` or `false`). The default value is `false`.	|Optional			|
|cra-gradle-exclude-configs     |text   |Specifies which Gradle configurations to exclude dependencies in scanning. Example: `runtimeClasspath,testCompileClasspath`. The parameter is empty by default.   |Optional   |
| cra-generate-cyclonedx-format      | text   | If set to `1`, CRA also generates the BOM in `cyclonedx` format (defaults to `0`). | Optional |
|cra-maven-exclude-scopes       |text   |Specifies which maven scopes to exclude dependencies in scanning. Example: `test,compile`. The parameter is empty by default.  |Optional   |
|cra-nodejs-create-package-lock		|text		|Enable Code Risk Analyzer discovery to build the `package-lock.json` file for node.js repos. This parameter is set to false by default.	|Optional			|
|cra-python-create-requirements-txt		|text		|Deprecated. The new CRA tools don't use this parameter anymore. Enable Code Risk Analyzer discovery to build the `requirements.txt` file for Python repos. This parameter is set to false by default.	|Optional			|
|dev-cluster-namespace		|text		|The Kubernetes cluster namespace where the Docker engine is hosted and deployed.	|Required			|
|dev-region		|text		|The {{site.data.keyword.cloud_notm}} region that hosts the cluster.	|Required			|
|dev-resource-group		|text		|The cluster resource group.	|Required			|
|doi-buildnumber		|text		|The build number value to use as the `--buildnumber` flag in `ibmcloud doi` commands.	|Optional			|
|doi-tags		|text		|Comma-separated custom tags.	|Optional			|
|doi-environment		|text		|The {{site.data.keyword.DRA_short}} target environment.	|Optional			|
|doi-logicalappname		|text		|The app name to use as the `--logicalappname` flag in `ibmcloud doi` commands	|Optional			|
|doi-toolchain-id		|text		|The {{site.data.keyword.DRA_short}} instance toolchain ID.	|Optional			|
|git-token		|SECRET		|The Git repo access token.	|Optional			|
|github-token		|SECRET		|The GitHub repo access token.	|Optional			|
|grit-token		|SECRET		|The {{site.data.keyword.gitrepos}} access token.	|Optional			|
|iam_retry_count		|text		|The number of retries to wait for fetching the IAM token.	|Optional			|
|iam_retry_sleep		|text		|The amount of wait time for fetching the IAM token.	|Optional			|
|ibmcloud-api-key		|SECRET		|The {{site.data.keyword.cloud_notm}} API key that interacts with the `ibmcloud` CLI tool.	|Required			|
|incident-assignee		|text		|The assignee for the incident issues (GitHub or GitLab username). |Optional			|
|incident-assignees		|text		|The one or more assignees for the incident issues (one or more usernames that are separated by a comma). Note that this parameter can be used only with GitHub and GitLab Premium accounts. |Optional			|
|incident-label		|text		|The label for new incident issues.	|Optional			|
|opt-in-pr-collection		|text		|Add any value to enable PR collection.	|Optional			|
|opt-in-sonar		|tool integration		|The Sonarqube scan integration.	|Optional			|
|opt-in-dynamic-scan		|tool integration		|To enable the owasp zap scan.	 |Optional			|
|opt-in-dynamic-ui-scan		|tool integration		|To enable the owasp zap UI scan.	 |Optional			|
|opt-in-dynamic-api-scan		|tool integration		|To enable the owasp zap API scan. 	|Optional			|
|pipeline-config		|text		|The configuration file that customizes pipeline behavior.	|Required			|
|pipeline-config-branch		|text		|The branch of the DevSecOps pipeline configuration.	|Optional			|
|pipeline-config-repo		|text		|The repo URL of the DevSecOps pipeline configuration location.	|Optional			|
|onepipeline-dockerconfigjson		|SECRET		|Deprecated. The base64-encoded Docker `config.json` file that pulls images from a private registry.	|Optional			|
|registry-namespace		|text		|The container registry namespace for the image.	|Required			|
|registry-region		|text		|The {{site.data.keyword.cloud_notm}} region for the image registry.	|Required			|
|repository-integration		|text		|The integration name for the repo.	|Optional			|
|[signing-key](#pipeline-parm-signing-key)		|SECRET		|The base64-encoded private key for signing images by using `.pfx` file data.	|Required			|
|[pipeline-debug](#pipeline-parm-pipeline-debug)		|select		|The pipeline debug mode switch.  |Optional			|
|[slack-notifications](#pipeline-parm-slack-notifications)		|text		|The switch that turns the Slack integration on or off |Optional		|
|[sonarqube](#pipeline-parm-sonarqube)		|tool integration		|The Sonarqube tool integration.	|Optional			|
|[sonarqube-namespace](#pipeline-parm-sonarqube-namespace) 	|text		|The configured Kubernetes namespace to use with the SonarQube instance that is created by the pipeline.	|Optional			|
|[static-scan-retry-count](#pipeline-parm-static-scan-retry-count)		|text		|The number of retries to wait for the Sonarqube instance to be established.	|Optional			|
|[static-scan-retry-sleep](#pipeline-parm-static-scan-retry-sleep)		|text		|The amount of wait time per retry iteration.	|Optional			|
|subpipeline-webhook-token | SECRET| The webhook secret of the `Subpipeline Webhook Trigger` for [triggering async pipelines](/docs/devsecops?topic=devsecops-devsecops-async-sub-pipelines). See also [Updating the async stage webhooks](/docs/devsecops?topic=devsecops-update-async-webhook). |Optional |
|va-scan-retry-count		|text		|The number of retries to wait for the vulnerability report scan.	|Required			|
|va-scan-retry-sleep	|text		|The number of wait times per retry iteration.	|Optional			|
|version		|text		|The version of the app to deploy.	|Required			|
|inventory-repo		|tool integration		|The inventory repo URL.	|Optional			|
|evidence-repo		|tool integration		|The evidence repo URL.	|Optional			|
|incident-repo  	|tool integration		|The incident issues repo URL.	|Optional			|
{: caption="Table 2. Continuous integration parameters" caption-side="top"}

## Continuous deployment parameters
{: #cd-parameters}

The following table lists and describes the continuous deployment parameters for pipelines:

|Name |Type	|Description |Required or Optional |
|:----------|:------------------------------|:------------------|:----------|
|artifactory-dockerconfigjson		|SECRET		|The base64-encoded Docker `config.json` file that stores credential information for artifactory.			|Optional			|
|[assignee](#pipeline-parm-assignee)		|text		|The assignee of the change request.			|Optional			|
|[backout-plan](#pipeline-parm-backout-plan)	|text		|Plan of how the change will be rolled back in case of a failure.			|Optional			|
|[change-request-id](#pipeline-parm-change-request-id)    |text   |The ID of an open change request. If this parameter is set to `notAvailable` by default, a change request is automatically created by the continuous deployment pipeline. |Optional			|
|[change-management-duration](#pipeline-parm-change-management-duration)    | text   | The planned end time of the change. | Optional |
| change-management-repo        | text   | The URL of the change management repository.  | Optional |
|[cluster](#pipeline-parm-cluster)	|text 		|The name of the Docker build cluster.		|Required			|
|[cluster-region](#pipeline-parm-cluster-region)		|text		|The {{site.data.keyword.cloud_notm}} region that hosts the cluster.	|Required			|
|[compliance-baseimage](#pipeline-parm-compliance-baseimage)	|text		|The baseimage for running the built-in pipeline code. |Optional			|
|cos-api-key		|text		|The Cloud Object Storage API key.	|Optional			|
|cos-bucket-name		|text		|The name of the bucket in your Cloud Object Storage instance that is used as an evidence locker.	|Optional			|
|cos-endpoint		|text		|The endpoint of your Cloud Object Storage instance that is used as an evidence locker.   |Optional			|
|[description](#pipeline-parm-description)		|text		|The description of the change that will be appended to the Change Request Description.	|Optional			|
|doi-buildnumber		|text		|The build number value to use as the `--buildnumber` flag in `ibmcloud doi` commands.	|Optional			|
|doi-tags		|text		|Comma-separated custom tags.	|Optional			|
|doi-environment		|text		|The {{site.data.keyword.DRA_short}} target environment.	|Optional			|
|doi-logicalappname		|text		|The app name to use as the `--logicalappname` flag in `ibmcloud doi` commands	|Optional			|
|doi-toolchain-id		|text		|The {{site.data.keyword.DRA_short}} instance toolchain ID.	|Optional			|
|emergency-label		|text		|Identifies the pull request as an emergency.	|Optional			|
|force-redeploy    |text   |Forces the deployment or redeployment of the app even if the last deployment does not contain a delta in the inventory. Set this parameter to `true` to force a deployment of the app as if it is the first deployment on the specified target environment. By default, this parameter is set to `false`. |Optional |
|git-token		|SECRET		|The Git repo access token.	|Optional			|
|github-token		|SECRET		|The GitHub repo access token.	|Optional			|
|grit-token		|SECRET		|The {{site.data.keyword.gitrepos}} access token.	|Optional			|
|artifact-token		|SECRET		|The token where artifacts are stored	|Required if artifact repo is in different source provider.			|
|iam_retry_count		|text		|The number of retries to wait for fetching the IAM token.	|Optional			|
|iam_retry_sleep		|text		|The amount of wait time for fetching the IAM token.	|Optional			|
|ibmcloud-api-key		|SECRET		|The {{site.data.keyword.cloud_notm}} API key that interacts with the `ibmcloud` CLI tool.	|Required			|
|opt-in-pr-collection		|text		|Add any value to enable PR collection.	|Optional			|
|[impact](#pipeline-parm-impact)		|text		|Additional notes on what this change implementation impacts.	|Optional			|
|pipeline-config		|text		|The configuration file that customizes pipeline behavior.	|Required			|
|pipeline-config-branch		|text		|The branch of the DevSecOps pipeline configuration.	|Optional			|
|pipeline-config-repo		|text		|The repo URL of the DevSecOps pipeline configuration location.	|Optional			|
|[pipeline-debug](#pipeline-parm-pipeline-debug)		|select		|The pipeline debug mode switch.	|Optional			|
|pipeline-dockerconfigjson		|SECRET		|The base64-encoded Docker `config.json` file that pulls images from a private registry.	|Optional	  	|
|[priority](#pipeline-parm-priority)		|text		|The priority of the change request.	|Optional			|
|[purpose](#pipeline-parm-purpose)		|text		|The reason why the change is needed.	|Optional			|
|region		|text		|The target region where the app is deployed.	|Optional			|
|[slack-notifications](#pipeline-parm-slack-notifications)	|text		|The switch that turns the Slack integration on or off.  |Optional			|
|[source-environment](#pipeline-parm-source-environment)	|text		|The source environment that the app is promoted from.	|Required			|
|[summary-retry-attempts](#pipeline-parm-summary-retry-attempts)		|text		|Set the maximum number of retries for the evidence summary upload. The default is `5`.  |Optional			|
|[summary-max-retry-time](#pipeline-parm-summary-max-retry-time)		|text		|Set the maximum backoff time for the evidence summary upload. The actual backoff time is generated exponentially with each retry until it reaches the maximum backoff time set with this parameter. The default is `32`.  |Optional			|
|[target-environment](#pipeline-parm-target-environment)		|text		|The target environment that the app is deployed to.	|Required			|
|version		|text		|The version of the app to deploy.	|Required	  	|
|inventory-repo		|tool integration		|The inventory repo URL.	|Optional			|
|evidence-repo		|tool integration		|The evidence repo URL.	|Optional			|
|incident-repo  	|tool integration		|The incident issues repo URL.	|Optional			|
{: caption="Table 3. continuous deployment parameters" caption-side="top"}

## Continuous compliance parameters
{: #pipelines-cc-parameters}

The following table lists and describes the continuous compliance parameters for pipelines:

|Name |Type	|Description |Required or Optional |
|:----------|:------------------------------|:------------------|:----------|
|app-url 		|text 		|The URL of your application that is deployed on the target. Recommended to use staging application url.			|Required			|
|repo-url 		|text 		|The URL of your application repository.			|Required, if same inventory is used to store multiple application artifacts.			|
|artifactory-dockerconfigjson		|SECRET		|The base64-encoded Docker `config.json` file that stores credential information for artifactory.			|Optional			|
|branch		|text		|The Git repo branch.	|Optional			|
|baseimage-auth-user		|text		|The user credential for the base image of the application Dockerfile, required by the Code Risk Analyzer scan.			|Optional			|
|baseimage-auth-email		|text		|The credentials for the application Dockerfile base image that is required by the Code Risk Analyzer scan.	|Optional			|	|baseimage-auth-email		|text 		|The e-mail credential for the base image of the application Dockerfile, required by the Code Risk Analyzer scan.		|Optional			|
|baseimage-auth-host		|text		|The credentials for the application Dockerfile base image that is required by the Code Risk Analyzer scan. |Optional			|	|baseimage-auth-host		|text		|The host credential for the base image of the application Dockerfile, required by the Code Risk Analyzer scan.	|Optional			|
|baseimage-auth-password		|SECRET		|The credentials for the application Dockerfile base image that is required by the Code Risk Analyzer scan.	|Optional			|	|baseimage-auth-password		|SECRET		|The password credential for the base image of the application Dockerfile, required by the Code Risk Analyzer scan. |Optional			|
|[compliance-baseimage](#pipeline-parm-compliance-baseimage)		|text		|The baseimage for running the built-in pipeline code. |Optional			|
|cos-api-key		|text		|The Cloud Object Storage API key.	|Optional			|
|cos-bucket-name		|text		|The name of the bucket in your Cloud Object Storage instance that is used as an evidence locker.	|Optional			|
|cos-endpoint		|text		|The endpoint of your Cloud Object Storage instance that is used as an evidence locker.   |Optional			|
|cra-custom-script-path  | text   | Path to a custom script to be run before CRA scanning. This script is sourced to provide the option to set ENV variables in the context of the CRA BOM tool. | Optional |
|cra-docker-buildflags   |text   |Customize docker build command for build stage scanning. The parameter is empty by default.    |Optional    |
|[custom-exempt-label](#pipeline-parm-custom-exempt-label)  |text   |Defines the custom label with which the incident issue has been marked as exempted.    |Optional    |
|dev-region		|text		|The {{site.data.keyword.cloud_notm}} region that hosts the cluster. Required for default static and dynamic scans. |Required			|
|dev-resource-group		|text		|The cluster resource group.	|Required			|
|dind-image		|text		|Base image to run sidecars	|Optional			|
|doi-environment		|text		|The {{site.data.keyword.DRA_short}} target environment.	|Optional			|
|doi-tags		|text		|Comma-separated custom tags.	|Optional			|
|doi-toolchain-id		|text		|The {{site.data.keyword.DRA_short}} instance toolchain ID.	|Optional			|
|environment-tag     |text   |Tag name that represents the target environment in the inventory. Example: `prod_latest`    |Required           |
|evidence-repo		|tool integration		|The evidence repo URL.	|Optional			|
|git-token		|SECRET		|The Git repo access token.	|Optional			|
|github-token		|SECRET		|The GitHub repo access token.	|Optional			|
|grit-token		|SECRET		|The {{site.data.keyword.gitrepos}} access token.	|Optional			|
|opt-in-sonar		|tool integration		|The Sonarqube scan integration.	|Optional			|
|opt-in-dynamic-scan		|tool integration		|To enable the owasp zap scan.	 |Optional			|
|opt-in-dynamic-ui-scan		|tool integration		|To enable the owasp zap UI scan.	 |Optional			|
|opt-in-dynamic-api-scan		|tool integration		|To enable the owasp zap API scan. 	|Optional			|
|ibmcloud-api-key		|SECRET		|The {{site.data.keyword.cloud_notm}} API key that interacts with the `ibmcloud` CLI tool.	|Required			|
|incident-assignee		|text		|The assignee for the incident issues (GitHub or GitLab username). |Optional			|
|incident-assignees		|text		|The one or more assignees for the incident issues (one or more usernames that are separated by a comma). Note that this parameter can be used only with GitHub and GitLab Premium accounts. |Optional			|
|incident-repo  	|tool integration		|The incident issues repo URL.	|Optional			|
|inventory-repo		|tool integration		|The inventory repo URL.	|Optional			|
|pipeline-config		|text		|The configuration file that customizes pipeline behavior.	|Required			|
|pipeline-config-branch		|text		|The branch of the DevSecOps pipeline configuration.	|Optional			|
|pipeline-config-repo		|text		|The repo URL of the DevSecOps pipeline configuration location.	|Optional			|
|[pipeline-debug](#pipeline-parm-pipeline-debug)		|select		|The pipeline debug mode switch.  |Optional			|
|pipeline-dockerconfigjson		|SECRET		|The base64-encoded Docker `config.json` file that pulls images from a private registry.	|Optional	  	|
|region-prefix          |text   |Region name as prefix for the `latest` tag for the target environment. Example: `us-south`   |Optional            |
|repository-integration		|text		|The integration name for the repo.	|Optional			|
|[slack-notifications](#pipeline-parm-slack-notifications)		|text		|The switch that turns the Slack integration on or off |Optional		|
|sonarqube		|tool integration		|The Sonarqube tool integration.	|Optional			|
|inventory-include    | text | Comma-separated list of the inventory entries that are included for scanning and testing in the CC pipeline. Entries can also be specified by using glob patterns. If not set, all entries are scanned and tested.  |Optional			|
|inventory-exclude    | text | Comma-separated list of the inventory entries that are excluded for scanning and testing in the CC pipeline. Entries can also be specified by using glob patterns.  |Optional			|
{: caption="Table 4. Continuous compliance parameters" caption-side="top"}

## Specifications
{: #pipeline-parm-specs}

### assignee
{: #pipeline-parm-assignee}

This parameter is for the promotion pull request. This value holds the assignee of the change request. This should be a functional id or an email who will be assigned to the change request in the change management system.

### backout-plan
{: #pipeline-parm-backout-plan}

This parameter is for the promotion pull request. Plan of how the change will be rolled back in case of a failure. By default it is empty.

### branch
{: #pipeline-parm-branch}

This is an optional parameter for the CI pipeline. It determines which branch we pull the code from the app repository. By default, it's `master`. 

### change-management-duration
{: #pipeline-parm-change-management-duration}

This is an optional parameter for the CD pipeline. The value of it will be the created change request's maintenance duration. By default it's `30 min`.

### change-request-id
{: #pipeline-parm-change-request-id}

This is an optional parameter for the CD pipeline. By providing a change request ID, the pipeline will skip the creation of a new change request and will use the provided change request. If this parameter is set to `notAvailable` or left empty, a change request is automatically created by the continuous deployment pipeline. By default it's `notAvailable`.

### cluster
{: #pipeline-parm-cluster}

This parameter holds the name of the cluster in the CD pipeline, which is used for the Docker deploy. This parameter is required for the example app.

### cluster-name
{: #pipeline-parm-cluster-name}

This parameter holds the name of the cluster in the CI pipeline, which is used for the Docker deploy. This parameter is required.

### cluster-region
{: #pipeline-parm-cluster-region}

This is a parameter for the CD pipeline. The {{site.data.keyword.cloud_notm}} region that hosts the cluster.	This is a required parameter for the example app.

### compliance-baseimage
{: #pipeline-parm-compliance-baseimage}

This parameter holds the version of the compliance-baseimage. If you want to use something other than our latest image, you can use this parameter.

Example:
`icr.io/continuous-delivery/toolchains/devsecops/baseimage:some-other-tag`

### custom-exempt-label
{: #pipeline-parm-custom-exempt-label}

This is an optional parameter for the CI and CC pipelines. If you marked an incident issue permanently exempted with a custom label, then this parameter should hold the value of the custom label.

### description
{: #pipeline-parm-description}

This parameter is for the promotion pull request. This parameter contains the description of the change, that will be appended to the Change Request Description. By default it is empty.

### impact
{: #pipeline-parm-impact}

This parameter is for the promotion pull request. Additional notes on what this Change Implementation will impact. By default it is empty.

### pipeline-debug
{: #pipeline-parm-pipeline-debug}

If this is set to 1, the pipeline will run in debug mode, and the logs will show more information. By default, it is set to 0.

### priority
{: #pipeline-parm-priority}

This parameter is for the promotion pull request. The priority of the change request. By default it is `'Critical | High | Moderate | Low | Plan'`. You can change it to one of these values.

### purpose
{: #pipeline-parm-purpose}

This parameter is for the promotion pull request. The reason why the change is needed. By default it is empty.

### signing-key
{: #pipeline-parm-signing-key}

This parameter is for the CI pipeline. It is a secret type parameter that holds the base64-encoded private key for signing images by using `.pfx` file data.

### slack-notifications
{: #pipeline-parm-slack-notifications}

Switch to turn on/off the Slack Integration. This is turned off by default, so the value is `0`. Set to `1` to turn it on, set to `0` to turn it off.

### sonarqube
{: #pipeline-parm-sonarqube}

This parameter is for all the pipelines that use static scan stages. This is an integration type parameter. If you are using your own sonarqube integration, select that integration as value. By default, this parameter is empty.

### sonarqube-namespace
{: #pipeline-parm-sonarqube-namespace}

This parameter is for all the pipelines that use static scan stages. This is the configured Kubernetes namespace to use with the SonarQube instance that is created by the pipeline, you can change it to any namespace that you want to use.

### source-environment
{: #pipeline-parm-source-environment}

The source environment that the app is promoted from, which is the source inventory branch of the promotion. The default value is `master`. This value can be overwritten with the usage of a webhook trigger by adding the `source-environment` property to the payload.

### static-scan-retry-count
{: #pipeline-parm-static-scan-retry-count}

This parameter is for any static scan that you want to run in a pipeline. These scans use API calls, which can take a short time to work. Therefore, retry these calls if needed. You can change the times of retries with this parameter. By default, this parameter is set to 30.

### static-scan-retry-sleep
{: #pipeline-parm-static-scan-retry-sleep}

This parameter is for any static scan that you want to run in a pipeline. These scans use API calls, which can take a short time to work. Therefore, retry these calls if needed. You can change the amount of seconds to wait with this parameter. By default, this parameter is set to 10.

### summary-retry-attempts
{: #pipeline-parm-summary-retry-attempts}

Use a retry method to upload the evidence summary to make sure it is successful. You can change the times of retries with this parameter. By default, this parameter is set to 5.


### summary-max-retry-time
{: #pipeline-parm-summary-max-retry-time}

Use a retry method to upload the evidence summary to make sure it is successful. You can change the amount of seconds to wait with this parameter. By default, this parameter is set to 32.

### target-environment
{: #pipeline-parm-target-environment}

The target environment that the app is deployed to, which is the target inventory branch of the promotion. The default value is `prod`. This value can be overwritten with the usage of a webhook trigger by adding the `target-environment` property to the payload.


