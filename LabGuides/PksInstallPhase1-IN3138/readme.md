# Lab PKS Installation Phase 1

**Contents:**

- [Lab Access Instructions](https://github.com/CNA-Tech/PKS-Ninja/tree/master/LabGuides/PksInstallPhase1-IN3138#lab-access-instructions)
- [Step 1: Deploy Ops Manager](https://github.com/CNA-Tech/PKS-Ninja/tree/master/LabGuides/PksInstallPhase1-IN3138#step-1-deploy-ops-manager)
- [Step 2: Deploy BOSH](https://github.com/CNA-Tech/PKS-Ninja/tree/master/LabGuides/PksInstallPhase1-IN3138#step-2-deploy-bosh)
- [Step 3: Prep for PKS Installation](https://github.com/CNA-Tech/PKS-Ninja/tree/master/LabGuides/PksInstallPhase1-IN3138#step-3-prep-for-pks-install)
- [Next Steps](https://github.com/CNA-Tech/PKS-Ninja/tree/master/LabGuides/PksInstallPhase1-IN3138#next-steps)

## Lab Access Instructions

For PKS Ninja students using the labs provided in the course, the lab admins will provide you with an IP address to RDP into the ControlCenter desktop in the vPod that has been assigned to you.

Obtaining IP address of the ControlCenter desktop (If using VMware Learning Platform): From browser on the virtual lab environment deskop, go to http://myip.oc.vmware.com/

All instructions in this lab guide should be performed from the ControlCenter desktop unless otherwise specified.

PKS installation on vSphere requires NSX-T to be installed. If NSX-T is not installed in your environment, jump to [NSX-T Pipeline Install](/LabGuides/NsxtPipelineInstall-IN7016/) to install and return here. One way to verify if NSX-T is installed is try accessing the NSX Manager/Console.

## Step 1: Deploy Ops Manager

1.1 Launch the Chrome browser from the desktop or Windows taskbar, and launch the **RegionA vCenter** vSphere web client from the **RegionA** bookmarks folder, select *Use Windows session authentication* and click **Login**. In the vSphere web client, right click on the `pks-mgmt-1` resource pool and select `Deploy OVF Template`

<details><summary>Screenshot 1.1</summary>
<img src="Images/2018-10-21-16-56-33.png">>
</details>
<br/>

1.2 On the `Select template` screen, select `Local File` and navigate to the Ops Manager OVA file. The file is E:\Downloads, and named "pcf-vsphere-2.3.build.170.ova"

<details><summary>Screenshot 1.2</summary>
<img src="Images/2018-10-21-17-03-48.png">
</details>
<br/>

1.3 On the `Select name and location` screen, rename the Virtual machine name `opsman` and select `RegionA01` as the datacenter

<details><summary>Screenshot 1.3</summary>
<img src="Images/2018-10-21-17-09-11.png">
</details>
<br/>

1.4 On the `Select a resource` screen, select the `pks-mgmt-1` resource pool

<details><summary>Screenshot 1.4</summary>
<img src="Images/2018-10-21-17-12-16.png">
</details>
<br/>

1.5 On the `Review details` screen, confirm the details and click `Next`

<details><summary>Screenshot 1.5</summary>
<img src="Images/2018-10-21-17-13-13.png">
</details>
<br/>

1.6 On the `Select storage` screen, set `Thin Provision` as the virtual disk format and `RegionA01-ISCSI01-COMP01` as the datastore

<details><summary>Screenshot 1.6</summary>
<img src="Images/2018-10-21-17-14-47.png">
</details>
<br/>

1.7 On the `Select networks` screen, ensure the `Destination Network` is set to `VM-RegionA01-vDS-MGMT`.

Note: this VM will later be attached to the `ls-pks-mgmt`, however we are connecting it to a different network during the `Deploy OVF Template` wizard as at the time of writing, there is a bug that prevents attachment to a logical switch. After the OVF deployment is complete, a later step will have you change the network attachment.

<details><summary>Screenshot 1.7</summary>
<img src="Images/2018-10-21-17-16-11.png">
</details>
<br/>

1.8 On the `Customize template` screen, enter the following variables:

  - Admin Password: VMware1!
  - Custom Hostname: opsman
  - DNS: 192.168.110.10
  - Default Gateway: 172.31.0.1
  - IP Address: 172.31.0.3
  - NTP Servers: ntp.corp.local
  - Netmask: 255.255.255.0
  - Public SSH Key: (leave blank)

<details><summary>Screenshot 1.8</summary>
<img src="Images/2018-10-21-17-30-07.png">
</details>
<br/>

1.9 On the `Ready to complete` screen, confirm the details and click `Finish`

<details><summary>Screenshot 1.9</summary>
<img src="Images/2018-10-21-17-31-21.png">
</details>
<br/>

1.10 After completing the `Deploy OVF Template` wizard, go to your recent tasks view and wait for the `Status` to change to `Completed` before proceeding

Note: In the Nested example lab, it takes ~20 minutes to deploy the Ops Manager VM

<details><summary>Screenshot 1.10</summary>
<img src="Images/2018-10-21-18-42-50.png">
</details>
<br/>

1.11 In the vSphere web client in the `Hosts and Clusters` view, expand the `pks-mgmt-1` resource pool and select the opsman vm. On the `Actions` pulldown select `Edit Settings`

<details><summary>Screenshot 1.11</summary>
<img src="Images/2018-10-21-19-35-33.png">
</details>
<br/>

1.12 On the `Edit Settings` menu for the opsman vm, set `Network Adapter 1` to `ls-pks-mgmt` If you don't see this network, that means NSX-T hasn't been installed as required. Please jump to [NSX-T Pipeline Install](/LabGuides/NsxtPipelineInstall-IN7016/) and install NSX-T first.

<details><summary>Screenshot 1.12</summary>
<img src="Images/2018-10-21-19-39-17.png">
</details>
<br/>

1.13 In the vSphere web client, right click on the opsman vm and select `Power On`

<details><summary>Screenshot 1.13</summary>
<img src="Images/2018-10-21-19-40-08.png">
</details>
<br/>

1.14 Open a web browser connection to `opsman.corp.local` and select `Internal Authentication`

<details><summary>Screenshot 1.14</summary>
<img src="Images/2018-10-21-19-47-13.png">
</details>
<br/>

1.15 On the `Internal Authentication` screen, enter the following values, check the box to agree to terms and conditions and click `Setup Authentication`

- Username: admin
- Password: VMware1!
- Decryption Passphrase: VMware1!
- Check "I agree to the terms and conditions..."
- Click "Setup Authentication"

Note: After clicking `Setup Authentication` it will take several minutes for the authentication system to start. The login screen will appear after the authentication system is finished starting up

<details><summary>Screenshot 1.15</summary>
<img src="Images/2018-10-21-19-49-15.png">
</details>
<br/>

1.16 From the Ops Manager web UI, login with Username: `Admin` Password: `VMware1!`

<details><summary>Screenshot 1.16</summary>
<img src="Images/2018-10-21-19-55-42.png">
</details>
<br/>

## Step 2: Deploy BOSH

2.1 From the ControlCenter desktop, open putty and connect to `cli-vm`. At the Bash prompt enter the following command:

`openssl s_client -host nsxmgr-01a.corp.local -port 443 -prexit -showcerts`

If the above command fails with gethostbyname failure, then use the IP address of nsxmgr-01a which is set to be  192.168.110.42. You can verify this by doing nslookup nsxmgr-01a.

`openssl s_client -host 192.168.110.42 -port 443 -prexit -showcerts`

Save the section of the output from `Begin Certificate` to `End Certificate` for use in the following steps, be sure to include the `---Begin Certificate---` and `---End Certificate---` header and footer

<details><summary>Screenshot 2.1</summary>
<img src="Images/2018-10-21-21-43-02.png">
</details>
<br/>

2.2 Log into the Ops Manager web UI and click on the tile `BOSH Director for vSphere`

<details><summary>Screenshot 2.2</summary>
<img src="Images/2018-10-21-21-07-42.png">
</details>
<br/>

2.3 On the `vCenter Configuration` page, enter the following values and click `Save`:
- Name: vcsa-01a
- vCenter Host: vcsa-01a.corp.local
- vCenter Username: administrator@vsphere.local
- vCenter Password: VMware1!
- Datacenter Name: RegionA01
- Virtual Disk Type: thin
- Ephemeral Datastore Names: RegionA01-ISCSI01-COMP01
- Persistent Datastore Names: RegionA01-ISCSI01-COMP01
- Select `NSX Networking`
- NSX Mode: NSX-T
- NSX Address: nsxmgr-01a.corp.local
- NSX Username: admin
- NSX Password: VMware1!
- Copy and Paste the NSX-T Cert from step 2.1
- VM Folder: pks_vms
- Template Folder: pks_templates
- Disk path Folder: pks_disk

<details><summary>Screenshot 2.3.1</summary>
<img src="Images/2018-10-21-21-29-43.png">
</details>

<details><summary>Screenshot 2.3.2</summary>
<img src="Images/2018-10-21-21-44-38.png">
</details>
<br/>

2.4 Continue with the Bosh Director tile configuration, select the `Director Config` tab on the left side menu and enter the following values:

- NTP Servers: ntp.corp.local
- Enable VM Resurrector Plugin: True
- Enable Post Deploy Scripts: True
- Recreate All VMs: True
- Leave all other settings set to default values and click `Save`

<details><summary>Screenshot 2.4</summary>
<img src="Images/2018-10-21-21-52-58.png">
</details>
<br/>

2.5 Continue with the Bosh Director tile configuration, select the `Create Availability Zones` tab and enter the following details:

Note: Each of the availability zones below will have a single cluster. When you add an availability zone, make sure to click `Add` on the upper right side of the window and do **not** click `Add Cluster`
- Click `Add` to add an Availability Zone with the following values
  - Name: PKS-MGMT-1
  - IaaS Configuration: vcsa-01a
  - Cluster: RegionA01-MGMT01
  - Resource Pool: pks-mgmt-1
- Click `Add` to add an Availability Zone with the following values
  - Name: PKS-COMP
  - IaaS Configuration: vcsa-01a
  - Cluster: RegionA01-COMP01
  - Resource Pool: pks-comp-1
- Click Save

<details><summary>Screenshot 2.5</summary>
<img src="Images/2018-10-21-22-06-12.png">
</details>
<br/>

2.6 Continue with the Bosh Director tile configuration, select the `Create Networks` tab and enter the following values:

- Enable ICMP Checks: True
- Click `Add Network` to add a network with the following values
  - Name: PKS-MGMT
  - vSphere Network Name: ls-pks-mgmt
  - CIDR: 172.31.0.0/24
  - Reserved IP Ranges: 172.31.0.1-172.31.0.3 
  - DNS 192.168.110.10
  - Gateway 172.31.0.1
  - Availability Zones: PKS-MGMT-1
- Click `Add Network` to add a network with the following values:
  - Name: PKS-COMP
  - vSphere Network Name: ls-pks-service
  - CIDR: 172.31.2.0/23
  - Reserved IP Ranges: 172.31.2.1
  - DNS: 192.168.110.10
  - Gateway: 172.31.2.1
  - Availability Zones: PKS-COMP
  -Click Save

<details><summary>Screenshot 2.6.1</summary>
<img src="Images/2018-10-21-23-02-32.png">
</details>

<details><summary>Screenshot 2.6.2</summary>
<img src="Images/2018-10-22-15-21-58.png">
</details>
<br/>

2.7 Continue with the Bosh Director tile configuration, select the `Assign AZs and Networks` tab and enter the following values:

- Singleton Availability Zone: PKS-MGMT-1
- Network: PKS-MGMT
- Click Save

<details><summary>Screenshot 2.7</summary>
<img src="Images/2018-10-21-23-17-12.png">
</details>
<br/>

2.8 Continue with the Bosh Director tile configuration, select the `Resource Config` tab and change the value of the `VM Type` in the second row to `medium.disk` as shown in Screenshot 2.8
-Click Save

<details><summary>Screenshot 2.8</summary>
<img src="Images/2018-10-22-01-12-45.png">
</details>
<br/>

2.9 In the Ops Manager web UI, click on `Installation Dashboard` on the top menu bar and then click `Review Pending Changes`

<details><summary>Screenshot 2.9</summary>
<img src="Images/2018-10-21-23-23-00.png">
</details>
<br/>

2.10 On the `Review Pending Changes` screen, ensure that the checkbox for Bosh Director is checked and click `Apply Changes`

<details><summary>Screenshot 2.10</summary>
<img src="Images/2018-10-21-23-24-52.png">
</details>
<br/>

2.11 Review the `Applying Changes` to observe the BOSH VM deployment. This will take a while to complete. While BOSH is deploying, you can skip ahead to Step 3 and return to the `Applying Changes` screen periodically to check the status of the deployment. Once the BOSH deployment is complete, you should see a `Changes applied` popup window as shown in Screenshot 2.11.2

Note: In the nested example lab, it takes ~30 minutes to complete the BOSH deployment

<details><summary>Screenshot 2.11.1 </summary>
<img src="Images/2018-10-21-23-26-50.png">
</details>

<details><summary>Screenshot 2.11.2 </summary>
<img src="Images/2018-10-22-00-41-06.png">
</details>
<br/>

2.12 In the vSphere web client from the `Hosts and Clusters` view, expand the pks-mgmt-1 resource pool and you should see the BOSH vm

<details><summary>Screenshot 2.12 </summary>
<img src="Images/2018-10-22-00-51-05.png">
</details>

## Step 3: Prep for PKS Install

3.1 Generate NSX-T Principal Identity certificate (You will need this for PKS Intallation)

3.1.1 From the ControlCenter desktop, open putty and connect to `cli-vm`. Enter the following command:

``` bash
mkdir nsxt-pi-cert
cd nsxt-pi-cert
```

3.1.2 Use a text editor to create a file with the following shell script to generate the PI cert, for example `nano create_certificate.sh`. Copy the following text to the file:

<details><summary>Click to expand create_certificate.sh</summary>

``` bash
#!/bin/bash
#create_certificate.sh

NSX_MANAGER="192.168.110.42"
NSX_USER="admin"

PI_NAME="pks-nsx-t-superuser"
NSX_SUPERUSER_CERT_FILE="pks-nsx-t-superuser.crt"
NSX_SUPERUSER_KEY_FILE="pks-nsx-t-superuser.key"

stty -echo
printf "Password: "
read NSX_PASSWORD
stty echo

openssl req \
  -newkey rsa:2048 \
  -x509 \
  -nodes \
  -keyout "$NSX_SUPERUSER_KEY_FILE" \
  -new \
  -out "$NSX_SUPERUSER_CERT_FILE" \
  -subj /CN=pks-nsx-t-superuser \
  -extensions client_server_ssl \
  -config <(
    cat /etc/ssl/openssl.cnf \
    <(printf '[client_server_ssl]\nextendedKeyUsage = clientAuth\n')
  ) \
  -sha256 \
  -days 730

cert_request=$(cat <<END
  {
    "display_name": "$PI_NAME",
    "pem_encoded": "$(awk '{printf "%s\\n", $0}' $NSX_SUPERUSER_CERT_FILE)"
  }
END
)

curl -k -X POST \
    "https://${NSX_MANAGER}/api/v1/trust-management/certificates?action=import" \
    -u "$NSX_USER:$NSX_PASSWORD" \
    -H 'content-type: application/json' \
    -d "$cert_request"
```

</details>
Press `ctrl o` `enter` and then `ctrl x` to return to bash prompt
<br/>

<details><summary>Screenshot 3.1.2</summary>
<img src="Images/2018-10-22-02-30-12.png">
</details>
<br/>

3.1.3 Return to the bash prompt enter the command `bash create_certificate.sh` and enter the password `VMware1!` when prompted

<details><summary>Screenshot 3.1.3</summary>
<img src="Images/2018-10-22-02-45-20.png">
</details>
<br/>

3.1.4 Review the contents of the NSX PI certificate and key and save or copy the contents as you will need these keys in later steps

``` bash
cat pks-nsx-t-superuser.crt
cat pks-nsx-t-superuser.key
```

<details><summary>Screenshot 3.1.4</summary>
<img src="Images/2018-10-22-02-52-14.png">
</details>
<br/>

3.1.5 In the NSX Manager UI, go to System > Trust to view certificates. You should now see a certificate for `pks-nsx-t-superuser`
Login for NSX Manager UI is: admin/VMware1!

<details><summary>Screenshot 3.1.5</summary>
<img src="Images/2018-10-22-03-42-57.png">
</details>
<br/>

3.2 Create and Register Principal Identity

3.2.1 From the `cli-vm` prompt, use a text editor to create a file with the following shell script and your certificate ID to generate the PI cert, for example `nano create_pi.sh`. _\[Do not cut and paste this script exactly, make sure to change the CERTIFICATE_ID to the id value from the create_certificate.sh output found in step 3.1.3]_

<details><summary>Click to expand create_pi.sh</summary>

``` bash
#!/bin/bash
#create_pi.sh

NSX_MANAGER="192.168.110.42"
NSX_USER="admin"
CERTIFICATE_ID='27fbd52c-a90e-478f-9fd1-2fb52625c9fe'

PI_NAME="pks-nsx-t-superuser"
NSX_SUPERUSER_CERT_FILE="pks-nsx-t-superuser.crt"
NSX_SUPERUSER_KEY_FILE="pks-nsx-t-superuser.key"
NODE_ID=$(cat /proc/sys/kernel/random/uuid)

stty -echo
printf "Password: "
read NSX_PASSWORD
stty echo

pi_request=$(cat <<END
    {
         "display_name": "$PI_NAME",
         "name": "$PI_NAME",
         "permission_group": "superusers",
         "certificate_id": "$CERTIFICATE_ID",
         "node_id": "$NODE_ID"
    }
END
)

curl -k -X POST \
    "https://${NSX_MANAGER}/api/v1/trust-management/principal-identities" \
    -u "$NSX_USER:$NSX_PASSWORD" \
    -H 'content-type: application/json' \
    -d "$pi_request"

curl -k -X GET \
    "https://${NSX_MANAGER}/api/v1/trust-management/principal-identities" \
    --cert $(pwd)/"$NSX_SUPERUSER_CERT_FILE" \
    --key $(pwd)/"$NSX_SUPERUSER_KEY_FILE"
```

</details>
<br/>

<details><summary>Screenshot 3.2.1</summary>
<img src="Images/2018-10-22-03-15-42.png">
</details>
<br/>

3.2.2 Return to the bash prompt and enter the command `bash create_pi.sh` and enter the password `VMware1!` when prompted. Your output should look similar to Screenshot 3.2.2 below

<details><summary>Screenshot 3.2.2</summary>
<img src="Images/2018-10-22-03-25-06.png">
</details>
<br/>

3.2.3 In the NSX Manager UI, go to System > Users and verify that you see a user account for `pks-nsx-t-superuser`
Login for NSX Manager UI is: admin/VMware1!

<details><summary>Screenshot 3.2.3</summary>
<img src="Images/2018-10-22-03-32-45.png">
</details>
<br/>

3.3 Import the PKS Tile

- Note: If you are not sure if your BOSH deployment has completed yet, check now to see if it is complete, using step 2.11 above if needed for reference
  - If the deployment is complete, return to the Ops Manager homepage and proceed with the following steps
  - If your BOSH deployment is not yet complete, leave your browser tab open to continue to monitor the deployment status and open a new tab and connect to the Ops Manager UI so that you have 2 active browser sessions. Use the 2nd Ops Manager connection to complete the following step

3.3.1 Log into the Ops Manager UI, Click `Import a Product`, select the Pivotal Container Service binary file. This is the final step of the phase 1 lab, when you resume with the phase 2 installation lab you will complete the PKS installation

Note: In the nested example lab, it takes ~10 minutes to import the PKS tile

<details><summary>Screenshot 3.2.1.1 </summary>
<img src="Images/2018-10-22-01-34-24.png">
</details>

<details><summary>Screenshot 3.2.1.2 </summary>
<img src="Images/2018-10-22-01-27-45.png">
</details>
<br/>

## Next Steps

### [Please click here to proceed to PKS Installation Phase 2](LabGuides/PksInstallPhase2-IN1916/)
