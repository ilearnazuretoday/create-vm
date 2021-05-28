# Create VM in Azure

Copy and paste commands into Azure cloud shell

> Hint
> We are not going to go this not, but you could also use `az interactive` mode for command completion

## Setup variables

Setup variables we will be using to create and manage vm. Make sure they are created correctly by checking echo output

```bash
group='RG-LEARNING-AZURE'
vm='your name'

echo $group, $vm
```

## Create linux ubuntu VM

Here we are creating a simple Linux VM with a Ubuntu image. Azure will create for us a VM user which we call *azureuser* and allow SSHing into VM with existing SSH keys

> Leaving an open SSH port on the maching is considered unsecure and should be avoided in production scenarios. Here we are doing it only for demo purposes.

```bash
az vm create \
  --resource-group $group \
  --name $vm \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

Check out the porta, has your VM been created?

## Open port 8000 for web traffic

In order to allow external traffic to our VM, we need to allow it. In Azure this is done by opening an external port in the VM.

```bash
az vm open-port --port 8000 --resource-group $group --name $vm

ip=$(az vm list-ip-addresses -g $group -n $vm --query "[].virtualMachine.network.publicIpAddresses[*].ipAddress" -o tsv)

echo 'Puclic is is:' $ip
```

## SSH to the machine

Create a new shell session and ssh into the machine

ssh azureuser@<copied IP>

## Create simple web app

Here we are createing a minimalistic html page and starting a simple python server to serve our html via opened 8000 port.

> Python3 http.server serves traffic by default on 8000 port. Alos by default the server will serve index.html content from the root locaiton where the server was started.


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

<IP Address>:8000

### Wget

wget -O- http://{$ip}:8000

## Cleanup resources

Explore vm before deleting it manually from Azure portal
