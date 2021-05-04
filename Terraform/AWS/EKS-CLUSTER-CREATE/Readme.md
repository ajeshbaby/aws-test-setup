
Terraform is widely used for IAAC (Infrastructure As A Code)
There are several advantages of terraform like Immutable infrastructure and its declarative code style.
Support wide this terraform can be integrated and used for any cloud platform like GCP, AWS, Openstack, VMware, Azure and many more.
Good community support and other several advantages.


## Pre-requisite:

1)	Make sure to install awscli and key in your AccesKey+SecretKey (https://linuxconfig.org/install-aws-cli-on-ubuntu-18-04-bionic-beaver-linux) 

2) 	Make sure to select your EC2 instance family type from https://www.ec2instances.info/?region=us-west-1&selected=r5ad.xlarge,r3.xlarge.
	#### NOTE:  
	Be cautious  with the perhour billing of the instance we select. Try seeing the price of same EC2 instance from other regions and decide.
            Please question youself if we really need such powerful instances.
	    
	    
3) 	Since we are using SPOT INSTANCES, please make sure to go through the link to have an analysis about the SPOT INSTANCES and its prices for past 3 months    before selecting the family in your specific region. This will avoid having a DownTime:
	
		
	Always keep the price of SPOT INSTANCE close to ONDEMAND instances. This will help in avoiding un-necessary DT  
	https://console.aws.amazon.com/ec2sp/v1/spot/home?region=us-east-1
	
4) 	Have a look at the `variables.tf`	
	
	


## Steps:

1)	```
	cd /tmp   
	wget https://releases.hashicorp.com/terraform/0.12.24/terraform_0.12.24_linux_amd64.zip  
	unzip terraform_0.12.24_linux_amd64.zip  
	mv terraform ~/bin  
	terraform version 
	```


2)	Git clone this repository


3)	cd EKS-CLUSTER-CREATE

	Make sure to edit `variables.tf` without fail everytime you need a new cluster.Best way is to create a folder and move the files everytime when we need a 	  new cluster. Modify changes like `cluster-name`  `key-pair`   `region`    `instance-type`   & `spot prices` (https://console.aws.amazon.com/ec2sp/v1/spot/home?region=us-east-1)

4)	`terraform init`

5)	If no errors above than:
	`terraform plan`

6)	If no errors above than:
	`terraform apply`
	
7)	Once the cluster is created , make sure to gete the kubeconfig file copied to your localmachine for executing k8s cluster commands:  

	`aws eks update-kubeconfig --name <nameofyour-cluster --region <region-name>`

example:	`aws eks update-kubeconfig --name kumar-cluster --region us-east-1`
	
	
8)	Once done with Cluster, please destroy it:
	`terraform destroy`
	
	

9)	We also need to install 'iscsi' client across all the worker nodes:

	kubectl get nodes -o wide

 	### Once the above command gives output, execute the below command ###
	### Make sure to give the correct path of the `terraform.pem` or whatever is the key you have created ###


	```for i in `kubectl get nodes -o wide |awk {'print $7'} |sed '1d'`; do ssh -o "StrictHostKeyChecking no"  -q ec2-user@$i -i terraform.pem 'echo "***************************************";echo `hostname`;echo "***************************************"; sudo yum install iscsi-initiator-utils -y &&  sudo systemctl enable --now iscsid && sudo modprobe iscsi_tcp && echo `sudo systemctl status iscsid`';done```


## Limitations

Clusters created using Spot instances will not have Managed Node group feature enabled, so you might not be able to see the instances attached to your cluster by going inside "EKS-- > COMPUTE" tab. We will have Node group, though both of them uses same ASG for ScaleIn & ScaleOut.

If you need to ScaleOut -->> you need to go to "AutoScalingGroup" and select your Nodegroup and later ScaleIn or ScaleOut as per choice.

We will get this sorted in next release using worker launch template.



	

  ## CLUSTER-AUTOSCALER : TO BE Added Soon

	
