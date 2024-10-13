# AIOps-demo-2

PS F:\workspace\vscode\AIOPS-LOCAL\module_2\demo2> set TENCENTCLOUD_SECRET_KEY=IKIDloyALTYYceiVguBeBfyLg4S5eGuLh8iL  
PS F:\workspace\vscode\AIOPS-LOCAL\module_2\demo2> set TENCENTCLOUD_SECRET_ID=xvmWGBGPWbsu3HUYn5g7NWVQE68UtSvf  

# cvm.tf  
terraform {  
  required_providers {  
    tencentcloud = {  
      source = "tencentcloudstack/tencentcloud"  
    }  
  }  
}  

\# Configure the TencentCloud Provider  
provider "tencentcloud" {  
  secret_id  = "xxx"  
  secret_key = "xxx"  
  region     = "ap-hongkong"  
}  

\# Get availability zones  
data "tencentcloud_availability_zones" "default" {}  

\# Get availability images  
data "tencentcloud_images" "default" {  
  image_type = ["PUBLIC_IMAGE"]  
  os_name    = "centos"  
}  

\# Get availability instance types  
data "tencentcloud_instance_types" "default" {   
  memory_size = 8  
}  

\# Create a web server  
resource "tencentcloud_instance" "web" {  
  instance_name              = "web server"  
  availability_zone          = data.tencentcloud_availability_zones.default.zones.0.name  
  image_id                   = data.tencentcloud_images.default.images.0.image_id  
  instance_type              = data.tencentcloud_instance_types.default.instance_types.0.instance_type  
  system_disk_type           = "CLOUD_PREMIUM"  
  system_disk_size           = 50  
  allocate_public_ip         = true  
  internet_max_bandwidth_out = 20  
  security_groups            = [tencentcloud_security_group.default.id]  
}

\# Create security group  
resource "tencentcloud_security_group" "default" {  
  name        = "web accessibility"  
  description = "make it accessible for both production and stage ports"  
}  

\# Create security group rule allow web request  
resource "tencentcloud_security_group_rule" "web" {  
  security_group_id = tencentcloud_security_group.default.id  
  type              = "ingress"  
  cidr_ip           = "0.0.0.0/0"  
  ip_protocol       = "tcp"  
  port_range        = "80,8080"  
  policy            = "accept"  
}  

\# Create security group rule allow ssh request  
resource "tencentcloud_security_group_rule" "ssh" {  
  security_group_id = tencentcloud_security_group.default.id  
  type              = "ingress"  
  cidr_ip           = "0.0.0.0/0"  
  ip_protocol       = "tcp"  
  port_range        = "22"  
  policy            = "accept"  
}   

PS F:\workspace\vscode\AIOPS-LOCAL\module_2\demo2> terraform apply -auto-approve  
data.tencentcloud_instance_types.default: Reading...  
data.tencentcloud_availability_zones.default: Reading...  
data.tencentcloud_images.default: Reading...  
data.tencentcloud_availability_zones.default: Read complete after 1s [id=1851109860]  
data.tencentcloud_images.default: Read complete after 1s [id=192136220]  
data.tencentcloud_instance_types.default: Read complete after 4s [id=1900960621]  

Terraform used the selected providers to generate the following execution plan. Resource actions  
are indicated with the following symbols:  
  + create  

Terraform will perform the following actions:  

  # tencentcloud_instance.web[0] will be created  
  + resource "tencentcloud_instance" "web" {  
      + allocate_public_ip                      = true  
      + availability_zone                       = "ap-hongkong-2"  
      + cpu                                     = (known after apply)  
      + create_time                             = (known after apply)  
      + disable_api_termination                 = false  
      + disable_monitor_service                 = false  
      + disable_security_service                = false  
      + expired_time                            = (known after apply)  
      + force_delete                            = false  
      + id                                      = (known after apply)  
      + image_id                                = "img-eb30mz89"  
      + instance_charge_type                    = "POSTPAID_BY_HOUR"  
      + instance_charge_type_prepaid_renew_flag = (known after apply)  
      + instance_name                           = "web server"  
      + instance_status                         = (known after apply)  
      + instance_type                           = "M8.SMALL8"  
      + internet_charge_type                    = (known after apply)  
      + internet_max_bandwidth_out              = 20  
      + key_ids                                 = (known after apply)  
      + key_name                                = (known after apply)  
      + memory                                  = (known after apply)  
      + orderly_security_groups                 = (known after apply)  
      + os_name                                 = (known after apply)  
      + private_ip                              = (known after apply)  
      + project_id                              = 0  
      + public_ip                               = (known after apply)  
      + running_flag                            = true  
      + security_groups                         = (known after apply)  
      + subnet_id                               = (known after apply)  
      + system_disk_id                          = (known after apply)  
      + system_disk_size                        = 50  
      + system_disk_type                        = "CLOUD_PREMIUM"  
      + uuid                                    = (known after apply)  
      + vpc_id                                  = (known after apply)  

      + data_disks (known after apply)  
    }  

  \# tencentcloud_security_group.default will be created  
  + resource "tencentcloud_security_group" "default" {    
      + description = "make it accessible for both production and stage ports"  
      + id          = (known after apply)  
      + name        = "web accessibility"  
      + project_id  = (known after apply)  
    }  

  \# tencentcloud_security_group_rule.ssh will be created  
  + resource "tencentcloud_security_group_rule" "ssh" {  
      + cidr_ip           = "0.0.0.0/0"  
      + description       = (known after apply)  
      + id                = (known after apply)  
      + ip_protocol       = "tcp"  
      + policy            = "accept"  
      + port_range        = "22"  
      + security_group_id = (known after apply)  
      + source_sgid       = (known after apply)  
      + type              = "ingress"  

      + address_template (known after apply)  

      + protocol_template (known after apply)  
    }  

  \# tencentcloud_security_group_rule.web will be created  
  + resource "tencentcloud_security_group_rule" "web" {  
      + cidr_ip           = "0.0.0.0/0"  
      + description       = (known after apply)  
      + id                = (known after apply)  
      + ip_protocol       = "tcp"  
      + policy            = "accept"  
      + port_range        = "80,8080"  
      + security_group_id = (known after apply)  
      + source_sgid       = (known after apply)  
      + type              = "ingress"  

      + address_template (known after apply)  

      + protocol_template (known after apply)  
    }  

Plan: 4 to add, 0 to change, 0 to destroy.  
tencentcloud_security_group.default: Creating...  
tencentcloud_security_group.default: Creation complete after 1s [id=sg-23vqlv8f]  
tencentcloud_security_group_rule.ssh: Creating...  
tencentcloud_security_group_rule.web: Creating...  
tencentcloud_instance.web[0]: Creating...  
tencentcloud_security_group_rule.ssh: Creation complete after 1s  [id=eyJzZ19pZCI6InNnLTIzdnFsdjhmIiwicG9saWN5X3R5cGUiOiJpbmdyZXNzIiwiY2lkcl9pcCI6IjAuMC4wLjAvMCIsInByb3RvY29sIjoidGNwIiwicG9ydF9yYW5nZSI6IjIyIiwiYWN0aW9uIjoiYWNjZXB0Iiwic291cmNlX3NnX2lkIjoiIiwiZGVzY3JpcHRpb24iOiIifQ==]  
tencentcloud_security_group_rule.web: Creation complete after 1s [id=eyJzZ19pZCI6InNnLTIzdnFsdjhmIiwicG9saWN5X3R5cGUiOiJpbmdyZXNzIiwiY2lkcl9pcCI6IjAuMC4wLjAvMCIsInByb3RvY29sIjoidGNwIiwicG9ydF9yYW5nZSI6IjgwLDgwODAiLCJhY3Rpb24iOiJhY2NlcHQiLCJzb3VyY2Vfc2dfaWQiOiIiLCJkZXNjcmlwdGlvbiI6IiJ9]         
tencentcloud_instance.web[0]: Still creating... [10s elapsed]  
tencentcloud_instance.web[0]: Still creating... [20s elapsed]  
tencentcloud_instance.web[0]: Still creating... [30s elapsed]  
╷  
│  
│   with data.tencentcloud_availability_zones.default,  
│   on cvm.tf line 17, in data "tencentcloud_availability_zones" "default":  
│   17: data "tencentcloud_availability_zones" "default" {}  
│  
│ This data source will been deprecated in Terraform TencentCloud provider later version. Please  
│ use `tencentcloud_availability_zones_by_product` instead.  
│  
│ (and 7 more similar warnings elsewhere)  
╷  
│ Warning: Argument is deprecated  
│  
│   with tencentcloud_instance.web,  
│   on cvm.tf line 40, in resource "tencentcloud_instance" "web":  
│   40:   security_groups            = [tencentcloud_security_group.default.id]  
│  
│ It will be deprecated. Use `orderly_security_groups` instead.  
│  
│ (and 2 more similar warnings elsewhere)  
╵  

Apply complete! Resources: 4 added, 0 changed, 0 destroyed.  
PS F:\workspace\vscode\AIOPS-LOCAL\module_2\demo2>   


![image](https://github.com/user-attachments/assets/de1e53d6-f34a-4e27-aa9a-8d3a04be2d6d)

PS F:\workspace\vscode\AIOPS-LOCAL\module_2\demo2> terraform import tencentcloud_instance.web ins-m9dla2yu  
data.tencentcloud_instance_types.default: Reading...  
data.tencentcloud_images.default: Reading...  
data.tencentcloud_availability_zones.default: Reading...  
data.tencentcloud_availability_zones.default: Read complete after 1s [id=1851109860]  
data.tencentcloud_images.default: Read complete after 2s [id=192136220]  
data.tencentcloud_instance_types.default: Read complete after 3s [id=1900960621]  
tencentcloud_instance.web: Importing from ID "ins-m9dla2yu"...  
tencentcloud_instance.web: Import prepared!  
  Prepared tencentcloud_instance for import  
tencentcloud_instance.web: Refreshing state... [id=ins-m9dla2yu]  

Import successful!  

The resources that were imported are shown above. These resources are now in  
your Terraform state and will henceforth be managed by Terraform.  

╷  
│ Warning: Deprecated Resource  
│  
│   with data.tencentcloud_availability_zones.default,  
│   on F:\workspace\vscode\AIOPS-LOCAL\module_2\demo2\cvm.tf line 17, in data "tencentcloud_availability_zones" "default":  
│   17: data "tencentcloud_availability_zones" "default" {}  
│
│ This data source will been deprecated in Terraform TencentCloud provider later version. Please    
│ use `tencentcloud_availability_zones_by_product` instead.  
╵

PS F:\workspace\vscode\AIOPS-LOCAL\module_2\demo2>   
PS F:\workspace\vscode\AIOPS-LOCAL\module_2\demo2> terraform init  
Initializing HCP Terraform...  
╷  
│ Error: Required token could not be found  
│  
│ Run the following command to generate a token for app.terraform.io:  
│     terraform login  

$ terraform login  
Terraform will request an API token for app.terraform.io using your browser.  

If login is successful, Terraform will store the token in plain text in  
the following file for use by subsequent commands:  
    C:\Users\Z840\AppData\Roaming\terraform.d\credentials.tfrc.json     

Do you want to proceed?  
  Only 'yes' will be accepted to confirm.  

  Enter a value: yes  


---------------------------------------------------------------------------------  

Terraform must now open a web browser to the tokens page for app.terraform.io.  

If a browser does not open this automatically, open the following URL to proceed:  
    https://app.terraform.io/app/settings/tokens?source=terraform-login  


---------------------------------------------------------------------------------  

Generate a token using your browser, and copy-paste it into this prompt.        

Terraform will store the token in plain text in the following file  
for use by subsequent commands:  
    C:\Users\Z840\AppData\Roaming\terraform.d\credentials.tfrc.json  

Token for app.terraform.io:  
  Enter a value:  


Retrieved token for user whitewatermy  


---------------------------------------------------------------------------------  

                                          -  
                                          -----                           -     
                                          ---------                      --     
                                          ---------  -                -----     
                                           ---------  ------        -------     
                                             -------  ---------  ----------     
                                                ----  ---------- ----------     
                                                  --  ---------- ----------     
   Welcome to HCP Terraform!                       -  ---------- -------        
                                                      ---  ----- ---  
   Documentation: terraform.io/docs/cloud             --------   -  
                                                      ----------  
                                                      ----------  
                                                       ---------  
                                                           -----  
                                                               -  


   New to HCP Terraform? Follow these steps to instantly apply an example configuration:  

   $ git clone https://github.com/hashicorp/tfc-getting-started.git  
   $ cd tfc-getting-started  
   $ scripts/setup.sh  



Z840@HP-Z840 MINGW64 /f/workspace/vscode/AIOPS-LOCAL/module_2/demo2  

PS F:\workspace\vscode\AIOPS-LOCAL\module_2\demo2> terraform init  
Initializing HCP Terraform...  
Do you wish to proceed?  
  As part of migrating to HCP Terraform, Terraform can optionally copy  

  Answer "yes" to copy the latest state snapshot to the configured  
  HCP Terraform workspace.  

  Answer "no" to ignore the existing state and just activate the configured     
  HCP Terraform workspace with its existing state, if any.  

  Should Terraform migrate your existing state?  

  Enter a value: yes  

Releasing state lock. This may take a few moments...  
Initializing provider plugins...  
- Reusing previous version of tencentcloudstack/tencentcloud from the dependency lock file  
- Using previously-installed tencentcloudstack/tencentcloud v1.81.131  

HCP Terraform has been successfully initialized!  

You may now begin working with HCP Terraform. Try running "terraform plan" to   
see any changes that are required for your infrastructure.  

If you ever set or change modules or Terraform Settings, run "terraform init"     
again to reinitialize your working directory.  
PS F:\workspace\vscode\AIOPS-LOCAL\module_2\demo2>   

