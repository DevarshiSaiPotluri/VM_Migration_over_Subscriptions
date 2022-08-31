#Select the correct subscription
	Set-AzContext -Subscription $sourceSubscriptionName

	#Set some parameters
	$location = 'region' 
	$vmName = 'VM which is to be migrated'
	$sourceResourceGroupName = ''
	$sourceSubscriptionId = ''
	$sourceSubscriptionName = "" 
	$targetSubscriptionId = ''
	$targetResourceGroupName = ''
	$targetSubscriptionName = ""
	$snapshotsResourceGroupName = "name of the resource group where you want the primary snapshots to be stored(this should be in source subscription)"
	$tags = @{'Application_Name' = 'Application Name'; 'Environment' = 'ENV'; 'Owner' = ''; 'Managed' = 'Portal'; 'DeviceLocation' = ''; 'Disaster_Recovery' = 'Tier 0'; 'PatchingCategory' = 'NonProduction' }

	#Get the VM details
	$vm = Get-AzVM -ResourceGroupName $sourceResourceGroupName -Name $vmName
	$vmSize = $vm.HardwareProfile.VmSize
	$vmOsTypeOffer = $vm.StorageProfile.ImageReference.Offer


	#OS DISK
	Set-AzContext -Subscription $sourceSubscriptionName
	$osDisk = $vm.StorageProfile.OsDisk
	$osDiskObj = Get-AzDisk -Name $osDisk.name
	$osDiskId = $osDiskObj.Id
	$osDiskName = $osDisk.name
	$osSnapshotName = $osDiskName + "_SNAPSHOT"
	$osSkuName = "Standard_LRS"
	$osDiskSkuName = $vm.StorageProfile.OsDisk.ManagedDisk.StorageAccountType
	

	#Create the os disk snapshot configuration
	$osSnapshotCreate = New-AzSnapshotConfig -SourceUri $osDiskId -Location $location -CreateOption copy

	#Take the os disk snapshot
	New-AzSnapshot -Snapshot $osSnapshotCreate -SnapshotName $osSnapshotName -ResourceGroupName $snapshotsResourceGroupName 

	#Get OutPut
	$osSnapshot = Get-AzSnapshot -ResourceGroupName $snapshotsResourceGroupName -Name $osSnapshotName
	Write-Output $osSnapshot.Id


	#Set the context to the subscription Id where os snapshot will be copied to
	Set-AzContext -SubscriptionId $targetSubscriptionName
		
	#Configuring new VM
	$targetVmConfig = New-AzVMConfig -VMName $vm.name -VMSize $vmSize

	#Network Parameters
	$targetVnetName = ""
	$targetSubnetName = ""
	$targetPrivateIp = ""
	$vmNicName = $vm.Name + "-nic1"
	$vnetResourceGroupName = ""
	$targetVnet = Get-AzVirtualNetwork -Name $targetVnetName -ResourceGroupName $vnetResourceGroupName
	$targetSubnet = Get-AzVirtualNetworkSubnetConfig -Name $targetSubnetName -VirtualNetwork $targetVnet

	#Creating NIC for the target VM
	$targetVmNic = New-AzNetworkInterface  -Name $vmNicName -ResourceGroupName $targetResourceGroupName -Location $location -SubnetId $targetSubnet.Id -PrivateIpAddress $targetPrivateIp

	#Adding NIC to the target VM config
	$targetVmConfig = Add-AzVMNetworkInterface -VM $targetVmConfig -Id $targetVmNic.Id
		

	#store os snapshot in Standard storage to reduce cost.
	$osSnapshotConfig = New-AzSnapshotConfig -SourceResourceId $osSnapshot.Id -Location $osSnapshot.Location -SkuName $osSkuName -CreateOption Copy

	#Create a new os snapshot in the target subscription and resource group
	New-AzSnapshot -Snapshot $osSnapshotConfig -SnapshotName $osSnapshotName -ResourceGroupName $targetResourceGroupName

	#Creating New OS Disk in Target Subscription
	#Creating OS disk config from osSnapshot
	$targetOsSnapshot = Get-AzSnapshot -ResourceGroupName $targetResourceGroupName -Name $osSnapshotName
	$osDiskConfig = New-AzDiskConfig -Location $location -SourceResourceId $targetOsSnapshot.Id -SkuName $osDiskSkuName -CreateOption Copy

	#Creating new OS disk out of the osSnapshot
	$newOsDisk = New-AzDisk -Disk $osDiskConfig -ResourceGroupName $targetResourceGroupName -DiskName $osDiskName 

	if ($vmOsTypeOffer -eq "WindowsServer") {
		#Set OS disk for VM config for Windows
		$targetVmConfig = Set-AzVMOSDisk -VM $targetVmConfig -ManagedDiskId $newOsDisk.Id -CreateOption Attach -Caching ReadWrite -Windows 
	}
	elseif ($vmOsTypeOffer -eq "UbuntuServer") {
		#Set OS disk for VM config for Linux
		$targetVmConfig = Set-AzVMOSDisk -VM $targetVmConfig -ManagedDiskId $newOsDisk.Id -CreateOption Attach -Caching ReadWrite -Linux
	}

	# Creating the virtual machine.
	Write-Host  "Creating virtual machine {"$vmName"} in"  $targetResourceGroupName  "of" $targetSubscriptionName  "as a part of " $vmName "migration!"
	#Creating new VM out of target VM config
	$newVmCreate = New-AzVM -VM $targetVmConfig -ResourceGroupName $targetResourceGroupName -Location $location
	Write-Output $newVmCreate
	Write-Host  "Virtual machine {"$vmName"} created successfully"

	$getNewVM = Get-AzVM -Name $vmName -ResourceGroupName $targetResourceGroupName

	# Adding Tags to the new VM
	Update-AzTag -Tag $tags -ResourceId $getNewVM.Id -Operation Merge -Verbose
	
	if ($vm.StorageProfile.DataDisks -ne $null) {
		#DATA DISKS
		Set-AzContext -Subscription $sourceSubscriptionName
		$dataDisks = $vm.StorageProfile.DataDisks
		$index = 0
		foreach ($dataDisk in $dataDisks) {
			Set-AzContext -Subscription $sourceSubscriptionName
			$dataDiskObj = Get-AzDisk -Name $dataDisk.name
			$dataDiskName = $dataDisk.name
			$dataSnapshotName = $dataDiskName + "_SNAPSHOT"
			$dataDiskID = $dataDiskObj.Id
			$dataDiskSkuName = $vm.StorageProfile.DataDisks[$index].ManagedDisk.StorageAccountType
			$dataSkuName = "Standard_LRS"
		
			#Create the data disk snapshot configuration
			$dataSnapshotCreate = New-AzSnapshotConfig -SourceUri $dataDiskID -Location $location -CreateOption copy

			#Take the data disk snapshot
			New-AzSnapshot -Snapshot $dataSnapshotCreate -SnapshotName $dataSnapshotName -ResourceGroupName $snapshotsResourceGroupName

			#Get OutPut
			$dataSnapshot = Get-AzSnapshot -ResourceGroupName $snapshotsResourceGroupName -Name $dataSnapshotName
			Write-Output $dataSnapshot.Id
			
			#Get the LUN value of the old attached data disk
			$lun = $dataDisk.Lun

			Write-Output $lun

			#Set the context to the subscription Id where data snapshot will be copied to
			Set-AzContext -SubscriptionId $targetSubscriptionName

			#store data snapshot in Standard storage to reduce cost.
			$dataSnapshotConfig = New-AzSnapshotConfig -SourceResourceId $dataSnapshot.Id -Location $dataSnapshot.Location -SkuName $dataSkuName -CreateOption Copy 

			#Create a new data snapshot in the target subscription and resource group
			New-AzSnapshot -Snapshot $dataSnapshotConfig -SnapshotName $dataSnapshotName -ResourceGroupName $targetResourceGroupName

			#Creating DATA disk config from dataSnapshot
			$targetDataSnapshot = Get-AzSnapshot -ResourceGroupName $targetResourceGroupName -Name $dataSnapshotName
			$dataDiskConfig = New-AzDiskConfig -Location $location -SourceResourceId $targetDataSnapshot.Id -SkuName $dataDiskSkuName -CreateOption Copy

			#Creating new DATA disk out of the dataSnapshot
			$newDataDisk = New-AzDisk -Disk $dataDiskConfig -ResourceGroupName $targetResourceGroupName -DiskName $dataDiskName

			Write-Output $newDataDisk.Name

			# Attach the new data disk to the new VM
			$disk = Get-AzDisk -ResourceGroupName $targetResourceGroupName -DiskName $newDataDisk.Name

			$getNewVM = Get-AzVM -Name $vmName -ResourceGroupName $targetResourceGroupName

			$getNewVM = Add-AzVMDataDisk -CreateOption Attach -Lun $lun -VM $getNewVM -ManagedDiskId $disk.Id

			Update-AzVM -VM $getNewVM -ResourceGroupName $targetResourceGroupName


			$dataDiskConfig = $null
			$dataSnapshotCreate = $null 
			$dataDiskID = $null
			$newDataDisk = $null
			$lun = $null
			$index = $index + 1

		}
	}
	else {
		Write-Host  "This VM has no Data Disks"
	}

	Write-Host  "Thank you! your VM {"$getNewVM.Name "} has been migrated successfully from " $sourceResourceGroupName "of" $sourceSubscriptionName "to" $targetResourceGroupName "of" $targetSubscriptionName
   
	# #Cleaning up the snapshots from $snapshotsResourceGroupName resource group
	# Set-AzContext -SubscriptionId $sourceSubscriptionName
	# $snapshots = Get-AzResource -ResourceType Microsoft.Compute/snapshots -ResourceGroupName $snapshotsResourceGroupName
	# Write-Output $snapshots
	# foreach ($snapshot in $snapshots) {
	# 	Remove-AzResource -ResourceGroupName $snapshotsResourceGroupName  -ResourceType Microsoft.Compute/snapshots -ResourceName $snapshot.Name -Force
	# }
