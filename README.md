# Create VM in Azure

In this exercise you will learn how to:

- create Azure virtual machine
- expose port on the virtual machine
- run a sample web app
- access the web app from internet
- remove virtual machine and accompanying resources

## Prerequisites

In order to follow up with the exercises, following prerequisites are required:

- Azure subscription. It's very easy to [create one with 12 months of free services](https://azure.microsoft.com/en-us/free/)
- basic understating of bash

This exercise is self paced and you will create resources on Azure while doing it, please remember to **remove the resources as per instructions** to avoid unexpected charges.

The easiest way to follow on with the exercise is to login to your Azure account after creating one and clone the repository in the [Azure cloud shell](https://docs.microsoft.com/en-us/azure/cloud-shell/overview). For the purposes of the exercise, we will copy and paste commands into Azure cloud shell.

> Hint
> We are not going to do this now, but you could also use `az interactive` mode for command completion

## Automation

For learning purposes please follow below instructions step by step, but if you would like to create the vm in a fully automated way including the web app, please use [create-vm-script](create-vm.sh).

> you can easily see all available Azure locations by running `az account list-locations -o table`. Use second column *Name* to supply location for the script.

## Setup variables

Setup variables we will be using to create and manage vm. Make sure they are created correctly by checking echo output. Resource groups and vms names in Azure must be unique across your subscriptions and additionally vm DNS labels must be unique globally. Please make sure to choose names that fit the above requirement.

```bash
group='your group name'
vm='your vm name'

echo $group, $vm
```

## Create linux ubuntu VM

Here we are creating a simple Linux VM with a Ubuntu image. Azure will create for us a VM user which we call *azureuser* and allow SSHing into VM with existing SSH keys

> Leaving an open SSH port on the virtual machine is considered insecure and should be avoided in production scenarios. Here we are doing it only for demo purposes.

```bash
az vm create \
  --resource-group $group \
  --name $vm \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

Check out the Azure portal, has your VM been created? Type your resource group name in the search box.

## Open port 8000 for web traffic

In order to allow external traffic to our VM, we need to open a port In Azure this is done by opening an external port in the VM.

```bash
az vm open-port --port 8000 --resource-group $group --name $vm

ip=$(az vm list-ip-addresses -g $group -n $vm --query "[].virtualMachine.network.publicIpAddresses[*].ipAddress" -o tsv)

echo 'Public is is:' $ip
```

## SSH to the machine

Create a new shell session and ssh into the machine

ssh azureuser@IP

## Create simple web app

Here we are creating a minimalist's html page and starting a simple python server to serve our html via opened 8000 port.

> Python3 http.server serves traffic by default on 8000 port. Also by default the server will serve index.html content from the root location where the server was started.

```bash
mkdir webapp
cd webapp
echo "Hallo this is simple web app" > index.html
python3 -m http.server
```

## Access website from a browser or wget

Swap back to previous session and call web page via wget or via browser.

> If you forgot the ip address of your vm, use `echo $ip`

### Browser

IP Address:8000

### Wget

`wget -O- http://$ip:8000`

> Check out how server is serving http page via GET verb

## Cleanup resources

Explore vm before deleting it manually from Azure portal
