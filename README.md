# VM_Migration_over_Subscriptions
This Powershell script helps in migrating a VM from one subscription to another along with the attached OS and Data disks.

Parameters Description:

  $location = 'region' 
  $vmName = 'VM which is to be migrated'
	$sourceResourceGroupName = 'the resource group which contains the VM that is to be migrated'
	$sourceSubscriptionId = 'Subscription ID of the subscription where the source resource group is present'
	$sourceSubscriptionName = "Subscription name of the subscription where the source resource group is present" 
	$targetSubscriptionId = 'Subscription ID of the subscription where the target resource group is present'
	$targetResourceGroupName = 'the resource group where the VM needs to be migrated'
	$targetSubscriptionName = "Subscription name of the subscription where the source resource group is present"
	$snapshotsResourceGroupName = "name of the resource group where you want the primary snapshots to be stored(this should be in source subscription)"
	$tags = @{'Application_Name' = 'Name of the application'; 'Environment' = 'name of the environement'}
	$osSkuName = "Standard_LRS" <keeping the cost in mind, this is the suggested SKU for a snapshot that is to be created>

  
#Network Parameters
	$targetVnetName = "VNET name where the migrated VM should be in i.e. in target subscription"
	$targetSubnetName = "SUBNET name where the migrated VM should be in i.e. in target VNET"
	$targetPrivateIp = "any static private IP which lies in the range of the target VNET=>target SUBNET"
	$vnetResourceGroupName = "Resource group name where the target VNET is present"
  
  NOTE:
  1)Necessary changes need to be made in case we need to set up public IP for a VM
  2)Need to add necessary extensions(based on your organizational compliance perspective)
  3)Need to attach storage account for boot diagnostics.

