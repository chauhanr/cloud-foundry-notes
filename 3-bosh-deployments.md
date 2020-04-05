# BOSH Deployments 

Once we have defined the releases using the BOSH release structure we have blobs or tar files for a
release. Next we have the step of taking these tar files (releases) and installing them on a VM
(instance groups) so that the component starts running. The process of defining which release is
part of which instance group is crutial and that is done by defining the deployment manifests. 

Deployment manifests contain the following sections: 
* deployment name 
* directors unique id (UUID) 
* stem cells 
* release - the name of the release that we have built as part of the release phase.  
* instance groups - 
* properties 
* updates. 

**Stemcells** 
The stem cells in the deployment manifest will have the following structure and will define the stem
cell that BOSH has to use for deployment. 

```
stemcells: 
- alias: default
  name: ubuntu-trusty
  version: 3074 
```

**Instance Groups** 
This section is the most important and complex as we define which job component gets deployed on
which VM in order to make the component work properly. 

The following are the properties of an instance group: 
1. Each instance group encapsulate a separate VM
2. There can be N number of intances 
3. They can comprise of one or more release jobs from one or more releases. 
4. Each represents a discrete functionality. (cloud controller, uaa, gorouter) 

An example is that of UAA instance groups - there are 5 release jobs that are part of this instance
group: 

```
instance_group: 
- name: uaa 
  ...
  jobs: 
  - name: consul_agent
    release: consul
    properties: 
    ...
  - name: uaa 
    release: uaa 
    properties: 
    ... 
  - name: route_registrar 
    release: routing 
    properties: 
    .. 
  - name: metron_agent
    release: loggregator
    properties: 
    ... 
  - name: statsd-injector 
    release: loggregator 
    properties: 
    ...

```

### properties 
There are two levels at which the properties can be defined: 
1. At the instance group level  
2. global level - being phased out as they tend to pollute the manifests. 
3. job level properties - these are appicable to all at the job level. 

```
instance_group: 
 - name: uaa 
  ...
  jobs: 
    # job level properties 
    properties: 
  
  properties:
    # instance group level  

properties: 
 # global level properties. 

``` 

### Credentials 
for secerts like the certificates as well as passwords that need to be kept, cloud foundry has a
component called cred hub that keeps the properties for passwods and other creds. 

The definition of the property can be pulled from cred hub in the following way: 

```
properties: 
  password: ((postgres_password)) 

```

here the CF will look for this property in cred hub and then set it here. 


