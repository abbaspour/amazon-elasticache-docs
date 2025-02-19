# Using global datastores \(console\)<a name="Redis-Global-Datastores-Console"></a>

To create a global datastore using the console, follow this two\-step process:

1. Create a primary cluster, either by using an existing cluster or creating a new cluster\. The engine must be Redis 5\.0\.6 or later\.

1. Add up to two secondary clusters in different AWS Regions, again using the Redis 5\.0\.6 engine or later\.

The following procedures guide you on how to create a global datastore for Redis and perform other operations using the ElastiCache for Redis console\.

**Topics**
+ [Creating a global datastore using an existing cluster](#Redis-Global-Datastores-Console-Create-Primary)
+ [Creating a new global datastore using a new primary cluster](#Redis-Global-Datastores-Create-From-Scratch)
+ [Viewing global datastore details](#Redis-Global-Datastores-Console-Details)
+ [Adding a Region to a global datastore](#Redis-Global-Datastores-Console-Create-Secondary)
+ [Modifying a global datastore](#Redis-Global-Datastores-Console-Modify-Regional-Clusters)
+ [Promoting the secondary cluster to primary](#Redis-Global-Datastores-Console-Promote-Secondary)
+ [Removing a Region from a global datastore](#Redis-Global-Datastore-Console-Remove-Region)
+ [Deleting a global datastore](#Redis-Global-Datastores-Console-Delete-GlobalDatastore)

## Creating a global datastore using an existing cluster<a name="Redis-Global-Datastores-Console-Create-Primary"></a>

In this scenario, you use an existing cluster to serve as the primary of the new global datastore\. You then create a secondary, read\-only cluster in a separate AWS Region\. This secondary cluster receives automatic and asynchronous updates from the primary cluster\. 

**Important**  
The existing cluster must use the Redis 5\.0\.6 engine or later\.

**To create a global datastore using an existing cluster**

1. Sign in to the AWS Management Console and open the ElastiCache console at [ https://console\.aws\.amazon\.com/elasticache/](https://console.aws.amazon.com/elasticache/)\.

1. On the navigation pane, choose **Redis** and then choose a cluster\. 

1. For **Actions**, choose **Setup Global Datastore**\.

1. On the **Setup Global Datastore** page, do the following:
   + Enter a value for **Global Datastore Name suffix**: This suffix is used to generate a unique name for the global datastore\. You can search for the global datastore by using the suffix that you specify here\. 
   + \(Optional\) Enter a **Description** value\. 

1. Under **Secondary cluster details**, choose a different AWS Region where the cluster will be stored\.

1. Under **Redis settings**, enter a value for **Name** and, optionally, for **Description** for the cluster\.

1. Keep the following options as they are\. They're prepopulated to match the primary cluster configuration, you can't change them\.
   + Engine version
   + Node type
   + Parameter group
**Note**  
ElastiCache autogenerates a new parameter group from values of the provided parameter group and applies the new parameter group to the cluster\. Use this new parameter group to modify parameters on a global datastore\. Each autogenerated parameter group is associated with one and only one cluster and, therefore, only one global datastore\.
   + Number of shards
   + Encryption at rest – Enables encryption of data stored on disk\. For more information, see [Encryption at rest](https://docs.aws.amazon.com/AmazonElastiCache/latest/red-ug/at-rest-encryption.html)\.
**Note**  
You can supply a different encryption key by choosing **Customer Managed Customer Master Key** and choosing the key\. For more information, see [Using customer managed CMKs from AWS KMS](https://docs.aws.amazon.com/AmazonElastiCache/latest/red-ug/at-rest-encryption.html#using-customer-managed-keys-for-elasticache-security)\.
   + Encryption in\-transit – Enables encryption of data on the wire\. For more information, see [Encryption in transit](https://docs.aws.amazon.com/AmazonElastiCache/latest/red-ug/in-transit-encryption.html)\. For Redis engine version 6\.x, if you enable encryption in\-transit you are prompted to specify one of the following **Access Control** options:
     + **No Access Control** – This is the default setting\. This indicates no restrictions\.
     + **User Group Access Control List** – Choose a user group with a defined set of users and permissions on available operations\. For more information, see [Managing User Groups with the Console and CLI](Clusters.RBAC.md#User-Groups)\.
     + **Redis AUTH Default User** – An authentication mechanism for Redis server\. For more information, see [Redis AUTH](https://docs.aws.amazon.com/AmazonElastiCache/latest/red-ug/auth.html)\.

1. \(Optional\) As needed, update the remaining secondary cluster settings\. These are prepopulated with the same values as the primary cluster, but you can update them to meet specific requirements for that cluster\.
   + Port
   + Number of replicas
   + Subnet group
   + Preferred Availability Zone\(s\)
   + Security groups
   + Customer Managed \(Customer Master Key\)
   + Redis AUTH Token
   + Enable automatic backups
   + Backup retention period
   + Backup window
   + Maintenance window
   + Topic for SNS notification

1. Choose **Create**\. Doing this sets the status of the global datastore to **Creating**\. The status transitions to **Modifying** after the primary cluster is associated to the global datastore and the secondary cluster is in **Associating** status\.

   After the primary cluster and secondary clusters are associated with the global datastore, the status changes to **Available**\. At this point, you have a primary cluster that accepts reads and writes and secondary clusters that accept reads replicated from the primary cluster\.

   The Redis page is updated to indicate whether a cluster is part of a global datastore, including:
   + **Global Datastore** – The name of the global datastore to which the cluster belongs\.
   + **Global Datastore Role** – The role of the cluster, either primary or secondary\.

You can add up to one additional secondary cluster in a different AWS Region\. For more information, see [Adding a Region to a global datastore](#Redis-Global-Datastores-Console-Create-Secondary)\.

## Creating a new global datastore using a new primary cluster<a name="Redis-Global-Datastores-Create-From-Scratch"></a>

If you choose to create a new global datastore, use the following procedure\. 

**To create a new global datastore**

1. Sign in to the AWS Management Console and open the ElastiCache console at [ https://console\.aws\.amazon\.com/elasticache/](https://console.aws.amazon.com/elasticache/)\.

1. On the navigation pane, choose **Global Datastore** and then choose **Create**\.

1. Under **Create Global Datastore**, do the following:

   1. Enter a value for **Global Datastore Name suffix**\. ElastiCache uses the suffix to generate a unique name for the global datastore\. You can search for the global datastore by using the suffix that you specify here\.

   1. \(Optional\) Enter a value for **Global Datastore Description**\.

1. Under **Primary cluster details**, for **Region**, choose an available AWS Region and one of the following options:
   + [Creating a new regional cluster as a primary](#Redis-Global-Datastores-Console-Create-Primary-Create-New)\.
   + [Using an existing cluster as primary cluster](#Redis-Global-Datastores-Console-Create-Primary-Using-Existing-1) 

### Creating a new regional cluster as a primary<a name="Redis-Global-Datastores-Console-Create-Primary-Create-New"></a>

**To create a new regional cluster as a primary**

1.  Follow the steps at [Creating a Redis \(cluster mode enabled\) cluster \(console\)](https://docs.aws.amazon.com/AmazonElastiCache/latest/red-ug/Clusters.Create.CON.RedisCluster.html)\.
**Note**  
When you select a parameter group to set the engine configuration values, that parameter group is applied to all clusters in the global datastore\. On the **Parameter Groups** page, the yes/no **Global** attribute indicates whether a parameter group is part of a global datastore\.

1. After you have successfully created the cluster in the previous step, choose **Next** to configure your secondary cluster details\.

1. Under **Secondary cluster details**, select a different AWS Region where the cluster will be stored\.

1. Under **Redis settings**, enter a value for **Name** and, optionally, for **Description** for the cluster\.

1. The following options are prepopulated to match the primary cluster configuration and cannot be changed:
   + Engine version
   + Instance type
   + Node type
   + Number of shards
   + Parameter group
**Note**  
ElastiCache autogenerates a new parameter group from values of the provided parameter group and applies the new parameter group to the cluster\. Use this new parameter group to modify parameters on a global datastore\. Each autogenerated parameter group is associated with one and only one cluster and, therefore, only one global datastore\.
   + Encryption at rest – Enables encryption of data stored on disk\. For more information, see [Encryption at rest](https://docs.aws.amazon.com/AmazonElastiCache/latest/red-ug/at-rest-encryption.html)\.
**Note**  
You can supply a different encryption key by choosing **Customer Managed Customer Master Key** and choosing the key\. For more information, see [Using customer managed CMKs from AWS KMS](https://docs.aws.amazon.com/AmazonElastiCache/latest/red-ug/at-rest-encryption.html#using-customer-managed-keys-for-elasticache-security)\.
   + Encryption in\-transit – Enables encryption of data on the wire\. For more information, see [Encryption in transit](https://docs.aws.amazon.com/AmazonElastiCache/latest/red-ug/in-transit-encryption.html)\. For Redis engine version 6\.4 and above, if you enable encryption in\-transit you are prompted to specify one of the following **Access Control** options:
     + **No Access Control** – This is the default setting\. This indicates no restrictions on user access to the cluster\.
     + **User Group Access Control List** – Choose a user group with a defined set of users that can access the cluster\. For more information, see [Managing User Groups with the Console and CLI](Clusters.RBAC.md#User-Groups)\.
     + **Redis AUTH Default User** – An authentication mechanism for Redis server\. For more information, see [Redis AUTH](https://docs.aws.amazon.com/AmazonElastiCache/latest/red-ug/auth.html)\.
   + \.

     Redis AUTH – An authentication mechanism for Redis server\. You can supply a different Redis AUTH token\. For more information, see [Redis AUTH](https://docs.aws.amazon.com/AmazonElastiCache/latest/red-ug/auth.html)\.
**Note**  
For Redis versions between 4\.0\.2, when Encryption in\-transit was first supported, and 6\.0\.4, Redis AUTH is the sole option\.

   The remaining secondary cluster settings are pre\-populated with the same values as the primary cluster, but the following can be updated to meet specific requirements for that cluster:
   + Port
   + Number of replicas
   + Subnet group
   + Preferred Availability Zone\(s\) 
   + Security groups
   + Customer Managed \(Customer Master Key\) 
   + Redis AUTH Token
   + Enable automatic backups
   + Backup retention period
   + Backup window
   + Maintenance window
   + Topic for SNS notification

1. Choose **Create**\. This sets the status of the global datastore to **Creating**\. After the primary cluster and secondary clusters are associated with the global datastore, the status changes to **Available**\. You have a primary cluster that accepts reads and writes and a secondary cluster that accepts reads replicated from the primary cluster\.

   The Redis page is also updated to indicate whether a cluster is part of a global datastore, including the following:
   + **Global Datastore** – The name of the global datastore to which the cluster belongs\.
   + **Global Datastore Role** – The role of the cluster, either primary or secondary\.

You can add up to one additional secondary cluster in a different AWS Region\. For more information, see [Adding a Region to a global datastore](#Redis-Global-Datastores-Console-Create-Secondary)\.

### Using an existing cluster as primary cluster<a name="Redis-Global-Datastores-Console-Create-Primary-Using-Existing-1"></a>

If you choose this option, follow the steps in [Creating a global datastore using an existing cluster](#Redis-Global-Datastores-Console-Create-Primary) beginning with step 5\. 

## Viewing global datastore details<a name="Redis-Global-Datastores-Console-Details"></a>

You can view the details of existing global datastores and also modify them on the **Global Datastore** page\.

**To view global datastore details**

1. Sign in to the AWS Management Console and open the ElastiCache console at [ https://console\.aws\.amazon\.com/elasticache/](https://console.aws.amazon.com/elasticache/)\.

1. On the navigation pane, choose **Global Datastore** and then choose an available global datastore\.

You can then examine the following global datastore properties:
+ **Global Datastore Name:** The name of the global datastore
+ **Description:** A description of the global datastore
+ **Status:** Options include:
  + Creating
  + Modifying
  + Available
  + Deleting
  + Primary\-Only \- This status indicates the global datastore contains only a primary cluster\. Either all secondary clusters are deleted or not successfully created\.
+ **Cluster Mode:** Either enabled or disabled
+ **Redis Engine Version:** The Redis engine version running the global datastore
+ **Instance Node Type:** The node type used for the global datastore
+ **Encryption at\-rest:** Either enabled or disabled
+ **Encryption in\-transit:** Either enabled or disabled
+ **Redis AUTH:** Either enabled or disabled

You can make the following changes to the global datastore:
+ [Adding a Region to a global datastore](#Redis-Global-Datastores-Console-Create-Secondary) 
+ [Removing a Region from a global datastore](#Redis-Global-Datastore-Console-Remove-Region) 
+ [Promoting the secondary cluster to primary](#Redis-Global-Datastores-Console-Promote-Secondary)
+ [Modifying a global datastore](#Redis-Global-Datastores-Console-Modify-Regional-Clusters)

The Global Datastore page also lists the individual clusters that make up the global datastore and the following properties for each:
+ **Region** \- The AWS Region where the cluster is stored
+ **Role** \- Either primary or secondary
+ **Cluster name** \- The name of the cluster
+ **Status** \- Options include:
  + **Associating** \- The cluster is in the process of being associated to the global datastore
  + **Associated** \- The cluster is associated to the global datastore
  + **Disassociating** \- The process of removing a secondary cluster from the global datastore using the global datastore name\. After this, the secondary cluster no longer receives updates from the primary cluster but it remains as a standalone cluster in that AWS Region\.
  + **Disassociated** \- The secondary cluster has been removed from the global datastore and is now a standalone cluster in its AWS Region\.
+ **Global Datastore Replica lag** – Shows one value per secondary AWS Region in the global datastore\. This is the lag between the secondary Region's primary node and the primary Region's primary node\. For cluster mode enabled Redis, the lag indicates the maximum delay, in seconds, among the shards\. 

## Adding a Region to a global datastore<a name="Redis-Global-Datastores-Console-Create-Secondary"></a>

You can add up to one additional AWS Region to an existing global datastore\. In this scenario, you are creating a read\-only cluster in a separate AWS Region that receives automatic and asynchronous updates from the primary cluster\.

**To add an AWS Region to a global datastore**

1. Sign in to the AWS Management Console and open the ElastiCache console at [ https://console\.aws\.amazon\.com/elasticache/](https://console.aws.amazon.com/elasticache/)\.

1. On the navigation pane, choose **Global Datastore**, and then for **Global Datastore Name** choose a global datastore\.

1. Choose **Add Region**, and choose the AWS Region where the secondary cluster is to reside\.

1. Under **Redis settings**, enter a value for **Name** and, optionally, for **Description** for the cluster\.

1. Keep the following options as they are\. They're prepopulated to match the primary cluster configuration, and you can't change them\.
   + Engine version
   + Instance type
   + Node type
   + Number of shards
   + Parameter group
**Note**  
ElastiCache autogenerates a new parameter group from values of the provided parameter group and applies the new parameter group to the cluster\. Use this new parameter group to modify parameters on a global datastore\. Each autogenerated parameter group is associated with one and only one cluster and, therefore, only one global datastore\.
   + Encryption at rest
**Note**  
You can supply a different encryption key by choosing **Customer Managed Customer Master Key** and choosing the key\.
   + Encryption in transit
   + Redis AUTH

1. \(Optional\) Update the remaining secondary cluster settings\. These are prepopulated with the same values as the primary cluster, but you can update them to meet specific requirements for that cluster:
   + Port
   + Number of replicas
   + Subnet group
   + Preferred Availability Zone\(s\)
   + Security groups
   + Customer Managed Customer Master Key\) 
   + Redis AUTH Token
   + Enable automatic backups
   + Backup retention period
   + Backup window
   + Maintenance window
   + Topic for SNS notification

1. Choose **Add**\.

## Modifying a global datastore<a name="Redis-Global-Datastores-Console-Modify-Regional-Clusters"></a>

You can modify properties of regional clusters\. Only one modify operation can be in progress on a global datastore, with the exception of promoting a secondary cluster to primary\. For more information, see [Promoting the secondary cluster to primary](#Redis-Global-Datastores-Console-Promote-Secondary)\.

**To modify a global datastore**

1. Sign in to the AWS Management Console and open the ElastiCache console at [ https://console\.aws\.amazon\.com/elasticache/](https://console.aws.amazon.com/elasticache/)\.

1. On the navigation pane, choose **Global Datastore**, and then for **Global Datastore Name**, choose a global datastore\.

1. Choose **Modify** and choose among the following options:
   + **Modify description** – Update the description of the global datastore
   + **Modify engine version** – Only Redis engine version 5\.0\.6 or later is available\.
   + **Modify node type** – Scale regional clusters both vertically \(scaling up and down\) and horizontally \(scaling in and out\)\. Options include the R5 and M5 node families\. For more information on node types, see [Supported node types](CacheNodes.SupportedTypes.md)\.
   + **Modify Automatic Failover** – Enable or disable Automatic Failover\. When you enable failover and primary nodes in regional clusters shut down unexpectedly, ElastiCache fails over to one of the regional replicas\. For more information, see [Auto failover](https://docs.aws.amazon.com/AmazonElastiCache/latest/red-ug/AutoFailover.html)\.

   For Redis clusters with cluster\-mode enabled:
   + **Add shards** – Enter the number of shards to add and optionally specify one or more Availability Zones\.
   + **Delete shards** – Choose shards to be deleted in each AWS Region\.
   + **Rebalance shards** – Rebalance the slot distribution to ensure uniform distribution across existing shards in the cluster\. 

To modify a global datastore's parameters, modify the parameter group of any member cluster for the global datastore\. ElastiCache applies this change to all clusters within that global datastore automatically\. To modify the parameter group of that cluster, use the Redis console or the [ModifyCacheCluster](https://docs.aws.amazon.com/AmazonElastiCache/latest/APIReference/API_ModifyCacheCluster.html) API operation\. For more information, see [Modifying a parameter group](ParameterGroups.Modifying.md)\. When you modify the parameter group of any cluster contained within a global datastore, it is applied to all the clusters within that global datastore\.

To reset an entire parameter group or specific parameters, use the [ResetCacheParameterGroup](https://docs.aws.amazon.com/AmazonElastiCache/latest/APIReference/API_ResetCacheParameterGroup.html) API operation\.

## Promoting the secondary cluster to primary<a name="Redis-Global-Datastores-Console-Promote-Secondary"></a>

If the primary cluster or AWS Region becomes unavailable of is experiencing performance issues, you can promote a secondary cluster to primary\. Promotion is allowed anytime, even if other modifications are in progress\. You can also issue multiple promotions in parallel and the global datastore resolves to one primary eventually\. If you promote multiple secondary clusters simultaneously, ElastiCache for Redis doesn't guarantee which one ultimately resolves to primary\.

**To promote a secondary cluster to primary**

1. Sign in to the AWS Management Console and open the ElastiCache console at [ https://console\.aws\.amazon\.com/elasticache/](https://console.aws.amazon.com/elasticache/)\.

1. On the navigation pane, choose **Global Datastore** under **Redis**\.

1. Choose the global datastore name to view the details\.

1. Choose the **Secondary** cluster\.

1. Choose **Promote to primary**\.

   You're then prompted to confirm your decision with the following warning: ` Promoting a region to primary will make the cluster in this region as read/writable. Are you sure you want to promote the secondary cluster to primary?`

   `The current primary cluster in primary region will become secondary and will stop accepting writes after this operation completes. Please ensure you update your application stack to direct traffic to the new primary region.`

1. Choose **Confirm** if you want to continue the promotion or **Cancel** if you don't\.

If you choose to confirm, your global datastore moves to a **Modifying ** state and is unavailable until the promotion is complete\.

## Removing a Region from a global datastore<a name="Redis-Global-Datastore-Console-Remove-Region"></a>

You can remove an AWS Region from a global datastore by using the following procedure\.

**To remove an AWS Region from a global datastore**

1. Sign in to the AWS Management Console and open the ElastiCache console at [ https://console\.aws\.amazon\.com/elasticache/](https://console.aws.amazon.com/elasticache/)\.

1. On the navigation pane, choose **Global Datastore** under **Redis**\.

1. Choose a global datastore\.

1. Choose the **Region** you want to remove\.

1. Choose **Remove region**\.
**Note**  
This option is only available for secondary clusters\. 

   You're then be prompted to confirm your decision with the following warning: ` Removing the region will remove your only available cross region replica for the primary cluster. Your primary cluster will no longer be set up for disaster recovery and improved read latency in remote region. Are you sure you want to remove the selected region from the global datastore?`

1. Choose **Confirm** if you want to continue the promotion or **Cancel** if you don't\.

If you choose confirm, the AWS Region is removed and the secondary cluster no longer receives replication updates\.

## Deleting a global datastore<a name="Redis-Global-Datastores-Console-Delete-GlobalDatastore"></a>

To delete a global datastore, first remove all secondary clusters\. For more information, see [Removing a Region from a global datastore](#Redis-Global-Datastore-Console-Remove-Region)\. Doing this leaves the global datastore in **primary\-only** status\. 

**To delete a global datastore**

1. Sign in to the AWS Management Console and open the ElastiCache console at [ https://console\.aws\.amazon\.com/elasticache/](https://console.aws.amazon.com/elasticache/)\.

1. On the navigation pane, choose **Global Datastore** under **Redis**\.

1. Under **Global Datastore Name** choose the global datastore you want to delete and then choose **Delete**\.

   You're then be prompted to confirm your decision with the following warning: `Are you sure you want to delete this Global Datastore?`

1. Choose **Delete**\.

The global datastore transitions to **Deleting** status\.