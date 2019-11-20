# VMware VRA OpenShift 4 Automation
OpenShift 4 Automation for add worker nodes using VMware VRA
using Ansible Tower we can automate add worker nodes task in OpenShift 4

below is the high level of workflow

![Screenshot from 2019-11-21 06-09-53](https://user-images.githubusercontent.com/32007820/69286352-9fa5fc00-0c25-11ea-819b-24ea0dc9b49f.png)

- Requester will request for a new project and new worker for the OpenShift Container Platform. Parameters include, but not limited to, name of ProjectGroup, number of workers and new username.
- VRealize Business Logic, i.e. quota check, permission to request, next level of approval, etc.
- Approval request sent to approver
- Approver approves request
- vRealize will contact vCenter to clone VM template worker with below parameters:
  - VM Name
  - VM UUID
  - VM Mac-address (generated by vRA)
  - VM IP address (generated by vRA)
- vRealize will trigger POST REST API with the requirements (as extra variables) to Ansible Tower. The extra variables include:
  - VM Name
  - VM UUID
  - VM Mac-address (generated by vRA)
  - VM IP address (generated by vRA)
  - New OpenShift Username
  - New OpenShift ProjectGroup name with labels
- Ansible will inform DHCP server to register mac-address to IP address
- Ansible will inform infoblox about the VM IP address and register the VM in the DNS Server (Forward and Reverse)
- Ansible will trigger vSphere to turn on the VM (based on VM name or UUID) and monitor the processes until the worker node joins the openshift cluster
- After new worker joined cluster, Ansible will provision the new username, new node labeling and new ProjectGroup
- Ansible Tower will inform vRA that the action has been successfully completed

Below is Ansible Tower workflow for automate openshift 4 add worker nodes

![WhatsApp Image 2019-11-20 at 4 16 47 PM](https://user-images.githubusercontent.com/32007820/69286493-fad7ee80-0c25-11ea-9757-646406fba1f4.jpeg)

1. powered-off-vmware-instance.yaml
2. vmware-facts-get-macaddress.yaml
3. add-dhcp-static-ip.yaml
4. add-dns-record.yaml
5. powered-on-vmware-instance.yaml
6. ocp-get-status.yaml
7. ocp-provisioner-2.yaml
8. ocp-result.yaml

below is demo video based on current configuration:
https://drive.google.com/file/d/1ZSW6FAWStPMKAycdEQlqDFgadiCkvEUG/view?usp=sharing 

Thanks!
