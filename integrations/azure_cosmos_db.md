---
title: Microsoft Azure Cosmos DB Integration
tags: [integrations list]
permalink: azure_cosmos_db.html
summary: Learn about the Wavefront Microsoft Azure Cosmos DB Integration.
---
## Microsoft Azure Integration

The Microsoft Azure integration enables monitoring Azure with Wavefront and offers pre-defined dashboards and alert conditions. 

### Metrics Configuration
Wavefront ingests Microsoft Azure metrics using the Azure Monitor APIs. For details on the metrics that the API supports, see the [documentation](https://docs.microsoft.com/en-us/azure/monitoring-and-diagnostics/monitoring-supported-metrics).

Metrics originating from Microsoft Azure are prefixed with `azure.` within Wavefront. After you set up the integration, you can browse the available metrics in the metrics browser. 

### Dashboards

Wavefront provides Microsoft Azure dashboards for the following services:

- Azure: Application Gateways
- Azure: App Service
- Azure: Container Instances
- Azure: Cosmos DB
- Azure: Event Hub
- Azure: Files
- Azure: Functions
- Azure: HDInsight Cluster
- Azure: Kubernetes Services
- Azure: Load Balancers
- Azure: Redis Caches
- Azure: Storage Accounts
- Azure: SQL Databases
- Azure: SQL Datawarehouse
- Azure: Summary
- Azure: Virtual Machine
- Azure: Virtual Machine Scale Set

Here's a preview of the Virtual Machine dashboard:
{% include image.md src="images/azure-overview.png" width="80" %}

## Microsoft Azure Integrations



### Adding an Azure Cloud Integration

Adding an Azure cloud integration requires establishing a trust relationship between Azure and Wavefront.

1. Log in to your Wavefront instance.
2. Follow the instructions on the left to establish the trust relationship.

The process first creates an Azure Active Directory application that represents Wavefront inside Azure. Then you retrieve information for that application, and paste it into the form on the far left to complete the trust setup.





undefined


## Metrics

See [Azure documentation](https://docs.microsoft.com/en-us/azure/azure-monitor/platform/metrics-supported) for Metrics descriptions.

|Metric Name|Description|
| :--- | :--- |
|azure.documentdb.databaseaccounts.availablestorage.*|Statistics: average, count, maximum, minimum|
|azure.documentdb.databaseaccounts.cassandraconnectionclosures.*|Statistics: average, count, maximum, minimum|
|azure.documentdb.databaseaccounts.cassandrarequestcharges.*|Statistics: average, count, maximum, minimum|
|azure.documentdb.databaseaccounts.cassandrarequests.*|Statistics: average, count, maximum, minimum|
|azure.documentdb.databaseaccounts.datausage.*|Statistics: average, count, maximum, minimum|
|azure.documentdb.databaseaccounts.documentcount.*|Statistics: average, count, maximum, minimum|
|azure.documentdb.databaseaccounts.documentquota.*|Statistics: average, count, maximum, minimum|
|azure.documentdb.databaseaccounts.indexusage.*|Statistics: average, count, maximum, minimum|
|azure.documentdb.databaseaccounts.metadatarequests.*|Statistics: average, count, maximum, minimum|
|azure.documentdb.databaseaccounts.mongorequestcharge.*|Statistics: average, count, maximum, minimum|
|azure.documentdb.databaseaccounts.mongorequests.*|Statistics: average, count, maximum, minimum|
|azure.documentdb.databaseaccounts.normalizedruconsumption.*|Statistics: average, count, maximum, minimum|
|azure.documentdb.databaseaccounts.provisionedthroughput.*|Statistics: average, count, maximum, minimum|
|azure.documentdb.databaseaccounts.serversidelatency.*|Statistics: average, count, maximum, minimum|
|azure.documentdb.databaseaccounts.serviceavailability.*|Statistics: average, count, maximum, minimum|
|azure.documentdb.databaseaccounts.totalrequests.*|Statistics: average, count, maximum, minimum|
|azure.documentdb.databaseaccounts.totalrequestunits.*|Statistics: average, count, maximum, minimum|
