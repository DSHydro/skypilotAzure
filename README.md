# skypilotAzure
skypilot configuration for eScience 2025 accelerator

## Setting up skypilot

We'll use [skypilot](https://github.com/skypilot-org/skypilot) to manage machines on Azure. This requires that you already have an Azure account that skypilot can connect to.

There is no server-side management of machines (although you can setup a server to do this https://docs.skypilot.co/en/latest/reference/api-server/api-server.html), so we'll name resources per user to keep track of.

NOTE: the [.sky.yaml](.sky.yaml) file in this repository contains project configuration, and other files like `hello_sky.yml` contain configuration for running individual jobs or launching various cloud resources.


### Install

Prerequisites:
- Azure Account
- GitHub CLI https://cli.github.com
- Pixi https://pixi.sh/latest/installation/

```bash
gh repo clone DSHydro/skypilotAzure
pixi install
pixi shell # exit to deactivate
```

### Test Installation

```bash
sky check azure
sky status
```

## Run stuff

### Simple Workflow

(see [hello_sky.yaml](hello_sky.yaml)

See the skypilot docs for all the possibilities. Basically, we are launching a VM on Azure and running code, and optionally removing all the Azure resources when we're done.

This most basic example just runs a command that prints to the terminal, and auto deletes all resources .

NOTE: We put our laptop username (`whoami`) in the cluster name as a simple way to keep track of who is creating the resource and `tmp` in the cluster name as an additional way to indicate these cloud resources shouldn't be kept around.

```bash
CLUSTER_NAME=sky-`whoami`-tmp
CONFIG_FILE=hello_sky.yaml
sky launch -c $CLUSTER_NAME $CONFIG_FILE
# Alternatively, auto-generate unique name of cluster resource
# Bug? AttributeError: module 'sky.setup_files.dependencies' has no attribute 'AZURE_CLI'
# sky jobs launch $CONFIG_FILE
```

**!! IMPORTANT !!**
To save the contents of your local drive run `sky stop $CLUSTER_NAME`. To remove everything including attached local storage `sky down $CLUSTER_NAME`


### Advanced Workflow

(see [vscode.yaml](hello_sky.yaml)

Launch a persistant VM with more resources to connect to with VSCode for exploratory work. If the VM is idle for more than 10 minutes, it will be shutdown.

NOTE: Put `vscode` in the cluster name to indicate what it's for. If creating multiple VMs/clusters, they need to have unique names.

```bash
CLUSTER_NAME=sky-`whoami`-vscode
CONFIG_FILE=vscode.yaml
sky launch -c $CLUSTER_NAME $CONFIG_FILE
```

This will launch a VM, clone a repo, and initialize a pixi environment. It will likely take a few minutes. Once launched, you can connect to the VM via VSCode (https://docs.skypilot.co/en/latest/examples/interactive-development.html#vscode)

When you open a folder, select `/home/azureuser/sky_workdir/`


### GPU Workflow

(see [vscode-gpu.yaml](vscode-gpu.yaml)

## Remove all cloud resources

Note, if multiple resources exist you're prompted which to delete. It can take several minutes to remove everything in a given configuration

```
CLUSTER_NAME=sky-`whoami`-vscode-gpu
CONFIG_FILE=vscode-gpu.yaml
sky launch -c $CLUSTER_NAME $CONFIG_FILE
```


sky launch vm-plus-bucket-mount.yaml --dryrun


