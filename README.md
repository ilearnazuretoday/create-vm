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
```bash
az vm create \
  --resource-group $group \
  --name $vm \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

## Open port 8000 for web traffic
```bash
az vm open-port --port 8000 --resource-group $group --name $vm

ip=$(az vm list-ip-addresses -g $group -n $vm --query "[].virtualMachine.network.publicIpAddresses[*].ipAddress" -o tsv)

echo 'Puclic is is:' $ip
```

## SSH to the machine

Create a new shell session and ssh into the machine

ssh azureuser@<copied IP>

## Create simple web app

```bash
mkdir webapp
cd webapp
echo "Hallo this is simple web app" > index.html
python3 -m http.server
```

## Access website from a browser or curl

### Browser

<IP Address>:8000

### Curl

curl http://{$ip}:8000

## Cleanup resources

Explore vm before deleting it manually from Azure portal
