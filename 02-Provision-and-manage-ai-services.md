# Provision and manage ai-service

[Learning Path](https://learn.microsoft.com/en-us/training/modules/create-manage-cognitive-services/1-introduction)

Learning Achievement:
- Provision Azure AI service resources in an Azure subscription.
- Identify endpoints, keys, and locations required to consume an Azure AI service resource.
- Use a REST API to consume an Azure AI service.
- Use an SDK to consume an Azure AI service.

**Provision Azure AI service resources in an Azure subscription**
- Create appropriate resources inside subscription

multi-service: enables you to consumer multiple AI-service at a single endpoint and single billing
single-service: single AI-service provisioned at individual endpoints

**Identify endpoints, keys**
Consumption of service through endpoint requires:
- endpoint uri: HTTP request where REST can access endpoint, typically through SDK
- subscription key: 2 keys created when provision AI service. can use either
- resouce location: data center

### Use a REST API

Definition: An Azure service that client applications can use to consume services

Any programming language can call to POST, GET, and PUT

### Use an SDK

Definition: Software development kit that enables a user to build using code-specific libraries

## Secure Ai Services
Learning Achievement:
- consider authentication for ai services
- manage network security for ai services

**Consder authentication**
Definition: use is restricted by subscription keys. primary consideration

regenerate keys: protect against oversharing CLI command: az cognitiveservices account keys regenerate

Key Vault: access given to security principals. User does some action, app retrieves the key from key vault, app uses key to access cog services

**Implement network security**
Restrict unauthorized use. first line of defense is limit what a user can see

### apply network access restrictions
Definition: Azure AI Services are accessible from all networks. Some services resources can be configured to restrict

**Manage azure ai services security**

## Monitor Azure Ai Services
Learning Achievement:
- monitor azure ai services costs
- create alerts
- view metrics
- manage diagnostic logging

**monitor cost**
- plan costs: calculator that enables you to estimate based on pricing tier, region, usage metrics and support. can be downloaded/saved
-  view costs: dashboard to view costs

**create alerts**
- alert rules: configurable based on events or metric thresholds
- create: Alerts tab add rule (1) scope: resource to monitor, (2) condition: based on signal type, (3) optional: send email, (4) alert rule details: e.g. name of alert

**View metrics**
- view in portal: pages available by resource
- add metrics: can add up to 100 named dashboards

**Manage diagnostic logging**
create resources before configuring diagnostic logging for resource. create storage in same region
- create resources for logging: need a destination (event hubs), most likely to use Azure Log Analytics: query and visualize log data or Azure Storage: data log archives

**configure diagnostic settings**

## Deploy AI Services in Containers
Learning Achievements:
- understand containers
- Provision AI service container

**Understand containers**
- service requires env that has hardware, operating system, runtime components
- Container definition: hosts app or service and the runtime components to run it
- Benefits: Containers are portable and easier to consolidate multiple apps that have different configurations
- use: pull image from registry, deploy container on host, specify required config settings

**Use AI service containers**
- container images live in microsoft container registry
- 3 required activities for deploy:
- (1) services API is downloaded to local host (Docker Server, Azure container instance, azure kubernetes service);
- (2) client apps submit data to endpoint of container service
- (3) usage metrics sent for container service to calc billing

**Azure container images**
- each container provides subset of AI services
- [List of Azure containers](https://learn.microsoft.com/en-us/azure/ai-services/cognitive-services-container-support)

**Azure container config**
3 required settings
- APIKey: Key from AI service used for billing
- Billing: Endpoint URI for deployed service used for billing
- Eula: Value of accept to state acceptance of container license

**Consume AI services from container**
- apps consumer container endpoint not Services endpoint when used
