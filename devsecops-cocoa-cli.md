---

copyright:
  years: 2021, 2022
lastupdated: "2022-08-17"

keywords: DevSecOps, cli, IBM Cloud

subcollection: devsecops

---

{{site.data.keyword.attribute-definition-list}}

# DevSecOps CLI 
{: #cd-devsecops-cli}

DevSecOps CLI adopts compliance solutions under {{site.data.keyword.cloud}} DevSecOps reference implementation; for example, the shift-left reference pipelines. For more information about the shift-left compliance architecture, see [DevSecOps with {{site.data.keyword.contdelivery_short}}](/docs/devsecops?topic=devsecops-devsecops_intro).
{: shortdesc}

## CLI interface
{: #devsecops-cli-interface}

The CLI uses a multipart structure on the command line that must be specified in this order:

```sh
cocoa <command> <subcommand> [options]
```
{: pre}

1. The base call to the CLI.

2. The main command, which typically corresponds to a service or a module that is used by the shift-left architecture; for example, change request, inventory, or tekton.

3. The subcommand that specifies which action to perform.

4. CLI options required by the action. On condition that they follow steps 1-3, you can specify the CLI options in any order.

Some commands use environment variables to get API Keys or to configure an option value that was not provided in the command line. You need to export these variables before you run the command.
{: important}

Command line options can take various types of input values, such as numbers, strings, Boolean values, arrays, and JSON objects. The options type is dependent upon the command that you specify. Use `cocoa <command> <subcommand> --help` to see the options types.
{: tip}

## CLI commands
{: #devsecops-cli-commands}

Although ServiceNow is documented, it is not supported by the default {{site.data.keyword.cloud_notm}} DevSecOps reference implementation. However, if you want to learn about using ServiceNow with the DevSecOps reference implementation, get help from {{site.data.keyword.cloud_notm}} development teams by joining us on [Slack](https://ic-devops-slack-invite.us-south.devops.cloud.ibm.com/){: external}.
{: note}

### cocoa check pull-request-approval
{: #pull-request-approval}

Checks the approval state of a Pull Request on a commit hash. If the Pull Request was not approved, it creates a new incident issue in the specified repository. The command can identify emergency pull requests marked with a label, in this case it has an exit code of `0`.

Required Environment Variables:

```text
GHE_TOKEN=              # Github Enterprise API Token (Optional if you are using --git-token-path field)
GHE_ORG=                # The owner of the repository
GHE_REPO=               # The repository name
INCIDENT_ISSUE_REPO=    # The incident issue repo name
EMERGENCY_LABEL=        # Emergency Label name
GIT_COMMIT=             # Commit hash of the Pull Request
GITLAB_TOKEN=           # Git Repos and Issue Tracking API Token (Required if you are using 'gitlab' as provider)
GITLAB_URL=             # Git Repos and Issue Tracking URL Example: https://<region>.git.cloud.ibm.com/api/v4 (Required if you are using 'gitlab' as provider)
```
{: screen}

Options:

```text
--label                 # Label to add to possible pr incident issue
--git-provider          # SCM provider (GitHub or GitLab)
--git-token-path        # (Optional) GitHub or GitLab Token's path
--git-api-url           # (Optional) GitHub or GitLab API url
```
{: screen}

If you are using `gitlab`, use `--git-token-path` field to set your GitLab Token and `--git-api-url` field to set the GitLab API URL instead of `GITLAB_TOKEN` and `GITLAB_URL` environment variables. If you are using GitHub, you can use `--git-token-path` field to set your GitHub Token and `--git-api-url` field to set the # GitHub Enterprise API URL instead of `GHE_TOKEN` and `GH_URL` environment variables.
If both of them are provided, `--git-token-path` and `--git-api-url` take precedence.

Running the command:

```sh
cocoa check pull-request-approval
```
{: codeblock}

### cocoa check pull-request-status
{: #pull-request-status}

Checks a given Pull Request's status and the repositories settings.
- **Branch Protection:** Rules to disable force pushing, prevent branches from being deleted, and optionally require status checks before merging.
- **Commit Status Check:** External services to mark commits with an `error`, `failure`, `pending`, or `success` state, which is then reflected in pull requests that involve those commits.
- **Check Runs:** Apps that perform continuous integration, code linting, or code scanning services and provide detailed feedback on commits.

Required Environment Variables:

```text
REQUIRED_CHECKS=        # Minimum required checks to be compliant set by client
GIT_BRANCH=             # Branch name for branch protection check
GIT_COMMIT=             # Commit hash of the Pull Request
GHE_TOKEN=              # Github Enterprise API Token (Optional if you are using --git-token-path field)
GHE_ORG=                # The owner of the repository
GHE_REPO=               # The repository name
```
{: screen}

If you are using `gitlab`, use `--git-token-path` field to set your GitLab Token and `--git-api-url` field to set the GitLab API URL instead of `GITLAB_TOKEN` and `GITLAB_URL` environment variables. If you are using GitHub, you can use `--git-token-path` field to set your GitHub Token and `--git-api-url` field to set the # GitHub Enterprise API URL instead of `GHE_TOKEN` and `GH_URL` environment variables.
If both of them are provided `--git-token-path` and  `--git-api-url` take precedence.

`REQUIRED_CHECKS` Example:

```json
  [
    {
      "type": "status",
      "name": "unit-test",
      "params": {
        "name": "travis/ci"
      }
    },
    {
      "type": "check-run",
      "name": "secret-detection",
      "params": {
        "name": "detect-secrets"
      }
    },
    {
      "type": "branch-protection",
      "name": "code review",
      "params": {
        "checks": ["travis/ci", "detect-secrets"]
      }
    },
    {
      "type": "check-run",
      "name": "open-source-license-scan",
      "params": {
        "name": "ossc/ci"
      }
    }
  ]
```
{: screen}

Running the command:

```sh
 cocoa check pull-request-status
```
{: codeblock}

### cocoa change-request commands
{: #change-request-commands}

The `cocoa change-request` commands support three different change management providers:
- ServiceNow V1
- ServiceNow V3
- GitLab

You can specify the provider by using the `--provider` option with the following values: `servicenow` (default), `servicenow-v3`, or `gitlab`.

Each provider requires different environment variable configuration:
- `servicenow`:
   - `SERVICENOW_TOKEN` - ServiceNow V1 API Token
   - `SERVICENOW_URL` - ServiceNow V1 API URL
- `servicenow-v3`:
   - `IAM_ACCESS_TOKEN` - IBMCloud IAM OAuth Token
   - `SERVICENOW_URL` - ServiceNow V3 API URL
- `gitlab`:
   - `GITLAB_TOKEN` - Gitlab API Token
   - `GITLAB_URL` - Gitlab API URL
   - `GITLAB_ORG` - Owner organization of the change management repository
   - `GITLAB_REPO` - Name of the change management repository

If you are using `gitlab`, use `--git-token-path` field to set your GitLab Token and `--git-api-url` field to set the GitLab API url instead of `GITLAB_TOKEN` and `GITLAB_URL` environment variables. If both of them are provided `--git-token-path` and `--git-api-url` take precedence.

### cocoa change-request get
{: #change-request-get}

Queries a Change Request from the ServiceNow API and saves it to a file or prints it to the console.

Required Environment Variables for ServiceNow v1 or v2:

```text
SERVICENOW_TOKEN= # ServiceNow API Token
SERVICENOW_URL=   # ServiceNow API URL
```
{: screen}

Required Environment Variables for ServiceNow v3:

```text
IAM_ACCESS_TOKEN= # Access token obtained from the IAM service
SERVICENOW_URL=   # ServiceNow API URL
```
{: screen}

Options:

```text
--change-request-id='CHGXXXXXX'  # (Required) The Change Request ID to query from ServiceNow.
--output='filename.json'         # (Optional) If provided, the Change Request data will be written to this file.
--provider='servicenow'          # (Optional) Change Management service provider (choices: 'servicenow', 'servicenow-v3', default: 'servicenow')
```
{: screen}

Running the command:

```sh
# Read Change Request with number 'CHG123456' and output the JSON Data to the console.
$ cocoa change-request get --change-request-id='CHG123456'

# Read Change Request with number 'CHG123456' and output the JSON Data to the file named 'cr.json'.
$ cocoa change-request get --change-request-id='CHG123456' --output='cr.json'

# Read Change Request with number 'CHG123456' using the ServiceNow v3 API
$ cocoa change-request get --change-request-id='CHG123456' --provider='servicenow-v3'
```
{: codeblock}

### cocoa change-request create
{: #change-request-create}

Creates a change request and posts it to the ServiceNow API.

Two usage methods:

* Provide all required CR fields from CLI options.

Options:

```text
--assigned-to      # A ServiceNow validated user (defaults to value found in the pull request)
--system           # the name of the system in ServiceNow
--impact           # impact explanation (defaults to value found in th pull request)
--outage-duration  # duration of the outage (format: 'd HH:mm:ss' or 'none')
--priority         # change priority, valid options: critical, high, moderate, low, planning (defaults to value found in the pull request)
--environment      # the environment for the deployment
--purpose          # purpose explanation (default to value found in the pull request)
--description      # description of the change
--backout-plan     # description of the backout plan (defaults to the value found in the PR)
--planned-start    # planned start time of the change (required format: YYYY-MM-DD HH:mm:ss, e.g 2020-05-13 13:00:12, in UTC)
--planned-end      # planned end time of the change (required format: YYYY-MM-DD HH:mm:ss, e.g 2020-05-13 13:00:12, in UTC)
--deployment-ready # readiness for deployment (choices: 'yes', 'no', default: 'yes')
--type             # change request type, option: standard, emergency
--provider         # (Optional) Change Management service provider (choices: 'servicenow', 'servicenow-v3', 'gitlab', default: 'servicenow')
```
{: screen}

* Provide a file name to parse the change request data from. JSON format is expected.

Options:

```sh
--file --f         # json file name to parse the cr data from
--provider         # (Optional) Change Management service provider (choices: 'servicenow', 'servicenow-v3', 'gitlab', default: 'servicenow')
```
{: screen}

Example file:

```json
{
  "type": "emergency",
  "assignedto": "alexandra.szanto@ibm.com",
  "backoutplan": "rollback",
  "priority": "critical",
  "purpose": "bugfix",
  "description": "test description",
  "environment": "crn:v1:bluemix:public::ch-ctu-2::::",
  "impact": "bug",
  "system": "devopsinsights",
  "outageduration": "0 00:00:00",
  "plannedstart": "2020-10-05 14:48:00",
  "plannedend": "2020-10-05 14:49:00",
  "deploymentready": "yes",
}
```
{: screen}

Required Environment Variables for ServiceNow v1 or v2:

```text
SERVICENOW_TOKEN=   # ServiceNow API Token
SERVICENOW_URL=     # ServiceNow API URL
```
{: screen}

Required Environment Variables for ServiceNow v3:

```text
IAM_ACCESS_TOKEN=   # Access token obtained from the IAM service
SERVICENOW_URL=     # ServiceNow API URL
```
{: screen}

Required Environment Variables for GitLab:

```text
GITLAB_TOKEN=           # Git Repos and Issue Tracking API Token (Required if you are using 'gitlab' as provider)
GITLAB_URL=             # Git Repos and Issue Tracking URL Example: https://<region>.git.cloud.ibm.com/api/v4 (Required if you are using 'gitlab' as provider)
GITLAB_ORG=             # The owner of the repository (Required if you are using 'gitlab' as provider)
GITLAB_REPO=            # The repository name (Required if you are using 'gitlab' as provider)
```
{: screen}

Running the command:

```sh
 cocoa change-request create --file=test.json
```
{: pre}

Or

```sh
$ cocoa change-request create \
  --assigned-to=<your-ibm-email> \
  --system=<servicenow-configuration-item> \
  --impact=<impact description> \
  --outage-duration='0 00:00:00' \
  --priority=<the-priority> \
  --environment=<involved-environment> \
  --purpose=<purpose-explanation> \
  --description=<description-of-the-change> \
  --backout-plan=<description-of-a-backout-plan> \
  --planned-start='2020-05-13 13:00:12' \
  --planned-end='2020-05-13 13:10:12' \
  --deployment-ready=<yes/no> \
  --type=<change-type> \
```
{: codeblock}

### cocoa change-request change-state-to-implement
{: #change-request-change-state-to-implement}

Changes the state of a Change Request to `implement` through the ServiceNow API.

Options:

```text
--provider         # (Optional) Change Management service provider (choices: 'servicenow', 'servicenow-v3', 'gitlab', default: 'servicenow')
```
{: screen}

Required Environment Variables regardless of the provider:

```text
CHANGE_REQUEST_ID=  # ID of the Change Request
```
{: screen}

Required Environment Variables, if you are using ServiceNow v1 or v2 provider:

```text
SERVICENOW_TOKEN=    # ServiceNow API Token
SERVICENOW_URL=      # ServiceNow API URL
```
{: screen}

Required Environment Variables, if you are using ServiceNow v3 provider:

```text
IAM_ACCESS_TOKEN=    # Access token obtained from the IAM service
SERVICENOW_URL=      # ServiceNow API URL
```
{: screen}

Required Environment Variables, if you are using the GitLab provider:

```text
GITLAB_TOKEN=           # Git Repos and Issue Tracking API Token (Required if you are using 'gitlab' as provider)
GITLAB_URL=             # Git Repos and Issue Tracking URL Example: https://<region>.git.cloud.ibm.com/api/v4 (Required if you are using 'gitlab' as provider)
GITLAB_ORG=             # The owner of the repository (Required if you are using 'gitlab' as provider)
GITLAB_REPO=            # The repository name (Required if you are using 'gitlab' as provider)
```
{: screen}

Running the command:

```sh
 cocoa change-request change-state-to-implement
```
{: pre}

### cocoa change-request close
{: #change-request-close}

Closes a Change Request through the ServiceNow API. You can attach 'close notes' by using the `--close-notes` option and specify the 'close category' using the `--close-category` option.

Options:

```text
--close-category  # Choices: "successful" (default), "successful_issues", "unsuccessful", "cancelled"
--close-notes     # String, defaults to "Deployment done."
--provider        # (Optional) Change Management service provider (choices: 'servicenow', 'servicenow-v3', 'gitlab', default: 'servicenow')
```
{: screen}

Required Environment Variables regardless of the provider:

```text
CHANGE_REQUEST_ID=  # ID of the Change Request
```
{: screen}

Required Environment Variables, if you are using ServiceNow v1 or v2 provider:

```text
SERVICENOW_TOKEN=    # ServiceNow API Token
SERVICENOW_URL=      # ServiceNow API URL
```
{: screen}

Required Environment Variables, if you are using ServiceNow v3 provider:

```text
IAM_ACCESS_TOKEN=    # Access token obtained from the IAM service
SERVICENOW_URL=      # ServiceNow API URL
```
{: screen}

Required Environment Variables, if you are using the GitLab provider:

```text
GITLAB_TOKEN=           # Git Repos and Issue Tracking API Token (Required if you are using 'gitlab' as provider)
GITLAB_URL=             # Git Repos and Issue Tracking URL Example: https://<region>.git.cloud.ibm.com/api/v4 (Required if you are using 'gitlab' as provider)
GITLAB_ORG=             # The owner of the repository (Required if you are using 'gitlab' as provider)
GITLAB_REPO=            # The repository name (Required if you are using 'gitlab' as provider)
```

Running the command:

```sh
 cocoa change-request close
```
{: pre}

### cocoa change-request check-approval
{: #change-request-check-approval}

Queries the approval status of a change request. If the Change Request is an emergency, the command does not fail but prompts for a retroactive approval.

Options:

```text
--provider         # (Optional) Change Management service provider (choices: 'servicenow', 'servicenow-v3', 'gitlab', default: 'servicenow')
```
{: screen}

Required Environment Variables regardless of the provider:

```text
CHANGE_REQUEST_ID=  # ID of the Change Request
```
{: screen}

Required Environment Variables, if you are using ServiceNow v1 or v2 provider:

```text
SERVICENOW_TOKEN=    # ServiceNow API Token
SERVICENOW_URL=      # ServiceNow API URL
```
{: screen}

Required Environment Variables, if you are using ServiceNow v3 provider:

```text
IAM_ACCESS_TOKEN=    # Access token obtained from the IAM service
SERVICENOW_URL=      # ServiceNow API URL
```
{: screen}

Required Environment Variables, if you are using the GitLab provider:

```text
GITLAB_TOKEN=           # Git Repos and Issue Tracking API Token (Required if you are using 'gitlab' as provider)
GITLAB_URL=             # Git Repos and Issue Tracking URL Example: https://<region>.git.cloud.ibm.com/api/v4 (Required if you are using 'gitlab' as provider)
GITLAB_ORG=             # The owner of the repository (Required if you are using 'gitlab' as provider)
GITLAB_REPO=            # The repository name (Required if you are using 'gitlab' as provider)
```
{: screen}

Running the command:

```sh
 cocoa change-request check-approval
```
{: pre}

### cocoa change-request prepare
{: #change-request-prepare}

Creates the change request data and prints it to the console or a file.

Required Environment Variables:

```text
GHE_TOKEN=              # Github Enterprise API Token (only required when parsing from pr)
GHE_ORG=                # The owner of the repository (optional, when --org is not provided)
GHE_REPO=               # The repository name (optional, when --repo is not provided)
EMERGENCY_LABEL=        # Emergency Label (optional, when --emergency-label is not provided)
```
{: screen}

The following fields can be parsed from the pull request body:

```text
--assigned-to
--impact
--priority
--purpose
--backout-plan
--type
```
{: screen}

In case they are provided by using the CLI option, the CLI option overrides the value that is found in the pull request. If all of them are provided, pr is not parsed, `--org`, `--repo`, and `GHE_TOKEN` is not necessary.

Options:

```text
--org              # github organization where the parsed pr is (only needed if fields are parsed from pr, defaults to GHE_ORG)
--repo             # github repository name whre the parsed pr is (only needed if fields are parsed from pr, defaults to GHE_REPO)
--pr               # the pull request number to parse (mutually exclusive with sha and branch)
--sha              # infer the pr number from hash (mutually exclusive with pr and branch)
--branch           # infer PR based on the last merged pr into this branch (defaults to master)
--assigned-to      # A ServiceNow validated user (defaults to value found in the pull request)
--system           # the name of the system in ServiceNow
--impact           # implact explanation (defaults to value found in th pull request)
--outage-duration  # duration of the outage
--planned-start    # planned start time of the change (required format: YYYY-MM-DD HH:mm:ss, e.g 2020-05-13 13:00:12, in UTC)
--planned-end      # planned end time of the change (required format: YYYY-MM-DD HH:mm:ss, e.g 2020-05-13 13:00:12, in UTC)
--priority         # change priority, valid options: critical, high, moderate, low, planning (defaults to value found in the pull request)
--environment      # the environment for the deployment
--purpose          # purpose explanation (default to value found in the pull request)
--backout-plan     # description of the backout plan (defaults to the value found in the PR)
--deployment-ready # description of the change
--type             # change request type, option: standard, emergency
--emergency-label  # emergency label to look for in the pull request
--file             # file name to print the cr data to (optional, prints to stdout by default)
```
{: screen}

Running the command:

```sh
$ cocoa cr prepare \
  --org=<github-organization> \
  --repo=<github-repo-name> \
  --pr=<pr number>
```
{: codeblock}

### cocoa change-request request-approval
{: #change-request-request-approval}

Requests approval for records in the new state with approval not requested. When running, the record follows the manual approval path.

Options:

```text
--crid="<ID>"
# or
--change-request-id="<ID>"
--provider         # (Optional) Change Management service provider (choices: 'servicenow', 'servicenow-v3', 'gitlab', default: 'servicenow')
```
{: screen}

Required Environment Variables, if you are using ServiceNow v1 or v2 provider:

```text
SERVICENOW_TOKEN=    # ServiceNow API Token
SERVICENOW_URL=      # ServiceNow API URL
```
{: screen}

Required Environment Variables, if you are using ServiceNow v3 provider:

```text
IAM_ACCESS_TOKEN=    # Access token obtained from the IAM service
SERVICENOW_URL=      # ServiceNow API URL
```
{: screen}

Required Environment Variables, if you are using the GitLab provider:

```text
GITLAB_TOKEN=           # Git Repos and Issue Tracking API Token (Required if you are using 'gitlab' as provider)
GITLAB_URL=             # Git Repos and Issue Tracking URL Example: https://<region>.git.cloud.ibm.com/api/v4 (Required if you are using 'gitlab' as provider)
GITLAB_ORG=             # The owner of the repository (Required if you are using 'gitlab' as provider)
GITLAB_REPO=            # The repository name (Required if you are using 'gitlab' as provider)
```
{: screen}

Running the command:

```sh
 cocoa change-request request-approval --crid="<insert-change-request-id-here>"
```
{: pre}

### cocoa change-request task add
{: #change-request-task-add}

This command is deprecated and will be removed in 2.0.0. Use `cocoa change-request attachment add` instead!
{: important}

Adds a Change Task to a specific Change Request.

Options:

```text
--description      # A more detailed description for the Change Task. [string]
--name             # Name of the Change Task. [string] [required]
--data             # Data for the Change Task or a path prefixed with @ to read the data from. [string] [default: "@/dev/stdin"]
--provider         # (Optional) Change Management service provider (choices: 'servicenow', 'servicenow-v3', 'gitlab', default: 'servicenow')
```
{: screen}

Required Environment Variables, if you are using ServiceNow v1 or v2 provider:

```text
SERVICENOW_TOKEN=    # ServiceNow API Token
SERVICENOW_URL=      # ServiceNow API URL
```
{: screen}

Required Environment Variables, if you are using ServiceNow v3 provider:

```text
IAM_ACCESS_TOKEN=    # Access token obtained from the IAM service
SERVICENOW_URL=      # ServiceNow API URL
```
{: screen}

Required Environment Variables, if you are using the GitLab provider:

```text
GITLAB_TOKEN=           # Git Repos and Issue Tracking API Token (Required if you are using 'gitlab' as provider)
GITLAB_URL=             # Git Repos and Issue Tracking URL Example: https://<region>.git.cloud.ibm.com/api/v4 (Required if you are using 'gitlab' as provider)
GITLAB_ORG=             # The owner of the repository (Required if you are using 'gitlab' as provider)
GITLAB_REPO=            # The repository name (Required if you are using 'gitlab' as provider)
```
{: screen}

Running the command:

```sh
$ cocoa change-request task add "CHGXXXXXXX" \
        --name='<task-name>' \
        --data='<data-for-the-task>'

$ cat data.txt | cocoa change-request task add "CHGXXXXXXX" --name='<task-name>'

$ cocoa change-request task add "CHGXXXXXXX" \
        --name='<task-name>' \
        --data='@<path/to/file>'
```
{: pre}

### cocoa change-request task get
{: #change-request-task-get}

This command is deprecated and will be removed in 2.0.0. Use `cocoa change-request attachment get` instead!
{: important}

Gets a specific Change Task of a Change Request.

Options:

```text
--provider         # (Optional) Change Management service provider (choices: 'servicenow', 'servicenow-v3', 'gitlab', default: 'servicenow')
```
{: screen}

Required Environment Variables, if you are using ServiceNow v1 or v2 provider:

```text
SERVICENOW_TOKEN=    # ServiceNow API Token
SERVICENOW_URL=      # ServiceNow API URL
```
{: screen}

Required Environment Variables, if you are using ServiceNow v3 provider:

```text
IAM_ACCESS_TOKEN=    # Access token obtained from the IAM service
SERVICENOW_URL=      # ServiceNow API URL
```
{: screen}

Required Environment Variables, if you are using the GitLab provider:

```text
GITLAB_TOKEN=        # Git Repos and Issue Tracking API Token (Required if you are using 'gitlab' as provider)
GITLAB_URL=          # Git Repos and Issue Tracking URL Example: https://<region>.git.cloud.ibm.com/api/v4 (Required if you are using 'gitlab' as provider)
GITLAB_ORG=          # The owner of the repository (Required if you are using 'gitlab' as provider)
GITLAB_REPO=         # The repository name (Required if you are using 'gitlab' as provider)
```
{: screen}

Running the command:

```sh
 cocoa change-request task get "CHGXXXXXXX" "CTASKXXXXXXX"
```
{: pre}

### cocoa change-request task list
{: #change-request-task-list}

This command is deprecated and will be removed in 2.0.0. Use `cocoa change-request attachment list` instead!
{: important}

Lists Change Tasks for a Change Request.

Options:

```text
--provider         # (Optional) Change Management service provider (choices: 'servicenow', 'servicenow-v3', default: 'servicenow')
```
{: screen}

Required Environment Variables, if you are using ServiceNow v1 or v2 provider:

```text
SERVICENOW_TOKEN=    # ServiceNow API Token
SERVICENOW_URL=      # ServiceNow API URL
```
{: screen}

Required Environment Variables, if you are using ServiceNow v3 provider:

```text
IAM_ACCESS_TOKEN=    # Access token obtained from the IAM service
SERVICENOW_URL=      # ServiceNow API URL
```
{: screen}

Running the command:

```sh
 cocoa change-request task list "<insert-change-request-id-here>"
```
{: pre}

### cocoa change-request task update
{: #change-request-task-update}

This command is deprecated and will be removed in 2.0.0. Use `cocoa change-request attachment update` instead!
{: important}

Updates a Change Task in a specific Change Request.

Options:

```text
--change-request-id  # The change request the change task belongs to [string] [required]
--change-task-id     # The change task id to be updated [string] [required]
--description        # A more detailed description for the Change Task. [string]
--data               # Data for the Change Task or a path prefixed with @ to read the data from. [string]
--provider           # (Optional) Change Management service provider (choices: 'servicenow', 'servicenow-v3', default: 'servicenow')
```
{: screen}

Required Environment Variables, if you are using ServiceNow v1 or v2 provider:

```text
SERVICENOW_TOKEN=    # ServiceNow API Token
SERVICENOW_URL=      # ServiceNow API URL
```
{: screen}

Required Environment Variables, if you are using ServiceNow v3 provider:

```text
IAM_ACCESS_TOKEN=    # Access token obtained from the IAM service
SERVICENOW_URL=      # ServiceNow API URL
```
{: screen}

Running the command:

```sh
$ cocoa change-request task update
        --change-request-id='CHGXXXXXXX' \
        --change-task-id='CTASKXXXXXXX' \
        --data='<data-for-the-task>' \
        --description='<description>'

$ cat data.txt | cocoa change-request task update --change-request-id='CHGXXXXXXX' --change-task-id='CTASKXXXXXXX' --data='@/dev/stdin'

$ cocoa change-request task update --change-request-id='CHGXXXXXXX' --change-task-id='CTASKXXXXXXX' --data='@<path/to/file>'
```
{: codeblock}

### cocoa change-request attachment add
{: #change-request-attachment-add}

Adds a Change attachment to a specific Change Request.

Options:

```text
--description      # A more detailed description for the Change Task. [string]
--name             # Name of the Change Task. [string] [required]
--data             # Data for the Change Task or a path prefixed with @ to read the data from. [string] [default: "@/dev/stdin"]
--provider         # (Optional) Change Management service provider (choices: 'servicenow', 'servicenow-v3', 'gitlab', default: 'servicenow')
```
{: screen}

Required Environment Variables, if you are using ServiceNow v1 or v2 provider:

```text
SERVICENOW_TOKEN=    # ServiceNow API Token
SERVICENOW_URL=      # ServiceNow API URL
```
{: screen}

Required Environment Variables, if you are using ServiceNow v3 provider:

```text
IAM_ACCESS_TOKEN=    # Access token obtained from the IAM service
SERVICENOW_URL=      # ServiceNow API URL
```
{: screen}

Required Environment Variables, if you are using the GitLab provider:

```text
GITLAB_TOKEN=        # Git Repos and Issue Tracking API Token (Required if you are using 'gitlab' as provider)
GITLAB_URL=          # Git Repos and Issue Tracking URL Example: https://<region>.git.cloud.ibm.com/api/v4 (Required if you are using 'gitlab' as provider)
GITLAB_ORG=          # The owner of the repository (Required if you are using 'gitlab' as provider)
GITLAB_REPO=         # The repository name (Required if you are using 'gitlab' as provider)
```
{: screen}

Running the command:

```sh
$ cocoa change-request attachment add "CHGXXXXXXX" \
        --name='<attachment-name>' \
        --data='<data-for-the-attachment>'

$ cat data.txt | cocoa change-request attachment add "CHGXXXXXXX" --name='<attachment-name>'

$ cocoa change-request attachment add "CHGXXXXXXX" \
        --name='<attachment-name>' \
        --data='@<path/to/file>'
```

### cocoa change-request attachment get
{: #change-request-attachment-get}

Gets a specific Change attachment of a Change Request.

Options:

```text
--provider         # (Optional) Change Management service provider (choices: 'servicenow', 'servicenow-v3', 'gitlab', default: 'servicenow')
```
{: screen}

Required Environment Variables, if you are using ServiceNow v1 or v2 provider:

```text
SERVICENOW_TOKEN=    # ServiceNow API Token
SERVICENOW_URL=      # ServiceNow API URL
```
{: screen}

Required Environment Variables, if you are using ServiceNow v3 provider:

```text
IAM_ACCESS_TOKEN=    # Access token obtained from the IAM service
SERVICENOW_URL=      # ServiceNow API URL
```
{: screen}

Required Environment Variables, if you are using the GitLab provider:

```text
GITLAB_TOKEN=        # Git Repos and Issue Tracking API Token (Required if you are using 'gitlab' as provider)
GITLAB_URL=          # Git Repos and Issue Tracking URL Example: https://<region>.git.cloud.ibm.com/api/v4 (Required if you are using 'gitlab' as provider)
GITLAB_ORG=          # The owner of the repository (Required if you are using 'gitlab' as provider)
GITLAB_REPO=         # The repository name (Required if you are using 'gitlab' as provider)
```
{: screen}

Running the command:

```sh
 cocoa change-request attachment get "CHGXXXXXXX" "CTASKXXXXXXX"
```

### cocoa change-request attachment list
{: #change-request-attachment-list}

Lists Change attachments for a Change Request.

Options:

```text
--provider         # (Optional) Change Management service provider (choices: 'servicenow', 'servicenow-v3', default: 'servicenow')
```
{: screen}

Required Environment Variables, if you are using ServiceNow v1 or v2 provider:

```text
SERVICENOW_TOKEN=    # ServiceNow API Token
SERVICENOW_URL=      # ServiceNow API URL
```
{: screen}

Required Environment Variables, if you are using ServiceNow v3 provider:

```text
IAM_ACCESS_TOKEN=    # Access token obtained from the IAM service
SERVICENOW_URL=      # ServiceNow API URL
```
{: screen}

Running the command:

```sh
 cocoa change-request attachment list "<insert-change-request-id-here>"
```

### cocoa change-request attachment update
{: #change-request-attachment-update}

Updates a Change attachment in a specific Change Request.

Options:

```text
--change-request-id  # The change request the change task belongs to [string] [required]
--change-task-id     # The change task id to be updated [string] [required]
--description        # A more detailed description for the Change Task. [string]
--data               # Data for the Change Task or a path prefixed with @ to read the data from. [string]
--provider           # (Optional) Change Management service provider (choices: 'servicenow', 'servicenow-v3', default: 'servicenow')
```
{: screen}

Required Environment Variables, if you are using ServiceNow v1 or v2 provider:

```text
SERVICENOW_TOKEN=    # ServiceNow API Token
SERVICENOW_URL=      # ServiceNow API URL
```
{: screen}

Required Environment Variables, if you are using ServiceNow v3 provider:

```text
IAM_ACCESS_TOKEN=    # Access token obtained from the IAM service
SERVICENOW_URL=      # ServiceNow API URL
```
{: screen}

Running the command:

```sh
$ cocoa change-request attachment update
        --change-request-id='CHGXXXXXXX' \
        --change-attachment-id='CTASKXXXXXXX' \
        --data='<data-for-the-attachment>' \
        --description='<description>'

$ cat data.txt | cocoa change-request attachment update --change-request-id='CHGXXXXXXX' --change-attachment-id='CTASKXXXXXXX' --data='@/dev/stdin'

$ cocoa change-request attachment update --change-request-id='CHGXXXXXXX' --change-attachment-id='CTASKXXXXXXX' --data='@<path/to/file>'
```

### cocoa evidence create
{: #evidence-create}

Creates evidence from the specified options and saves it to a file or prints it to the console.

Running the command:

```sh
$ cocoa evidence create \
  --namespace=[choices: "ci", "cd"] \               # Pipeline type the evidence was collected from
  --evidence-name=<evidence-name> \                 # It will be used to name evidence-file in git
  --evidence-type=<evidence-type> \                 # e.g: com.ibm.unit_test
  --evidence-type-version=<evidence-type-version> \
  --result=[choices: "failure", "success"] \        # The result of the scan/test
  --issue=<issue-url> \                             # e.g: --issue=foo --issue=bar || --issue=foo
  --artifact=<artifact_url> <artifact_hash>\        # Url and hash pair(s) to the test artifact(s)
  --log=<log_url> <log_hash> \                      # Url and hash pair(s) to the log(s)
  --pipeline-id=<pipeline-id> \
  --pipeline-run-id=<pipeline-run-id> \
  --pipeline-run-url=<pipeline-run-url> \
  --toolchain-crn=<toolchain-crn> \                 # Name of the toolchain cloud resource
  --output=<filename>                                # If present, the evidence will be saved to the given file.
```
{: codeblock}

CLI options can be also set from environment variables except for `issue`, `log`, and `artifact`.
Multiple issues or artifacts must be provided with multiple issue or artifact flags. For example:

```sh
 cocoa evidence create --artifact <url> <hash>  --artifact <url> <hash> --issue <issue-url> --issue <issue-url>
```
{: pre}

`artifact` consists of a URL that points to the artifact file and a hash, they should be provided the same way as in the previous example.
{: note}

### cocoa evidence format-summary
{: #evidence-format-summary}

Formats the evidence summary into a human readable format. It can be fed into the change request content.

Running the command:

```sh
$ cocoa evidence format-summary \
  --input=<filepath> \          # (default: -, referring to stdin) If present, the formatted evidence will be read from the given file.
  --output=<filepath>           # (default: -, referring to stdout) If present, the formatted evidence will be saved to the given file.
```
{: codeblock}

```sh
# Reading from stdin and printing to stdout:
$ cat raw-summary.json | cocoa evidence format-summary -i - -o - | tee formatted-summary.txt
$ cat raw-summary.json | cocoa evidence format-summary | tee formatted-summary.txt
# Reading to and from file:
$ cocoa evidence format-summary --input=raw-summary.json --output=formatted-summary.txt
```
{: codeblock}

### cocoa evidence upload
{: #evidence-upload}

Uploads evidence to the specified backends. Backends can be different evidence locker types, for example {{site.data.keyword.cos_short}}, GitHub or {{site.data.keyword.DRA_short}}. Different lockers require different parameters to be provided.

{{site.data.keyword.DRA_short}} is not yet supported.
{: note}

Required Options:

```text
--backend
```
{: screen}

Required Environment Variables:

```text
EVIDENCE_REPO_ORG=  # Evidence repo org (Required if you are using 'git' backend)
EVIDENCE_REPO_NAME= # Evidence repo name (Required if you are using 'git' backend)
```
{: screen}

Required Environment Variables, if you are using GitHub:

```text
GHE_TOKEN=          # Github Enterprise API Token (Optional if you are using --git-token-path)
```
{: screen}

Required Environment Variables, if you are using GitLab:

```text
GITLAB_TOKEN=        # Git Repos and Issue Tracking API Token (Required if you are using 'gitlab' as provider)
GITLAB_URL=          # Git Repos and Issue Tracking URL Example: https://<region>.git.cloud.ibm.com/api/v4 (Required if you are using 'gitlab' as provider)
```
{: screen}

Required Environment Variables, if you are using Cloud Object Storage:

```text
COS_API_KEY=        # Cloud Object Storage API Key (Required if you are using 'cos' backend)
COS_BUCKET_NAME=    # Bucket Name where the evidence will be uploaded in the COS Instance (Required if you are using 'cos' backend)
COS_ENDPOINT=       # The COS API Endpoint matching the region where the bucket is located (Required if you are using 'cos' backend)
```
{: screen}

If you are using `gitlab`, use `--git-token-path` field to set your GitLab Token and `--git-api-url` field to set the GitLab API URL instead of `GITLAB_TOKEN` and `GITLAB_URL` environment variables. If you are using `github`, use `--git-token-path` field to set your GitHub Token and `--git-api-url` field to set the # GitHub Enterprise API URL instead of `GHE_TOKEN` and `GH_URL` environment variables.
If both of them are provided, `--git-token-path` and `--git-api-url` take precedence.

Running the command:

```sh
$ cocoa evidence upload

$ cocoa evidence upload \
  --backend=[choices: "git", "insights", "cos"] \   # e.g. --backend=cos,git,insights
  --namespace=[choices: "ci", "cd"] \               # Pipeline type the evidence was collected from
  --evidence-name=<evidence-name> \                 # It will be used to name evidence-file in git
  --evidence-type=<evidence-type> \                 # e.g: com.ibm.unit_test
  --evidence-type-version=<evidence-type-version> \
  --result=[choices: "failure", "success"] \        # The result of the scan/test
  --issue=<issue-url> \                             # e.g: --issue=foo --issue=bar || --issue=foo
  --artifact=<artifact_url> <artifact_hash>\        # Url and hash pair(s) to the test artifact(s)
  --log=<log_url> <log_hash> \                      # Url and hash pair(s) to the log(s)
  --pipeline-id=<pipeline-id> \
  --pipeline-run-id=<pipeline-run-id> \
  --pipeline-run-url=<pipeline-run-url> \
  --toolchain-crn=<toolchain-crn>                   # Name of the toolchain cloud resource
```
{: codeblock}

CLI options can be also set from environment variables except for `backend`, `issue`, `log`, and `artifact`. Multiple issues or artifacts must be provided with multiple issue or artifact flags. For example:

```sh
 cocoa evidence upload --backend=git --artifact <url> <hash>  --artifact <url> <hash> --issue <issue-url> --issue <issue-url> --git-provider='gitlab' 
```
{: codeblock}

`artifact` consists of a URL that points to the artifact file and a hash, provide these items in the same way as in the previous example.
{: note}

### cocoa evidence summarize
{: #evidence-summaries}

Queries evidence from the Evidence Locker and generates an Evidence Summary from their contents. Backends can be different evidence locker types, such as {{site.data.keyword.cos_short}} or GitHub. Currently, `git` and `cos` are supported as backend. Different lockers require different parameters to be provided.

Required Environment Variables for Git:

```text
EVIDENCE_REPO_ORG=  # Use this environment varibale to provide the owner organisation of the evidence repository (Required if you are using 'git' backend)
EVIDENCE_REPO_NAME= # Use this environment varibale to provide the name of the evidence repository (Required if you are using 'git' backend)
PIPELINE_RUN_ID=    # Can be used instead of '--pipeline-run-id' option
TOOLCHAIN_CRN=      # Can be used instead of '--toolchain-crn' option
```
{: screen}

Required Environment Variables, if you are using GitHub:

```text
GHE_TOKEN=          # Github Enterprise API Token (Optional if you are using --git-token-path)
```
{: screen}

Required Environment Variables, if you are using GitLab:

```text
GITLAB_TOKEN=        # Git Repos and Issue Tracking API Token (Required if you are using 'gitlab' as provider)
GITLAB_URL=          # Git Repos and Issue Tracking URL Example: https://<region>.git.cloud.ibm.com/api/v4 (Required if you are using 'gitlab' as provider)
```
{: screen}

Options for Git:

```text
--backend           # (Required) Specifies the type of locker from where the evidences are queried
--repo              # Evidence Repositories name (can be substituted by EVIDENCE_REPO_NAME environment variable)
--org               # Evidence Repositories owner organisation (can be substituted by EVIDENCE_REPO_ORG environment variable)
--pipeline-run-id   # (Required)
--toolchain-crn     # (Required)
--prefix-list       # (Required) a list of paths separated by comma where evidences can be found in the repository
--output            # (Optional) file name to write the evidence summary into
--git-token-path    # (Optional) Github or Gitlab Token's path
--git-api-url       # (Optional) Github or Gitlab API url
```
{: screen}

If you are using `gitlab`, use `--git-token-path` field to set your GitLab Token and `--git-api-url` field to set the GitLab API URL instead of `GITLAB_TOKEN` and `GITLAB_URL` environment variables. If you are using `github`, use `--git-token-path` field to set your GitHub Token and `--git-api-url` field to set the # GitHub Enterprise API URL instead of `GHE_TOKEN` and `GH_URL` environment variables.
If both of them are provided `--git-token-path` and  `--git-api-url` take precedence.

By default, the CLI on each invocation ensures that you work with an up-to-date version of a Git locker. This behavior can be disabled by setting `COCOA_USE_CACHE` to any value except `0`, `false`, `no`, or `n`. In this case, the CLI uses its internal cache to look up evidence (much faster), but results might be stale.

Required Environment Variables, if you are using Cloud Object Storage:

```text
COS_API_KEY=      # COS api token
COS_BUCKET_NAME=  # COS bucket name where evidences are stored
COS_ENDPOINT=     # COS endpoint where the bucket is available
PIPELINE_RUN_ID=  # Can be used instead of '--pipeline-run-id' option
TOOLCHAIN_CRN=    # Can be used instead of '--toolchain-crn' option
```
{: screen}

Options for cos:

```text
--backend         # (Required) Specifies the type of locker from where the evidences are queried
--pipeline-run-id # (Required)
--toolchain-crn   # (Required)
--prefix-list     # (Required) a list of paths separated by comma where evidences can be found in the repository
--output          # (Optional) file name to write the evidence summary into
```
{: screen}

Running the command:

```sh
# Save the evidence summary to 'summary.json' file.
$ cocoa evidence summarize \
  --backend='git' \
  --pipeline-run-id='id-123-123' \
  --toolchain-crn='crn-123-123' \
  --prefix-list='raw/ci/pipeline-run-id-1,raw/ci/pipeline-run-id-2' \
  --output='summary.json'

# Print the evidence summary to the console
$ cocoa evidence summarize \
  --backend='git' \
  --pipeline-run-id='id-123-123' \
  --toolchain-crn='crn-123-123' \
  --prefix-list='raw/ci/pipeline-run-id-1,raw/ci/pipeline-run-id-2'
```
{: codeblock}

### cocoa artifact upload
{: #artifact-upload}

Uploads an artifact to the specified backends. Backends can be different evidence locker types, for example {{site.data.keyword.cos_short}}, GitHub or {{site.data.keyword.DRA_short}}. Different lockers require different parameters to be provided.

Options:

```text
--backend           # (Required) Specifies the types of lockers we upload the artifact ('cos', 'git')
--pipeline-run-id   # The ID of the PipelineRun running the CLI command
--namespace         # Specifies the pipeline the evidence is collected from
--upload-path       # If present, it will override the artifact name with the specified value
```
{: screen}

Options for Git:

```text
--git-token-path    #(Optional) Github or Gitlab Token's path
--git-api-url           #(Optional) Github or Gitlab API url
```
{: screen}

Required Environment Variables:

```text
PIPELINE_RUN_ID=    # Can be used instead of '--pipeline-run-id' option
NAMESPACE=          # Can be used instead of '--namespace' option
```
{: screen}

Required Environment Variables, if you are using 'git' provider:

```text
EVIDENCE_REPO_ORG=  # The Git repo org (Required if you are using 'git' backend)
EVIDENCE_REPO_NAME= # The Git repo name (Required if you are using 'git' backend)
```
{: screen}

Required Environment Variables, if you are using GitHub:

```text
GHE_TOKEN=          # Github Enterprise API Token (Optional if you are using --git-token-path)
```
{: screen}

Required Environment Variables, if you are using GitLab:

```text
GITLAB_TOKEN=       # Git Repos and Issue Tracking API Token (Required if you are using 'gitlab' as git-provider)
GITLAB_URL=         # Git Repos and Issue Tracking URL Example: https://<region>.git.cloud.ibm.com/api/v4 (Required if you are using 'gitlab' as git-provider)
```
{: screen}

Required Environment Variables, if you are using Cloud Object Storage:

```text
COS_API_KEY=        # Cloud Object Storage API Key (Required if you are using 'cos' backend)
COS_BUCKET_NAME=    # Bucket Name where the artifact will be uploaded in the COS Instance (Required if you are using 'cos' backend)
COS_ENDPOINT=       # The COS API Endpoint matching the region where the bucket is located (Required if you are using 'cos' backend)
```
{: screen}

If you are using `gitlab`, use `--git-token-path` field to set your GitLab Token and `--git-api-url` field to set the GitLab API URL instead of `GITLAB_TOKEN` and `GITLAB_URL` environment variables. If you are using `github`, use `--git-token-path` field to set your GitHub Token and `--git-api-url` field to set the # GitHub Enterprise API URL instead of `GHE_TOKEN` and `GH_URL` environment variables.
If both of them are provided `--git-token-path` and  `--git-api-url` take precedence.

Running the command:

```sh
$ cocoa artifact upload

$ cocoa artifact upload \
  --backend=[choices: "git", "cos"] \ # e.g. --backend=cos,git
  --pipeline-run-id=<pipeline-run-id> # can be substituted by PIPELINE_RUN_ID
  --namespace=[choices: "ci", "cd"] \ # Pipeline type the evidence was collected from, can be substituted by NAMESPACE
  <file-path>
```
{: codeblock}

CLI options can be also set from environment variables except for `backend` and `upload-path`.

### cocoa set-status
{: #set-status}

Sets a commit's status. The current implementation is tested on GitHub. See documentation about [GitHub statuses](https://docs.github.com/en/rest/reference/repos#statuses){: external}.

Required Environment Variables:

```text
GHE_ORG=      # Github organization/username
GHE_REPO=     # Github repository
GHE_COMMIT=   # Github commit hash
```
{: screen}

Required Environment Variables, if you are using GitHub:

```text
GHE_TOKEN=    # Github token (Optional if you are using --git-token-path)
```
{: screen}

Required Environment Variables, if you are using GitLab:

```text
GITLAB_TOKEN=     # Git Repos and Issue Tracking API Token (Required if you are using 'gitlab' as git-provider)
GITLAB_URL=       # Git Repos and Issue Tracking URL Example: https://<region>.git.cloud.ibm.com/api/v4 (Required if you are using 'gitlab' as git-provider)
```
{: screen}

If you are using `gitlab`, use `--git-token-path` field to set your GitLab Token and `--git-api-url` field to set the GitLab API URL instead of `GITLAB_TOKEN` and `GITLAB_URL` environment variables. If you are using `github`, use `--git-token-path` field to set your GitHub Token and `--git-api-url` field to set the # GitHub Enterprise API URL instead of `GHE_TOKEN` and `GH_URL` environment variables.
If both of them are provided `--git-token-path` and  `--git-api-url` take precedence.

Options for Git:

```text
--git-token-path    #(Optional) Github or Gitlab Token's path
--git-api-url           #(Optional) Github or Gitlab API url
```
{: screen}

Running the command:

```sh
$ cocoa set-status \
 --state="pending" \
 --targetURL="https://cloud.ibm.com/devops/pipelines/tekton/some-toolchain/runs/some-pipelinerun/lint/lint?env_id=ibm:yp:us-south" \
 --context="tekton/lint" \
 --description="Tekton linter is running."
```
{: codeblock}

### cocoa inventory add
{: #inventory-add}

Adds a value to the inventory repository. Creates a file with the `name` option, if it does not exist otherwise overwrites it.
Currently, the following fields are optional parameters: `type`, `sha256`, `provenance`, and `signature`. However, after the upcoming breaking changes, they are mandatory.

Required Environment Variables:

```text
GHE_ORG=     # Github Organization (required if --org option is not specified).
GHE_REPO=    # Github Repository (required if --repo option is not specified).
```
{: screen}

Required Environment Variables, if you are using GitHub:

```text
GHE_TOKEN=    # Github Enterprise API Token(Optional if you are using --git-token-path)
```
{: screen}

Required Environment Variables, if you are using GitLab:

```text
GITLAB_TOKEN=     # Git Repos and Issue Tracking API Token (Required if you are using 'gitlab' as git-provider)
GITLAB_URL=       # Git Repos and Issue Tracking URL Example: https://<region>.git.cloud.ibm.com/api/v4 (Required if you are using 'gitlab' as git-provider)
```
{: screen}

Options for Git:

```text
--git-token-path    #(Optional) Github or Gitlab Token's path
--git-api-url           #(Optional) Github or Gitlab API url
```
{: screen}

| Option           | Description                                      | Value type | Required or default value |
| ---------------- | ------------------------------------------------ | ---------- | ------------------------- |
| artifact         | Artifact name.                                   | String | Required |
| version          | The version of the application.            | String | Required |
| repository-url   | The repository of the application.               | String | Required |
| pipeline-run-id  | The id of the pipeline run.                       | String | Required |
| commit-sha       | The commit of the application repository from which the artifact is built | String | Required |
| name             | The name of the application the artifact belongs to | String | Required |
| build-number     | The number of the build.                         | number | Required |
| org              | The GitHub organization that owns the inventory repository. | String | |
| repo             | The name of the inventory repository.            | String | |
| app-artifacts    | Arbitrary app content in JSON format             | String | |
| type             | Type of the artifact                             | String | |
| sha256           | The sha256 hash of the artifact                  | String | |
| provenance       | URL pointing to the artifact (for example, built image)   | String | |
| signature        | The artifact's signature                         | String | |
| environment      | The name of the environment where the entry should be added. | String | "master" |
| git-provider*    | The Git version control service provider, either GitHub or GitLab | String* | "github" |
| git-token-path   | Git token path to read the secret from           | String | |
| git-api-url      | Git API URL                                      | String | |
{: caption="Table 1. Options for git" caption-side="bottom"}

Running the command:

```sh
$ cocoa inventory add \
  --artifact=cocoa-script-cli \
  --type=type \
  --sha256=786800e8e48938664fe2397ca14ab8dabd48f34656ef5cfda4143b4519cb714f \
  --signature=123123valid \
  --name=cocoa-cli \
  --repository-url=http://validURL.com \
  --commit-sha=8e86dc4647ce28632103dce46b756c70d339349a \
  --version=v4 \
  --build-number=33 \
  --pipeline-run-id=123123valid \
  --git-token-path=./git-token \
  --org=test \
  --app-artifacts='{"app": "test", "some_value": "value"}' \
  --repo=repository
```
{: codeblock}

```sh
$ cocoa inventory add \
  --environment=environment \
  --artifact=foo-helm-chart/foo/chart.yaml \
  --repository-url=http://validURL.com \
  --commit-sha=786800e8e48938664fe2397ca14ab8dabd48f34656ef5cfda4143b4519cb714f \
  --build-number=33 \
  --pipeline-run-id=123123valid \
  --version=v4 \
  --name=chart
```
{: codeblock}

### cocoa inventory get
{: #inventory-get}

Gets an entry from the inventory repository. The target can be a specific version or an environment. Either use `--version` or `--environment`, and use only one of them. If the `--property` option is missing, the whole content of the inventory entry is printed.

Required Environment Variables, if you are using GitHub:

```text
GHE_TOKEN=    # Github Enterprise API Token (Optional if you are using --git-token-path)
```
{: screen}

Required Environment Variables, if you are using GitLab:

```text
GITLAB_TOKEN= # Git Repos and Issue Tracking API Token (Required if you are using 'gitlab' as git-provider)
GITLAB_URL=   # Git Repos and Issue Tracking URL Example: https://<region>.git.cloud.ibm.com/api/v4 (Required if you are using 'gitlab' as git-provider)
```
{: screen}

Options for Git:

```text
--git-token-path       #(Optional) Github or Gitlab Token's path
--git-api-url          #(Optional) Github or Gitlab API url
```
{: screen}

If you are using `gitlab`, use `--git-token-path` field to set your GitLab Token and `--git-api-url` field to set the GitLab API URL instead of `GITLAB_TOKEN` and `GITLAB_URL` environment variables. If you are using `github`, use `--git-token-path` field to set your GitHub Token and `--git-api-url` field to set the # GitHub Enterprise API URL instead of `GHE_TOKEN` and `GH_URL` environment variables.
If both of them are provided `--git-token-path` and  `--git-api-url` take precedence.

By default, the CLI on each invocation ensures that you work with an up-to-date version of a Git locker. This behavior can be disabled by setting `COCOA_USE_CACHE` to any value except `0`, `false`, `no`, or `n`. In this case, the CLI uses its internal cache to look up evidence (much faster), but results might be stale.

Running the command:

```sh
# Uses the environment as target, and pretty-prints only a given property of the inventory entry to the console.
$ cocoa inventory get \
  --org='Github-ID' \
  --repo='compliance-inventory-repo' \
  --entry='hello-compliance-app' \
  --environment='master' \
  --property='name'

# Uses the version as target, and pretty-prints the whole content of the inventory entry to the console.
$ cocoa inventory get \
  --org='Github-ID' \
  --repo='compliance-inventory-repo' \
  --entry='hello-compliance-app' \
  --version='v1'
```
{: codeblock}

### cocoa inventory get-sha
{: #get-sha}

Gets the latest commit hash by a label or environment from the inventory repository. Either use `--label` or `--environment`, and use only one of them.

Required Environment Variables, if you are using GitHub:

```text
GHE_TOKEN=    # Github Enterprise API Token (Optional if you are using --git-token-path)
```
{: screen}

Required Environment Variables, if you are using GitLab:

```text
GITLAB_TOKEN= # Git Repos and Issue Tracking API Token (Required if you are using 'gitlab' as git-provider)
GITLAB_URL=   # Git Repos and Issue Tracking URL Example: https://<region>.git.cloud.ibm.com/api/v4 (Required if you are using 'gitlab' as git-provider)
```
{: screen}

Options for Git:

```text
--git-token-path    #(Optional) Github or Gitlab Token's path
--git-api-url       #(Optional) Github or Gitlab API url
```
{: screen}

If you are using `gitlab`, use `--git-token-path` field to set your Gitlab Token and `--git-api-url` field to set the GitLab API URL instead of `GITLAB_TOKEN` and `GITLAB_URL` environment variables. If you are using `github`, use `--git-token-path` field to set your GitHub Token and `--git-api-url` field to set the # GitHub Enterprise API URL instead of `GHE_TOKEN` and `GH_URL` environment variables.
If both of them are provided `--git-token-path` and  `--git-api-url` take precedence.

Running the command:

```sh
# Get the latest commit hash of the staging branch
$ cocoa inventory get-sha \
  --org='Github-ID' \
  --repo='compliance-inventory-repo' \
  --environment='staging'

# Get the commit hash of the "pipeline-run-id" label
$ cocoa inventory get-sha \
  --org='Github-ID' \
  --repo='compliance-inventory-repo' \
  --label='pipeline-run-id'
```
{: codeblock}

### cocoa inventory label
{: #inventory-label}

Adds a label to an inventory entry, or moves a label in the inventory. The target can be the latest commit on a specific branch or another label.

Options:

```text
--org          # The Github organisation which owns the inventory repository.
--repo         # The name of the inventory repository.
--environment  # The inventory branch
--to-label     # Another label in the inventory, that will be looked up/removed
```
{: screen}

Required Environment Variables:

```text
GHE_ORG=     # Github Organization (required if --org option is not specified).
GHE_REPO=    # Github Repository (required if --repo option is not specified).
```
{: screen}

Required Environment Variables, if you are using GitHub:

```text
GHE_TOKEN=    # Github Enterprise API Token (Optional if you are using --git-token-path)
```
{: screen}

Required Environment Variables, if you are using GitLab:

```text
GITLAB_TOKEN=   # Git Repos and Issue Tracking API Token (Required if you are using 'gitlab' as git-provider)
GITLAB_URL=     # Git Repos and Issue Tracking URL Example: https://<region>.git.cloud.ibm.com/api/v4 (Required if you are using 'gitlab' as git-provider)
```
{: screen}

Options for Git:

```text
--git-token-path    #(Optional) Github or Gitlab Token's path
--git-api-url           #(Optional) Github or Gitlab API url
```
{: screen}

If you are using `gitlab`, use `--git-token-path` field to set your GitLab Token and `--git-api-url` field to set the GitLab API URL instead of `GITLAB_TOKEN` and `GITLAB_URL` environment variables. If you are using `github`, use `--git-token-path` field to set your GitHub Token and `--git-api-url` field to set the # GitHub Enterprise API URL instead of `GHE_TOKEN` and `GH_URL` environment variables.
If both of them are provided `--git-token-path` and  `--git-api-url` take precedence.
{: screen}

Running the command:

```sh
# label the latest state on "staging" branch
$ cocoa inventory label add \
  --org='Github-ID' \
  --repo='compliance-inventory-repo' \
  --environment='staging' \
  "new-label-to-add"
```
{: codeblock}

```sh
# attach a label to another label (to the same commit)
$ cocoa inventory label add \
  --org='Github-ID' \
  --repo='compliance-inventory-repo' \
  --to-label="some-label" \
  "new-label-to-add"
```
{: codeblock}

```sh
# move or create the `staging_latest` label to another label (to the same commit)
$ cocoa inventory label move \
  --org='Github-ID' \
  --repo='compliance-inventory-repo' \
  --to-label="some-label" \
  "label-to-move"
```
{: codeblock}

### cocoa inventory promote
{: #inventory-promote}

Promotes entries from the inventory from one environment to another. The source environment can be either a branch or a tag.

Required Environment Variables:

```text
GHE_ORG=          # Github Organization (required if --org option is not specified).
GHE_REPO=         # Github Repository (required if --repo option is not specified).
PIPELINE_RUN_ID=  # Can be used instead of --pipeline-run-id (either the option or the variable is required).
```

Required Environment Variables, if you are using GitHub:

```text
GHE_TOKEN=    # Github Enterprise API Token (Optional if you are using --git-token-path)
```

Required Environment Variables, if you are using GitLab:

```text
GITLAB_TOKEN=        # Git Repos and Issue Tracking API Token (Required if you are using 'gitlab' as provider)
GITLAB_URL=          # Git Repos and Issue Tracking URL Example: https://<region>.git.cloud.ibm.com/api/v4 (Required if you are using 'gitlab' as provider)
```

Options for Git:

```sh
--git-token-path    #(Optional) Github or Gitlab Token's path
--git-api-url           #(Optional) Github or Gitlab API url
```

If you are using `gitlab`, use `--git-token-path` field to set your GitLab Token and `--git-api-url` field to set the GitLab API URL instead of `GITLAB_TOKEN` and `GITLAB_URL` environment variables. If you are using `github`, use `--git-token-path` field to set your GitHub Token and `--git-api-url` field to set the # GitHub Enterprise API URL instead of `GHE_TOKEN` and `GH_URL` environment variables.
If both of them are provided `--git-token-path` and `--git-api-url` take precedence.

Running the command:

```sh
$ cocoa inventory promote \
  --org='Github-ID' \
  --repo='compliance-inventory-repo' \
  --source='master' \
  --target='prod' \
  --priority='Critical' \
  --assigned-to='employee@ibm.com' \
  --description='desc' \
  --purpose='purpose' \
  --impact='impact' \
  --backout-plan='rollback'
```
{: codeblock}

### cocoa incident add
{: #incident-add}

Creates or updates an incident issue for a failing task in a pipeline run based on the commit-hash.

Options:

```text
--task              # (Required) The name of the failing task
--commit-hash       # (Required) The commit hash which triggered the pipeline run
--pipeline-run-url  # The pipeline run url where the task ran
--org               # The incident issue repo org
--repo              # The incident issue repo name
--assignee          # (Optional) Assignee(s) for the incident issue (github/gitlab username) e.g: --assigne=jane-doe --assignee=john-smith
--app-repo-url      # The app repo URL
--label             # Label(s) to add to the incident issue (optional) e.g: --label=foo --label=bar
--git-token-path    # (Optional) Github or Gitlab Token's path
--git-api-url       #(Optional) Github or Gitlab API url
```
{: screen}

Required Environment Variables:

```text
GHE_ORG=                    # Can be used instead of --org (either the option or the variable is required)
GHE_REPO=                   # Can be used instead of --repo (either the option or the variable is required)
PIPELINE_RUN_URL=           # Can be used instead of --pipeline-run-url (either the option or the variable is required)
APP_REPO_URL=               # Can be used instead of --app-repo-url (either the option or the variable is required)
```
{: screen}

Required Environment Variables, if you are using GitHub:

```text
GHE_TOKEN=    # Github Enterprise API Token (Optional if you are using --git-token-path)
```
{: screen}

Required Environment Variables, if you are using GitLab:

```text
GITLAB_TOKEN=        # Git Repos and Issue Tracking API Token (Required if you are using 'gitlab' as provider)
GITLAB_URL=          # Git Repos and Issue Tracking URL Example: https://<region>.git.cloud.ibm.com/api/v4 (Required if you are using 'gitlab' as provider)
```
{: screen}

If you are using `gitlab`, use `--git-token-path` field to set your GitLab Token and `--git-api-url` field to set the GitLab API URL instead of `GITLAB_TOKEN` and `GITLAB_URL` environment variables. If you are using `github`, use `--git-token-path` field to set your GitHub Token and `--git-api-url` field to set the # GitHub Enterprise API URL instead of `GHE_TOKEN` and `GH_URL` environment variables.
If both of them are provided, `--git-token-path` and `--git-api-url` take precedence.

Running the command:

```sh
 cocoa incident add --task=<failing-task-name> --commit-hash=<abc123>
```
{: codeblock}

### cocoa incident process
{: #incident-process}

This command processes provided scan results and creates incident issues in the provided repository per vulnerability (not per task run like `cocoa incident add` does). If issues exist already for subject-incident pairs, it does not create new ones.

If `--set-grace-period` is set, it creates issues with Grace Period set, or update existing issues, to have Grace Period.

Grace Period is set to 15 days if enabled. If Exempt or Grace Period is set on an incident issue, the check lists the issue, but does not mark it as an error.

Exceptions are either configured in CR VA, or you can submit the Incident or CVE for an exemption approval. If approved, you can edit the Incident issue (setting `Excempt: **true**`), and link to the approved request.

Currently supported scan result files:

- Container Registry VA scan (option `--type va`)
- CRA Vulnerability scan (option `--type cra`)
- Xray (option `--type xray`)
- OWASP ZAP (option `--type owasp-zap`)
- OWASP ZAP UI (option `--type owasp-zap-ui`)
- Twistlock (option `--type twistlock`)

For more information on Container Registry VA and result format, see [Vulnerability Advisor for IBM Cloud Container Registry](https://cloud.ibm.com/apidocs/container-registry/va){: external}.
For more information on the CRA Vulnerability scanner, see the [Configuring Code Risk Analyzer](https://cloud.ibm.com/docs/ContinuousDelivery?topic=ContinuousDelivery-cd-configure-cra-repos){: external}documentation.

Usage:

```sh
$ cocoa incident process \
  <options> \
  <path to result file>
```
{: codeblock}

Options:

```text
--type             # (Required) Scan type [cra | va]
--subject          # (Required) Subject of scans (repo, or image name)
--drilldown-url    # (Required) URL to the point where the incident was found (can be a pipelien run, a commit hash or an image URL with digest)
--set-grace-period # Should the created incidents have Grace period set
--git-provider     # Git service provider [github | gitlab] Default is "github"
--org              # The incident issue repository org
--repo             # The incident issue repository name
--label            # Label(s) to add to the incident issue (optional) e.g: --label=foo --label=bar
--assignee         #(Optional) Assignee(s) for the incident issue (github/gitlab username) e.g: --assigne=jane-doe --assignee=john-smith
--git-token-path   #(Optional) Github or Gitlab Token's path
--git-api-url      #(Optional) Github or Gitlab API url
--custom-exempt-label # (Optional) Defines the custom label with which the incident issue has been marked as exempted
```
{: screen}

Required Environment Variables:

```text
INCIDENT_REPO_ORG=        # Can be used instead of --org (either the option or the variable is required)
INCIDENT_REPO_NAME=       # Can be used instead of --repo (either the option or the variable is required)
```
{: screen}

Required Environment Variables, if you are using GitHub:

```text
GHE_TOKEN=                # Github Enterprise API Token (Optional if you are using --git-token-path)
```
{: screen}

Required Environment Variables, if you are using GitLab:

```text
GITLAB_TOKEN=        # Git Repos and Issue Tracking API Token (Required if you are using 'gitlab' as provider)
GITLAB_URL=          # Git Repos and Issue Tracking URL Example: https://<region>.git.cloud.ibm.com/api/v4 (Required if you are using 'gitlab' as provider)
```
{: screen}

If you are using `gitlab`, use `--git-token-path` field to set your GitLab Token and `--git-api-url` field to set the GitLab API URL instead of `GITLAB_TOKEN` and `GITLAB_URL` environment variables. If you are using `github`, use `--git-token-path` field to set your GitHub Token and `--git-api-url` field to set the # GitHub Enterprise API URL instead of `GHE_TOKEN` and `GH_URL` environment variables.
If both of them are provided, `--git-token-path` and `--git-api-url` take precedence.

Return values:

- The command lists incident issue URLs found or created according to the result file and subject.
- If no issues are found, or all found issues have either Exempt or Grace period set, the command exits with zero status.
- If any of the issues that are found have no Exempt or Grace Period set, the command exits with a nonzero status.

Running the command:

```sh
$ cocoa incident process \
  --type va \
  --subject us.icr.io/service-image \
  --drilldown-url us.icr.io/service-image@sha256:digest \
  path/to/scan-result.json
```
{: codeblock}

### cocoa locker commands
{: #locker-commands}

The evidence locker is a Git repository, either on GitHub (Enterprise) or GitLab.
The Git provider can be specified by using `--git-provider` (it can be either `github` or `gitlab`, by default it is `github`).
For both providers, authentication is required, for GitHub set the `GHE_TOKEN` environment variable or `--git-token-path` field, for GitLab, set `GITLAB_TOKEN` or `--git-token-path` field.
For GitLab, you must also specify where the GitLab instance is located, by using the `GITLAB_URL` environment variable or `--git-api-url` field. You can do the same for GitHub Enterprise instances, by using the `GH_URL` environment variable or `--git-api-url` field, but it defaults to `https://github.ibm.com`.

The name of the evidence locker repository can be set by using `--org` and `--repo`, or by using the `EVIDENCE_LOCKER_REPO_OWNER` and `EVIDENCE_LOCKER_REPO_NAME` environment variables.

By default, the CLI on each invocation ensures that you work with an up-to-date version of a Git locker. This behavior can be disabled by setting `COCOA_USE_CACHE` to any value except `0`, `false`, `no`, or `n`. In this case, the CLI uses its internal cache to look up evidence (much faster), but results might be stale.

Additionally, you can upload evidence and attachments to [Cloud Object Storage](https://www.ibm.com/cloud/object-storage){: external}as well for archiving purposes. It is done automatically when the following environment variables are present:

- `COS_ENDPOINT`: The [endpoint](https://cloud.ibm.com/docs/cloud-object-storage?topic=cloud-object-storage-endpoints){: external}where the Cloud Object Storage bucket can be accessed.
- `COS_BUCKET_NAME`: The name of the Cloud Object Storage bucket.
- `COS_API_KEY`: An [IBM Cloud API key](https://cloud.ibm.com/docs/account?topic=account-userapikey#userapikey){: external}or [Service ID API key](https://cloud.ibm.com/docs/account?topic=account-serviceidapikeys&interface=ui){: external}that has write access to the Cloud Object Storage bucket.
- `IBM_AUTH_ENDPOINT`: The endpoint where the API key can be used to generate an [IAM token](https://cloud.ibm.com/docs/account?topic=account-iamtoken_from_apikey&interface=api){: external}(defaults to `https://iam.cloud.ibm.com/identity/token`){: external}.

### cocoa locker asset add < URI >
{: #locker-asset-add}

Adds an asset to the evidence locker. If the asset exists, the asset is printed.

Options:

```text
--org              # (Required) Owner of the evidence locker repo, defaults to `EVIDENCE_LOCKER_REPO_OWNER`
--repo             # (Required) Name of the evidence locker repo, defaults to `EVIDENCE_LOCKER_REPO_NAME`
--type             # Type of the asset ("commit", "image", "generic")
--version          # Show version number
--format           # Output format ("id", "json", default: "id")
--git-provider     # Git service provider ("github", "gitlab", default: "github")
--git-token-path   # Git token path to read the secret from 
--git-api-url      # Github or Gitlab API url
--related          # The ID of a related asset (default: [])
--date             # Asset creation date
--details          # Additional asset details, as key=value pairs (default: [])
--origin           # Additional details about the origin of the asset, as key=value pairs (default: [])
--dry-run          # Shows what would be added to the locker (default: false)
```
{: screen}

Use the `--dry-run` flag if you don't want to add anything to the locker, and you want to check what would be added to the locker.

The type of the asset can be specified with the `--type` flag, currently `commit`, `image`, and `generic` is supported.

The `uri` parameter can be any valid [URI](https://datatracker.ietf.org/doc/html/rfc3986){: external}, but a few formats are treated specially:

- In case `uri` scheme is `docker` (that is, it begins with `docker://`), the command assumes the `--type=image` flag
- In case `uri` scheme is `git`, or the path ends with a `.git` suffix, the command assumes the `--type=commit` flag

When `type` is not `generic` (that is, `image` or `commit`), the URI must match on the following patterns:

- For `commit`, the URI _must_ have a fragment, which _must_ be a 40-character hexadecimal commit sha (for example `https://github.ibm.com/foo/bar.git#aaaaaaaabbbbbbbbccccccccddddddddeeeeeeee`)
- For `image`, the URI _must_ contain the image digest (for example `docker://us.icr.io/foo/bar:v1.2.3@sha256:0000000011111111222222223333333344444444555555556666666677777777`)

When `type` is `generic`, the `--type` flag must be explicitly passed.

More details can be added to the asset by using the `--details` flag, which accepts `key=value` pairs.

More details about the origin of the asset can be provided by using the `--origin` flag, which accepts `key=value` pairs.

Some environment variables are automatically picked up to add details about the asset origin:

- Setting `TOOLCHAIN_CRN` is the same as specifying `--origin toolchain_crn=$TOOLCHAIN_CRN`
- Setting `PIPELINE_ID` is the same as specifying `--origin pipeline_id=$PIPELINE_ID`
- Setting `PIPELINE_RUN_ID` is the same as specifying `--origin pipeline_run_id=$PIPELINE_RUN_ID`

The `--date` flag can be used to override the asset creation date (defaults to the current time), it accepts any string that [`Date.parse`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Date/parse) accepts.

Related assets can be added by using the `--related` flag (for multiple related assets, the flag can be specified multiple times). Assets can be specified with their internal ID, or by their URI (if they're not of type `generic`).

By default, the `id` format (the `--format` flag) prints the internal ID of the created asset. With the `json` format, the whole asset is going to be printed, as a JSON object.

Running the command to add a Git commit:

```sh
$ cocoa locker asset add 'https://github.ibm.com/foo/bar.git#aaaaaaaabbbbbbbbccccccccddddddddeeeeeeee' \
                         --details tag=v1.2.3
```
{: codeblock}

Example output:

```sh
b5bbbbb55555b5555b555b55b5555bb5b555b5b555bb5b55bbb5555fbbbbb
```
{: codeblock}

Running the command to add a Docker image:

```sh
$ cocoa locker asset add docker://us.icr.io/foo/bar:v1.2.3@sha256:0000000011111111222222223333333344444444555555556666666677777777 \
                         --origin "job_name=my job" \
                         --related https://github.ibm.com/foo/bar.git#aaaaaaaabbbbbbbbccccccccddddddddeeeeeeee \
                         --format json
```
{: codeblock}

Example output:

```sh
{
  "version": "1",
  "id": "0000000011111111222222223333333344444444555555556666666677777777",
  "uri": "docker://us.icr.io/foo/bar:v1.2.3@sha256:0000000011111111222222223333333344444444555555556666666677777777",
  "origin": {
    "job_name": "my job",
    "toolchain_crn": "crn:v1:bluemix:public:toolchain:au-syd:a/111111111111c2f2222222222b22a7a63:ac2a22a2-2a2a-2222-aaa2-222aa22a2a2a::",
    "pipeline_run_id": "f333b3bc-3333-3fea-3333-333d3a3b33b3",
    "pipeline_id": "444aaa4a-b4c4-4444-4f4b-aa4444a444a4"
  },
  "details": {
    "registry": "us.icr.io",
    "name": "foo/bar",
    "tag": "v1.2.3",
    "digest": "0000000011111111222222223333333344444444555555556666666677777777"
  },
  "date": "2021-07-15T14:26:06.301Z",
  "type": "image",
  "related": [
    "b5bbbbb55555b5555b555b55b5555bb5b555b5b555bb5b55bbb5555fbbbbb"
  ]
}
```
{: codeblock}

Running the command to add a release:

```sh
$ cocoa locker asset add release:my-app@v1.2.3 \
                         --type generic \
                         --date 2021-06-01T12:00:00 \
                         --related docker://us.icr.io/foo/bar:v1.2.3@sha256:0000000011111111222222223333333344444444555555556666666677777777 \
                         --related docker://us.icr.io/foo/baz:v1.2.3@sha256:0000000011111111222222223333333344444444555555556666666677777777
```
{: codeblock}

Example output:

```sh
0000000011111111222222223333333344444444555555556666666677777777%
```
{: codeblock}

### cocoa locker asset get < URI >
{: #locker-asset-get}

Retrieves an asset from the evidence locker.

Options:

```text
--org              # (Required) Owner of the evidence locker repo, defaults to `EVIDENCE_LOCKER_REPO_OWNER`
--repo             # (Required) Name of the evidence locker repo, defaults to `EVIDENCE_LOCKER_REPO_NAME`
--type             # Type of the asset ("commit", "image", "generic")
--version          # Show version number
--git-provider     # Git service provider ("github", "gitlab", default: "github")
--git-token-path   # Git token path to read the secret from 
--git-api-url      # Github or Gitlab API url
--format           # Output format ("id", "json", default: "id")
```
{: screen}

Run the command:

```sh
 cocoa locker asset get https://github.ibm.com/foo/bar.git#aaaaaaaabbbbbbbbccccccccddddddddeeeeeeee --format json
```
{: codeblock}

Example output:

```sh
{
  "version": "1",
  "id": "0000000011111111222222223333333344444444555555556666666677777777",
  "uri": "docker://us.icr.io/foo/bar:v1.2.3@sha256:0000000011111111222222223333333344444444555555556666666677777777",
  "origin": {
    "toolchain_crn": "crn:v1:bluemix:public:toolchain:au-syd:a/111111111111c2f2222222222b22a7a63:ac2a22a2-2a2a-2222-aaa2-222aa22a2a2a::",
    "pipeline_run_id": "f333b3bc-3333-3fea-3333-333d3a3b33b3",
    "pipeline_id": "444aaa4a-b4c4-4444-4f4b-aa4444a444a4"
  },
  "details": {
    "sha": "aaaaaaaabbbbbbbbccccccccddddddddeeeeeeee",
    "repository": "https://github.ibm.com/foo/bar.git",
    "tag": "v1.2.3"
  },
  "date": "2021-07-15T14:26:06.301Z",
  "type": "image",
  "related": []
}
```
{: codeblock}

### cocoa locker asset list < URI >
{: #locker-asset-list}

Lists the related assets from the evidence locker.

Options:

```text
--org              # (Required) Owner of the evidence locker repo, defaults to `EVIDENCE_LOCKER_REPO_OWNER`
--repo             # (Required) Name of the evidence locker repo, defaults to `EVIDENCE_LOCKER_REPO_NAME`
--type             # Type of the asset ("commit", "image", "generic")
--version          # Show version number
--git-provider     # Git service provider ("github", "gitlab", default: "github")
--git-token-path   # Git token path to read the secret from 
--git-api-url      # Github or Gitlab API url
--format           # Output format ("id", "json", default: "id")
```
{: screen}

Run the command:

```sh
 cocoa locker asset list release:my-app@v1.2.3 --format id --type generic
```
{: codeblock}

Example output:

```sh
1122211111122222111111111111111111111111111111666661111111111111
2222222222555552222222222222222333333222222222222222222222222222
3333333337777773333333333333333333333333333333344444433333333333
```
{: codeblock}

Running the command by using the `json` format combined with [`jq`](https://stedolan.github.io/jq/){: external}:

```sh

 cocoa locker asset list release:my-app@v1.2.3 --format json --type generic | jq -r '.[].uri'
```
{: codeblock}

Example output:

```sh
release:my-app@v1.2.3
docker://us.icr.io/foo/bar:v1.2.3@sha256:0000000011111111222222223333333344444444555555556666666677777777
https://github.ibm.com/foo/bar.git#aaaaaaaabbbbbbbbccccccccddddddddeeeeeeee
```
{: codeblock}

### cocoa locker evidence add
{: #locker-evidence-add}

Adds evidence to the evidence locker.

The evidence locker can be specified by using flags. For more information, see [cocoa locker commands](#cocoa-locker-commands).

The evidence can be configured by using the following flags:

- `--evidence-type-id`: a string that identifies the type of the evidence (for example `com.ibm.unit_test`)
- `--evidence-type-version`: a string that identifies the evidence details schema (for example `1.0.0`, `v2`, and so on)

Some optional flags can be specified as well:

- `--date`: evidence creation date (defaults to the current timestamp), accepts a string that can be parsed with [`Date.parse`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Date/parse).
- `--details`: evidence details can be specified as `key=value` pairs, the flag can be specified multiple times (for example `--details result=success --details foo=bar`, and so on).
- `--origin`: details about the origin of the evidence can be specified by using `key=value` pairs, for example `--origin job_id=123`, and so on.
- `--asset`: specifies which assets (by their URI or internal ID) are relevant for the evidence, see `cocoa locker asset` commands. The flag can be specified multiple times. The asset must be already present in the locker.
- `--attachment`: specifies file attachments to the evidence. Each attachment must point to an existing file, and each attachment name must be unique (that is, `--attachment a/foo.json --attachment b/foo.json` is not permitted).
- `--issue`: list of issue URLs, which can be used to track activities that are related to the findings. The flag can be passed multiple times.
- `--scope`: a unique identifier that can be used to correlate evidence from multiple sources.
- `--comment-on-issues`: add a comment with details about the created evidence to the incident issues provided by using `--issue` flag
   - Since the issues can be either on `gitlab` or `github`, the provider is parsed from the issue URLs
   - Every issue must be from the same repository
   - The issue urls must match one of the following patterns:
     - `https://<host>/<owner>/<name>/issues/<issue number>` - for `github`
     - `https://<host>/<owner>/<name>/-/issues/<issue number>` - for `gitlab`
   - Required environment variables to access the issues repository:
     - if the urls point to `github` issues:
       - `GH_URL`: optional, defaults to `https://github.ibm.com/api/v3`
       - `GHE_TOKEN`
     - if the urls point to `gitlab` issues:
       - `GITLAB_URL`: required, Base URL of the Gitlab API
       - `GITLAB_TOKEN`

Some environment variables are automatically picked up to add details about the asset origin:

- Setting `TOOLCHAIN_CRN` is the same as specifying `--origin toolchain_crn=$TOOLCHAIN_CRN`
- Setting `PIPELINE_ID` is the same as specifying `--origin pipeline_id=$PIPELINE_ID`
- Setting `PIPELINE_RUN_ID` is the same as specifying `--origin pipeline_run_id=$PIPELINE_RUN_ID`

The behavior of the command can be altered by using these flags:

- `--dry-run`: Adds nothing to the locker, but shows what would be added.
- `--format`: controls the output format, `id` or `json` (`id` by default).

Run the command to save results for [`detect-secrets`](https://github.com/IBM/detect-secrets){: external}:

```sh
$ cocoa locker evidence add --evidence-type-id com.ibm.detect_secrets \
                            --evidnece-type-version 1.0.0 \
                            --details result=success \
                            --asset https://github.ibm.com/foo/bar.git#aaaaaaaabbbbbbbbccccccccddddddddeeeeeeee
```
{: codeblock}

Run the command to save unit test results:

```sh
$ cocoa locker evidence add --evidence-type-id com.ibm.unit_test \
                            --evidence-type-version 1.0.0 \
                            --details failure \
                            --asset https://github.ibm.com/foo/bar.git#aaaaaaaabbbbbbbbccccccccddddddddeeeeeeee
                            --attachment path/to/results/junit.xml \
                            --issue https://github.ibm.com/foo/bar/issues/123
```
{: codeblock}

### cocoa locker evidence get < evidence-id >
{: #locker-evidence-get}

Retrieves a piece of evidence from the locker.

Options:

```text
--org              # (Required) Owner of the evidence locker repo, defaults to `EVIDENCE_LOCKER_REPO_OWNER`
--repo             # (Required) Name of the evidence locker repo, defaults to `EVIDENCE_LOCKER_REPO_NAME`
--git-provider     # Git service provider ("github", "gitlab", default: "github")
--version          # Show version number
--git-token-path   # Git token path to read the secret from 
--git-api-url      # Github or Gitlab API url
--format           # Output format ("json", default: "json")
```
{: screen}

Run the command:

```sh
 cocoa locker evidence get 0000000011111111222222223333333344444444555555556666666677777777
```
{: codeblock}

Example output:

```sh
{
  "id": "0000000011111111222222223333333344444444555555556666666677777777",
  "evidence_type_id": "image_signing",
  "evidence_type_version": "1.0.0",
  "date": "2021-09-08T10:10:43.955Z",
  "origin": {
    "toolchain_crn": "crn:v1:bluemix:public:toolchain:us-south:a/111111111111c2f2222222222b22a7a63:ac2a22a2-2a2a-2222-aaa2-222aa22a2a2a::",
    "pipeline_run_id": "f333b3bc-3333-3fea-3333-333d3a3b33b3",
    "pipeline_id": "444aaa4a-b4c4-4444-4f4b-aa4444a444a4"
  },
  "details": {
    "result": "success"
  },
  "issues": [],
  "attachments": {
    "app-image_signature": "0000000011111111222222223333333344444444555555556666666677777777"
  },
  "assets": [
    "docker://us.icr.io/foo/bar:v1.2.3@sha256:0000000011111111222222223333333344444444555555556666666677777777"
  ]
}
```
{: codeblock}

### cocoa locker evidence summary < asset-id >
{: #locker-evidence-summary}

Returns the evidence summary for one or more assets. When no assets are specified on the command line, assets are read from `stdin`.

Optional flags:
- `--latest-only`: Discards evidence if newer evidence is available for an asset (`true` by default, set to `false` to disable this behavior)
- `--scope`: Considers evidence that has the specified scope only (see `evidence add --scope`), can be specified multiple times
- `--check-immutable-storage`: Checks if every evidence is also present in a Cloud Object Storage bucket and is protected by a retention period of at least 365 days. Appends `com.ibm.immutable_storage` evidence to the summary.
   - See [`cocoa locker`](#cocoa-locker) section on how to configure the Cloud Object Storage bucket.
- `--dry-run`: Has an effect when combined with `--check-immutable-storage`. If used, `com.ibm.immutable_storage` evidence is only appended to the summary but it does not get uploaded to the evidence locker.

Run the command:

```sh
$ cocoa locker evidence summary \
    docker://us.icr.io/foo/bar@sha256:1234567812345678123456781234567812345678123456781234567812345678 \
    docker://us.icr.io/baz/quux@sha256:1234567812345678123456781234567812345678123456781234567812345678 \
    --scope 11a1aa11-1a11-11a1-aa11-a11a1a1111a1 \
    --scope 22a2aa22-2a22-22a2-aa22-a22a2a2222a2
```
{: codeblock}

Example read from stdin:

```sh
$ cat <<EOF | cocoa locker evidence summary
docker://us.icr.io/foo/bar@sha256:1234567812345678123456781234567812345678123456781234567812345678
docker://us.icr.io/baz/quux@sha256:1234567812345678123456781234567812345678123456781234567812345678
EOF
```
{: codeblock}

### cocoa locker attachment get < attachment-id >
{: #locker-attachment-get}

Retrieves an attachment that was previously uploaded with `cocoa locker evidence add`.

Options:

```text
--org              # (Required) Owner of the evidence locker repo, defaults to `EVIDENCE_LOCKER_REPO_OWNER`
--repo             # (Required) Name of the evidence locker repo, defaults to `EVIDENCE_LOCKER_REPO_NAME`
--git-provider     # Git service provider ("github", "gitlab", default: "github")
--version          # Show version number
--git-token-path   # Git token path to read the secret from 
--git-api-url      # Github or Gitlab API url
```
{: screen}

Run the command to upload attachment:

```sh
$ cocoa locker evidence add --evidence-type-id com.ibm.unit_test \
                            --evidence-type-version 1.0 \
                            --details result=success \
                            --attachment path/to/junit.xml
```
{: codeblock}

Example output of attachment upload:

```sh
{
  ...
  "attachments": {
    "junit.xml": "5aa5555aa55aa55a555aa5a5aa555555aaaa5aa5aa5555a55a5aa5aa5a5aaaaa",
  },
  ...
}
```
{: codeblock}

Run the command to retrieve the attachment:

```sh
 cocoa locker attachment get 5aa5555aa55aa55a555aa5a5aa555555aaaa5aa5aa5555a55a5aa5aa5a5aaaaa
```
{: codeblock}

### cocoa locker check
{: #locker-check}

Checks the required configuration and settings of the locker.

Currently, only the Cloud Object Storage based locker has a configuration requirement:
- Because every evidence must be kept for at least one year, the Cloud Object Storage bucket must be protected by a retention policy. Default retention period must be greater or equal to 365 days.

For more information, see [cocoa locker commands](#cocoa-locker-commands).

Required Environment Variables:

```text
COS_API_KEY=        # Cloud Object Storage API Key
COS_BUCKET_NAME=    # Bucket Name where the evidence will be uploaded in the COS Instance
COS_ENDPOINT=       # The COS API Endpoint matching the region where the bucket is located
```
{: screen}

Run the command:

```sh
 cocoa locker check --provider='cos'
```
{: codeblock}

### cocoa tekton get-pipeline-logs
{: #tekton-get-pipeline-logs}

Creates log files for every step from a pipeline run and saves it to a directory. Use either the environment variables (`PIPELINE_ID`, `PIPELINE_RUN_ID`) or override the environment variables with explicit flags (`--pipeline-id`, `--pipeline-run-id`).

Options:

```text
-o --output-dir       # (Required) Specifies the directory where the logs will be saved
   --pipeline-id      # ID of the pipeline
   --pipeline-run-id  # ID of the pipeline run
-u --use-task-ref     # (Optional) Can be used to override the default name of the pipeline task to the taskRef, when creating the directory
```
{: screen}

Required Environment Variables:

```text
IBMCLOUD_API_KEY= # IBM Cloud API Token
TOOLCHAIN_REGION= # Region of the toolchain
PIPELINE_ID=      # Can be used instead of '--pipeline-id option
PIPELINE_RUN_ID=  # Can be used instead of '--pipeline-run-id option
```
{: screen}

Running the command:

```sh
$ cocoa tekton get-pipeline-logs \
  --output-dir=<output-dir> \
  --pipeline-id=<pipeline-id> \
  --pipeline-run-id=<pipeline-run-id> \
  --use-task-ref
```
{: codeblock}

### cocoa changelog
{: #changelog}

This command is deprecated and will be removed in 2.0.0.
{: important}

cocoa changelog is not supported in {{site.data.keyword.gitrepos}}.
{: important}

Prints the changelog between two revisions in the repo to stdout.

Options:

```text
--org   # Defaults to GHE_ORG env variable, github organization
--repo  # Defaults to GHE_REPO env variable, name of the github repository
```
{: screen}

Required Environment Variables:

```text
GHE_TOKEN=  # Github Enterprise API Token (Required)
GHE_ORG=    # The owner of the repository (optional, when --org is not provided)
GHE_REPO=   # The repository name (optional, when --repo is not provided)
```
{: screen}

This command contains three usage methods:

* Check commits in range when both `--from` and `--to` options are provided.

Options:

```text
--from  # git commit hash to calculate the changes from
--to    # git commit hash to calculate the changes to
```
{: screen}

Running the command:

```sh
$ cocoa changelog \
  --org=<github-organization> \
  --repo=<github-repo-name> \
  --from=<commit-hash-to-calculcate-changes-from> \
  --to=<commit-hash-to-calculate-changes-to>
```
{: codeblock}

* Contain all commits that belong to a specific pull request when the `--pr` option is provided.

Options:

```text
--pr # pull request number from which the changelog is calculated
```
{: screen}

Running the command:

```sh
$ cocoa changelog \
  --org=<github-organization> \
  --repo=<github-repo-name> \
  --pr=<pull-request-number>
```
{: codeblock}

* If the preceding information is not provided, the changelog contains the changes of the currently merged pull request.

Running the command:

```sh
$ cocoa changelog \
  --org=<github-organization> \
  --repo=<github-repo-name>
```
{: codeblock}
