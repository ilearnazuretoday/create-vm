# Create VM in Azure

Copy and paste commands into Azure cloud shell

> Hint
> Use `az interactive` mode for command completion

## Setup variables
group='RG-LEARNING-AZURE'

vm='your name'

## Create linux ubuntu VM
az vm create \
  --resource-group $group \
  --name $vm \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys

## Open port 8000 for web traffic
az vm open-port --port 8000 --resource-group $group --name $vm

ip=$(az vm list-ip-addresses -g $group -n $vm --query "[].virtualMachine.network.publicIpAddresses[*].ipAddress" -o tsv)
echo 'Puclic is is:' $ip

## SSH to the machine
ssh $ip

## Create simple web app

mkdir webapp
cd webapp
echo "Hallo this is simple web app" > index.html
python3 -m http.server

## Access website from a browser or curl

### Browser

<IP Address>:8000

### Curl

curl http://{$ip}:8000

## Cleanup resources

az vm delete  -g $group -m $vm --yes
