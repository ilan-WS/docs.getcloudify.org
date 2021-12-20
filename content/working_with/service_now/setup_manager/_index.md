+++
title = "Cloudify Manager Setup"
description = "Get started with setting up your hosted service trial or deploying your own trial server"
weight = 102
alwaysopen = false
+++

The first stop in this journey is to setup your Cloudify Environment.

If you have x_clop2_cloudify.admin role you should be able to see the application menu :

from the menu modules you can select Getting Started Setup then you can get started with Cloudify Environment

Cloudify Environment can be in many flavors :

* As a Hosted Service
* Installed on public network [SSL,Non-SSL] and in case of SSL with self-signed certificate
  {the certificate needs to be added to ServiceNow}
* Installed on private network [mid-server needs to be configured]

for more details on how to setup Cloudify manager please refer to [{{< param cfy_manager_name >}} setup]({{< relref "/trial_getting_started/set_trial_manager/_index.md" >}}) before moving on.


## Adding Cloudify Manager External Certificate *Self-Signed*

To add the certificate you will need to do the following:

* Get the manager external certificate
* Login to your ServiceNow instance as admin
* Using the navigation on left pane , search for certificate
* Under System Definitions -> you will find Certificates
* Click on new and give it a name , and select PEM format and paste the content of the certificate


## Mid Server Configuration

To configure the mid server we will follow ServiceNow installation instructions:

* Create a user and grant it `mid_server` role and specific to our application we will also grant it access to `sys_attachment` table
* Download the mid_installer package and install it on the host inside the private network
* Add the mid server name to the config that refer to the local manager endpoint

a video to show the mid-server installation and configuration

<iframe src="https://drive.google.com/file/d/1GtFI9iTezeGKTylrxX2w3cfs8Nzb_0qb/view" width="500" height="300" frameborder="0" allow="autoplay; fullscreen" allowfullscreen></iframe>


## Getting Started Cloudify Environment

a video that demonstrate how to setup Cloudify as a service and configure various flavors of Cloudify environments:

<iframe src="https://drive.google.com/file/d/1-LXyrjKXkW4M6xAzSVAEmX9OuFaIuoLj/view" width="500" height="300" frameborder="0" allow="autoplay; fullscreen" allowfullscreen></iframe>

{{%children style="h3" description="true"%}}
