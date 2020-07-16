+++
cloud_full = "Azure"
cloud = "Azure"
cloud_subtype = "Azure Resource Manager (ARM)"
blueprint_name = "azure-arm.yaml"
deployment_name = "virtual-machine.azure-arm"

title = "Azure (ARM) - Infrastructure provisioning basics"
description = "Azure (ARM) - Infrastructure provisioning basics"
weight = 25
alwaysopen = false
+++

{{%children style="h2" description="true"%}}

This example demonstrates a simple infrastructure setup in **{{< param cloud_full >}}** using **{{< param cloud_subtype >}}**, the deployment consists of:

 * Instance
 * Security Group
 * Network
 * All of the essential peripherals in {{< param cloud >}} (IP address, NIC, etc...).

In this example we will deploy only the infrastructure.
Later, in the more advanced examples (multi cloud examples)
we will leverage this setup as the basis for deploying a generic application server and an application.

## Prerequisites
This example expects the following prerequisites:

* A cloudify manager setup ready. This can be either a [{{< param mgr_hosted_title >}}]({{< param mgr_hosted_link >}}), a [{{< param mgr_premium_title >}}]({{< param mgr_premium_link >}}), or a [{{< param mgr_community_title >}}]({{< param mgr_community_link >}}).
* Access to {{< param cloud >}} infrastructure is required to demonstrate this example.

#### CLI or Management Console?

Cloudify allows for multiple user interfaces. Some users find the {{< param cfy_console_name >}} (web based UI) more intuitive while others prefer the {{< param cfy_cli_name >}} (Command Line Interface). This tutorial and all following ones will describe both methods.

* [Using the {{< param cfy_console_name >}}](#cloudify-management-console)
* [Using the {{< param cfy_cli_name >}}](#cloudify-cli)

## Cloudify Management Console

This section explains how to run the above described steps using the {{< param cfy_console_name >}}.
The {{< param cfy_console_name >}} and {{< param cfy_cli_name >}} can be used interchangeably for all Cloudify activities.



### Create Secrets

To connect to {{< param cloud >}}, credentials are required.
Cloudify recommends storing such sensitive information in a Cloudify secret.
Secrets are kept encrypted in a secure way and used in run-time by the system.
Learn more about Cloudify secrets [here]({{< relref "/working_with/manager/using-secrets.md" >}}).

{{< param cloud >}} credentials can be created by following the guide [here]({{< relref "working_with/official_plugins/Infrastructure/azure.md" >}}).

To store the access keys as secrets in the Cloudify manager, login to the {{< param cfy_console_name >}} and select the **System Resources** page. Scroll to the **Secret Store Management** widget and use the **Create** button to add the following new secrets:

* azure_subscription_id
* azure_tenant_id
* azure_client_id
* azure_client_secret

**Notes**

* `subscription_id` - the account subscription ID.
* `tenant_id` - the Service Principal `tenant`.
* `client_id` - the Service Principal `appId`.
* `client_secret` - the Service Principal `password`.

![Required plugins for this example]( /images/trial_getting_started/aws_basic/Screenshot249.png )

### Upload Plugins

Plugins are Cloudify's extendable interfaces to services, cloud providers and automation tools.
I.e., connecting to {{< param cloud >}} requires the {{< param cloud >}} plugin.

To upload the required plugins to your manager, select the **Cloudify Catalog** page, scroll to the **Plugins Catalog** widget and select the plugins you wish to upload.

For this example, upload the following plugins:

* Utilities
* {{< param cloud >}}




### Upload Blueprint

A Cloudify blueprint is a general purpose model for describing systems, services or any orchestrated object topology.
Blueprints are represented as descriptive code (yaml based files) and typically stored and managed as part of the source repository.
The {{< param cloud >}} infrastructure blueprint is available [here]({{< param basic_blueprint_master >}}/{{< param blueprint_name >}}).

The flow required to setup a service consists of:

1. Upload the blueprint describing the service to the Cloudify Manager.
1. Create a deployment from the uploaded blueprint. This generates a model of the service topology in the Cloudify database and provides the "context" needed for running workflows.
1. Run the **install** workflow for the created deployment to apply the model to the infrastructure.

Let's run these one by one.

To upload a blueprint to the Cloudify manager, select the **Cloudify Catalog** page, and use the **Upload blueprint** button next to the {{< param cloud >}}-VM-Setup-using-ARM blueprint.

### Deploy

Once the blueprint is uploaded, it will be displayed in the Blueprints widget. to deploy the blueprint click the **Create deployment** button next to the blueprint you wish to deploy. Specify a deployment name, update any inputs (such as the {{< param cloud >}} region), and click **Deploy**

![Create a Cloudify Deployment]( /images/trial_getting_started/aws_basic/Screenshot259.png )

Switch to the **Deployments** page. The deployment you have created should be displayed in the deployments list.

To apply the deployment and push it to the infrastructure run the **Install** workflow by clicking the **Execute workflow** menu next to the deployment, expanding **Default workflows**, and selecting **Install**.

![Run a Cloudify Workflow]( /images/trial_getting_started/aws_basic/Screenshot260.png )

You can track the progress of the installation workflow by checking the node instances progress, or get a detailed view by clicking the deployment, and in the drill down page scroll down to the **Deployment Executions** widget and expand the **Install** workflow.

![Track the progress of a Cloudify Workflow]( /images/trial_getting_started/aws_basic/Screenshot261.png )

### Validate

In this example we have setup a simple infrastructure. A virtual instance (VM) was created in the region specified in the Deployment inputs alongside a new network and various other resources.

* Go to your {{< param cloud >}} console and see the new instance and other resources that were created.
* Examine the Deployment page in the {{< param cfy_console_name >}} for more information about your deployed nodes, topology, and view the installation logs.

To login to your new {{< param cloud >}} instance, you can look at the **Deployment Outputs/Capabilities** widget on the Deployment screen to find your {{< param cloud >}} instance public IP, SSH username, and SSH private key.

![Get Cloudify Deployment outputs]( /images/trial_getting_started/aws_basic/Screenshot263.png )

### Teardown

To remove the deployment and destroy the orchestrated infrastructure resources, run the **Uninstall** workflow by clicking the **Execute workflow** menu next to the deployment, expanding **Default workflows**, and selecting **Uninstall**.

____


## Cloudify CLI

Create a CLI profile instructing your CLI how to connect with the Cloudify manager by running the following CLI commands

```bash
cfy init
cfy profiles use <your manager hostname / URL / IP> -u admin -p <the admin  password> --ssl
cfy profiles set --manager-tenant default_tenant
```

### Create Secrets

To enable Cloudify to connect to {{< param cloud >}}, credentials are required.
Cloudify recommends storing such sensitive information as a Cloudify secret.
Secrets are encrypted in a secure way and used during run-time by the system.
Learn more about Cloudify secrets [here]({{< relref "/working_with/manager/using-secrets.md" >}}).

{{< param cloud >}} credentials can be created by following the guide [here]({{< relref "working_with/official_plugins/Infrastructure/azure.md" >}}).

To store the access keys as secrets via the {{< param cfy_cli_name >}}, run the following (replacing <value> with the actual string retrieved from {{< param cloud >}}):

```bash   
cfy secrets create azure_client_id --secret-string <client_id>
cfy secrets create azure_tenant_id --secret-string <tenant_id>
cfy secrets create azure_subscription_id --secret-string <subscription_id>
cfy secrets create azure_client_secret --secret-string <client_secret>
```

**Notes**

* `subscription_id` - the account subscription ID.
* `tenant_id` - the Service Principal `tenant`.
* `client_id` - the Service Principal `appId`.
* `client_secret` - the Service Principal `password`.


### Upload Plugins

Plugins are Cloudify's extendable interfaces to services, cloud providers, and automation tools.
Connecting to {{< param cloud >}} requires the {{< param cloud >}} plugin. You may upload specific plugins or, for simplicity, upload the plugin bundle containing all of the basic, pre-packaged, plugins.

To upload the default plugins bundle (this may take a few minutes depending on your internet speed):
```bash
cfy plugins bundle-upload
```

**Tip**: Read more about Cloudify [plugins]({{< relref "/working_with/official_plugins/_index.md" >}}) and [writing your own plugins]({{< relref "/developer/writing_plugins/_index.md" >}}).

### Upload Blueprint and Deploy

A Cloudify blueprint is a general purpose model for describing systems, services or any orchestrated object topology. Blueprints are represented as descriptive code (YAML-based files) and are typically stored and managed as part of the source code repository.

The {{< param cloud >}} infrastructure blueprint is available [here]({{< param basic_blueprint_master >}}/{{< param blueprint_name >}}). You can check the `environment.json` file ({{< param cloud >}} template file) of this example [here](https://github.com/cloudify-community/blueprint-examples/tree/master/virtual-machine/resources/arm).

Uploading a blueprint to Cloudify can be done by direct upload or by providing the link in the source code repository.
The flow to do that is:

 1. Upload the blueprint.
 1. Create a deployment from the uploaded blueprint. This generates a model of the service topology in the Cloudify database and provides the "context" needed for running workflows.
 1. Run the **install** workflow for the created deployment to apply the model to the infrastructure.

In order to perform this flow as a single unit, we will use the **install** command.

```bash
cfy install {{< param basic_blueprint_zip >}} -n {{< param blueprint_name >}}
```

**Tip**: If the above flow returns an error on this stage (for example, the wrong credentials were provided) and the deployment was already created, you should stop the installation and remove the deployment before you run the command again. To do that, run:
```
cfy executions start stop -d {{< param deployment_name >}} -p ignore_failure=true
cfy executions start uninstall -d {{< param deployment_name >}} -p ignore_failure=true
cfy uninstall {{< param deployment_name >}}
```
Fix the mistake and try again. If you run the uninstall commands above and get this error message:
```
An error occurred on the server: 404: Requested `Deployment` with ID `{{< param deployment_name >}}` was not found
```
Just delete the "{{< param deployment_name >}}" blueprint and try the install command again (read about [blueprints]({{< relref "cli/orch_cli/blueprints.md" >}}) and [deployments]({{< relref "cli/orch_cli/deployments.md" >}}) commands).

### Validate

In this example we have setup a simple infrastructure. A virtual instance (VM) was created in the region specified in the Deployment inputs alongside a new network and various other resources.

* Go to your {{< param cloud >}} console and see the new instance and other resources that were created.
* You can easily get a list of all deployed nodes by running:

```bash
Listing nodes for deployment {{< param deployment_name >}}...

Nodes:
+------------+---------------------------+---------------------------+---------+---------------------------+------------+----------------+---------------------+-----------------------------+------------+
|     id     |       deployment_id       |        blueprint_id       | host_id |            type           | visibility |  tenant_name   | number_of_instances | planned_number_of_instances | created_by |
+------------+---------------------------+---------------------------+---------+---------------------------+------------+----------------+---------------------+-----------------------------+------------+
| deployment | virtual-machine.azure-arm | virtual-machine.azure-arm |         | cloudify.azure.Deployment |   tenant   | default_tenant |          1          |              1              |   admin    |
+------------+---------------------------+---------------------------+---------+---------------------------+------------+----------------+---------------------+-----------------------------+------------+

Showing 1 of 1 nodes

```

**Tip**: To check out some more commands to use with the {{< param cfy_console_name >}}, run `cfy --help`

An even easier way to review your deployment is through the [{{< param cfy_console_name >}}](#validate).
Login to the console and browse to the **Deployments** page.
Select the deployment (`{{< param deployment_name >}}`) and explore the topology, inputs, outputs, nodes, and logs.

![azure_arm_simple_vm_topology.png]( /images/trial_getting_started/azure_arm_simple_vm_topology.png )

This is also a good time to examine the Cloudify blueprint used in the example.
The blueprint can be examined in the {{< param cfy_console_name >}}, however in this case
we will go to the Cloudify examples repository in Github and examine it there: [{{< param blueprint_name >}}]({{< param basic_blueprint_master >}}/{{< param blueprint_name >}})


### Teardown

To remove the deployment and delete all resources from {{< param cloud >}} simply run the uninstall command:
```bash
cfy uninstall {{< param deployment_name >}}
```