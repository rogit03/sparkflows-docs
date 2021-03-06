CloudFormation Template with External DB
========================


Overview
--------

Using CloudFormation Templates, Fire can be easily installed on AWS. This CFT works with EMR 5.8 onwards.

The below steps would allow you to start up an EMR Cluster and have Fire setup on it.

The CFT does the following:

* Creates External DB for Fire to be used as the metastore for Fire data
* Creates EMR cluster with 1 master node and 2 worker nodes by default.
* Once the cluster is ready it runs the job/script to deploy Fire (takes around 1-1:30 min for deploying app!).


Relevant Files
--------------

.. list-table:: Below are the Relevant Files
   :widths: 10 20 40
   :header-rows: 1

   * - Title
     - Description
     - File
   * - emr-file.json
     - CloudFormation Template
     - https://s3.amazonaws.com/sparkflows-release/fire/CFT/emr-fire.json
   * - deploy-fire.sh
     - Script for deploying Fire
     - https://s3.amazonaws.com/sparkflows-release/fire/CFT/deploy-fire.sh
   * - script-runner.jar
     - Script Runner
     - https://s3.amazonaws.com/sparkflows-release/fire/CFT/script-runner.jar
     

Ports
-----

* With this CFT and deploy-fire.sh, when Fire comes up, it would be listening on ports 8085 and 8086.

Download Files and Upload to your S3 Bucket
----------------------------------------------

* Download CFT **emr-fire.json** from the above link.
* Download **deploy-fire.sh** and **script-runner.jar** from the above links and upload them to your s3 bucket


Update Cloudformation template based on your environment
---------------------------------------------------------

Update the CFT **emr-fire.json** according to your requirement and environment in which you are deploying.

* ElasticMapReduce-Master-SecurityGroup under mastersg::

    From AWS console -> EC2 -> Security Groups -> search for "ElasticMapReduce-master"
  
  
* ElasticMapReduce-Slave-SecurityGroup under slavesg::

    From AWS console -> EC2 -> Security Groups -> search for "ElasticMapReduce-slave"
  
  
* Applications::

    By default the CFT deploys Hadoop, Hive & Spark. Add any other Applications which you need.
  
  
* EbsRootVolumeSize::

    If required change the root(/) ebs volume size. By default CFT has 50GB disk volume
  
  
* SizeInGB for Master and Core Instances::

    If required change the SizeInGB under EbsConfiguration. By default CFT has 50GB disk volume (used for hdfs)
  
  
* VolumesPerInstance for Master and Core Instances::

    If required change the VolumesPerInstance under EbsConfiguration By default cft has 1. It means one additional disk of 50GB added to each instance(for hdfs). e.g. If you change it 2, two 50GB (SizeInGB size) disks will be added to each instances.
  
  
* deploy-fire.sh and script-runner.jar::

    Change the s3 bucket path for these two files, this s3 bucket  must be same bucket as S3Bucket. You'll pass the S3Bucket value while creating the cloudformation stack.


Steps to Create EMR Cluster and Deploy Fire
--------------------------------------------------

* **AWS web Console -> Management tools -> CloudFormation**

  * Click on **Create Stack**.
  
* Next page is **Select Template**

  * Select the radio-button **Upload a template to Amazon S3**
  * Select the updated **emr-fire.json** from your system
  * Click Next
  
* Next page is **Specify Details**

  * Enter CloudFormation stack name
 
 
.. list-table:: Update Parameters where needed
   :widths: 10 40
   :header-rows: 1

   * - Name of Parameter
     - Description
   * - AdditionalSecurityGroups
     - From the list choose the additional secuirty group(sg), it's required because default emr sg's ports are not opened for ssh, fire & etc...
   * - AmiId
     - EMR cluster can be launched using Custom AMI, pass the value if you have a Custom AMI
   * - ClusterName
     - Name for EMR Cluster
   * - CoreInstanceType
     - Provide the required instance type for core nodes, default instance type is m4.xlarge
   * - CoreNodes
     - Choose the required number of core nodes, by default it’s 2
   * - EmrVersion
     - Choose the required EMR version, it’s should be above EMR v.5.8.x
   * - Environment
     - By default dev
   * - FireVersion
     - Enter the required version of Fire
   * - KeyName
     - Enter the valid pem key name to connect to emr nodes
   * - MasterInstanceType
     - Provide the required instance type for master nodes, default instance type is m4.xlarge
   * - MasterNodes
     - By default 1 
   * - Owner
     -  provide the name of a team or person creating the cluster
   * - ReleaseVersion
     - Enter the required ReleaseVersion, it has to match with fire version
   * - S3Bucket
     - Provide the s3 bucket name, this s3 bucket should be same s3 bucket where deploy-fire.sh and script-runner.jar are uploaded
   * - Subnet
     - Provide the proper subnet name, which has sufficient resources to create emr cluster 
   * - TaskInstanceType
     - Optional, required only if you’re choosing TaskNodes. Provide the required instance type for task nodes, default instance type is m4.xlarge
   * - TaskNodes
     -  Optional, required only if you want to create the cluster with tasknodes.By default zero, enter the required number of nodes


* Click Next
  
* Next Page is **Options**

  * If required (not mandatory) enter tag details
  * Click Next
  
* Next Page is **Review**

  * Review all the details provided to create an EMR stack
  * Click on Create
  * It will start creating the Stack

* Next page is back to **Cloudformation Page**

  * Choose your Stack name
  * Click on **Events** to check the process
  * Click on **Resources** to get the EMR Cluster id
  
  
* Once the stack runs successfully, your EMR Cluster and Fire is ready to use. Cluster creation time depends on your EMR cluster configuration


* To **cross check** the Fire installation

  * Go to EMR from AWS web console
  * Choose your EMR Cluster
  * Identify the Master Node Public DNS 
  * Go to **http://masternodeip:8085/index.html**
  
  
Connect Fire to the New Cluster
-------------------------------

* Go to User/Administration
* Click on **Infer Hadoop Configuration**
* Save

Load Examples
--------------

* In Fire, click on **Load Examples**
* ssh to the master node
* cd /opt/fire/fire-3.1.0
* hadoop fs -put data

Create **hadoop** user
----------------------

* Go to User/User
* Click on **Add User**
* Create a new user with username 'hadoop'
* Log out and log back in as user **hadoop*

Start running the Examples
--------------------------

* Go to **Applications**
     
Summary
-------

Using the above CFT you have your EMR cluster with Fire running seamlessly.
