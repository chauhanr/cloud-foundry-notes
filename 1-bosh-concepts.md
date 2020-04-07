# BOSH concepts 

BOSH is a release engineering tool that was purpose built for release and deployments of cloud
foundry (CF) however it can be used to deploy software onto VM, containers and even physical
machines. 

The main advantage of BOSH as a release engineering tool chain is that it is cloud provider agnostic
when it comes to release rollout. This is made possible by the Cloud provider interface (CPI) a
layer that BOSH interacts with when rolling out the software releases. It is up to the cloud
providers (IaaS) to implement all the primitives that BOSH needs to call in order to roll out the
deployment. 
The concept of the CPI is unique to BOSH as similar tools like Terraform still need to know which
IaaS they are trying to interact with. 

## BOSH primitives 
There are 3 primitives that BOSH works with: 
1. Stemcells 
2. Releases 
3. Deployments 

### Stemcells 
In biology stemcell is an undifferntiated cell that can be differentiated into several type of
specialized cells throughout the body. A BOSH stemcell follows a similar concept and it has: 
* bare minimum OS skeletal image with preinstalled common utilities - these are hardened to reduce
  the attack surface. 
* configuration files to secure the image 
* BOSH agent to report back to the BOSH director 
* IaaS specific packaging and verisoning. 

The stemcell are agnostic to the software that will be installed on them in the downstream. So the
stemcells become specialized VM once the BOSH deployments take hold and install the software needed
on top of the base image. The specialized downstream software is often called a BOSH release. 

Because the same image is used everywhere it becomes easier to push changes like security patches as
well as upgrade the software.

The stem cells are nothing but tarballs. The BOSH team is the one responsible to preparing and publishing 
the BOSH Images. 

### Releases 
BOSH releases is a collection of the software, configurations and dependency it needs to be
installed/ deployed to a VM in a reproduceable way. A single BOSH release is a collection of
software that need to work in unison.

The concept of BOSH releases being IaaS agnostic holds as the IaaS specific details are handled by
the CPI and the cloud configurations files in BOSH. This is the reason that the Pivotal team was
able to take the Pivotal Web Services off AWS and move it to GCP in a matter of weeks. 

The BOSH release is made up of one or more release jobs and you are free to choose which job you
want to run and deploy different components. The releae jobs are deployed to a VM and are know as
what is called an **instance group**. Example of an instance group are Diego cell, cloud controller
etc. 

Cloud Foundry is encapsulated in a BOSH release and is present in the cf-deployments project Example
of an instance group are Diego cell, cloud controller etc. 

### Deployments 

BOSH deploys software to the actual machine using what is called a deployment manifest which is
nothing but a yaml file that defines which release job runs on which machine and which instance
group is deployed. 

The BOSH will push the jobs mentioned on the release to the appropriate machine with the stemcell
and the jobs will go ahead and run the scripts, code and configuraitons files to install the
software that is part of the job. 

Remember that the instance group jobs are run on the same VM. 

## Cloud Configurations 

In order to keep BOSH IaaS agnostic the cloud configuraiton yaml files will hold all the IaaS
specific details and that is how BOSH knows about the IaaS that is actually running under neath. As
the cloud config is just a configuration the BOSH deployment and release code is free from the IaaS
level details. 

The information that the cloud configurations contain are 
* compilation VM
* Availability ZOnes 
* networks
* VM types 
* disk types etc. 

 THe configurations for the cloud providers are present in a file called `iaas.yaml` and containes a
whole bunch of details. 

[Next](2-bosh-releases.md)
