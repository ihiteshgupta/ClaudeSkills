---
name: azure-architect
description: Expert in Microsoft Azure architecture, services, and best practices. Use for Azure infrastructure design, deployment, and cloud-native applications.
allowed-tools: Read, Write, Edit, Grep, Glob, Bash
---

# Azure Solutions Architect Expert

## Purpose
Provide expert guidance on Microsoft Azure architecture, service selection, deployment strategies, and cloud-native application design on Azure.

## When to Use This Skill
- Designing Azure infrastructure
- Selecting appropriate Azure services
- Migrating applications to Azure
- Implementing Azure Functions (serverless)
- Setting up Azure DevOps pipelines
- AKS (Azure Kubernetes Service) deployments
- Cost optimization
- Hybrid cloud scenarios

## Core Azure Services

### Compute
- **Virtual Machines** - IaaS VMs
- **Azure Functions** - Serverless functions
- **App Service** - PaaS web apps
- **AKS** - Azure Kubernetes Service
- **Container Instances** - Serverless containers
- **Azure Batch** - Large-scale computing

### Storage
- **Blob Storage** - Object storage
- **Files** - File shares
- **Queue Storage** - Message queuing
- **Table Storage** - NoSQL key-value
- **Disk Storage** - VM disks

### Database
- **Azure SQL Database** - Managed SQL Server
- **Cosmos DB** - Multi-model NoSQL
- **Database for PostgreSQL/MySQL** - Managed databases
- **Azure Cache for Redis** - In-memory cache
- **Azure Synapse** - Analytics service

### Networking
- **Virtual Network** - Network isolation
- **Application Gateway** - Layer 7 load balancer
- **Load Balancer** - Layer 4 load balancer
- **Traffic Manager** - DNS-based routing
- **Front Door** - Global load balancer
- **VPN Gateway** - Site-to-site VPN
- **ExpressRoute** - Dedicated connection

### Security & Identity
- **Azure AD** - Identity management
- **Key Vault** - Secrets management
- **Security Center** - Security management
- **Sentinel** - SIEM solution

### DevOps & Monitoring
- **Azure DevOps** - CI/CD platform
- **Azure Monitor** - Monitoring and logging
- **Application Insights** - APM
- **Log Analytics** - Log aggregation

## Architecture Patterns

### 1. Web Application with Azure App Service
```
Architecture:
┌──────────────────────────────────────────┐
│   Azure Front Door + CDN                 │
└────────────┬─────────────────────────────┘
             │
┌────────────▼─────────────────────────────┐
│   Application Gateway + WAF              │
└────────────┬─────────────────────────────┘
             │
┌────────────▼─────────────────────────────┐
│   App Service (Web Apps)                 │
│   Auto-scaling enabled                   │
└────────────┬─────────────────────────────┘
             │
┌────────────▼─────────────────────────────┐
│   Azure SQL Database + Redis Cache       │
└──────────────────────────────────────────┘

Bicep Template:
```bicep
param location string = resourceGroup().location
param appName string = 'myapp'

// App Service Plan
resource appServicePlan 'Microsoft.Web/serverfarms@2022-03-01' = {
  name: '${appName}-plan'
  location: location
  sku: {
    name: 'P1v2'
    tier: 'PremiumV2'
    capacity: 2
  }
  kind: 'linux'
  properties: {
    reserved: true
  }
}

// Web App
resource webApp 'Microsoft.Web/sites@2022-03-01' = {
  name: '${appName}-web'
  location: location
  identity: {
    type: 'SystemAssigned'
  }
  properties: {
    serverFarmId: appServicePlan.id
    httpsOnly: true
    siteConfig: {
      linuxFxVersion: 'NODE|18-lts'
      alwaysOn: true
      ftpsState: 'Disabled'
      minTlsVersion: '1.2'
      http20Enabled: true
      appSettings: [
        {
          name: 'DATABASE_URL'
          value: '@Microsoft.KeyVault(SecretUri=${keyVault.properties.vaultUri}secrets/database-url/)'
        }
        {
          name: 'REDIS_HOST'
          value: redisCache.properties.hostName
        }
      ]
    }
  }
}

// Autoscale settings
resource autoScale 'Microsoft.Insights/autoscalesettings@2022-10-01' = {
  name: '${appName}-autoscale'
  location: location
  properties: {
    enabled: true
    targetResourceUri: appServicePlan.id
    profiles: [
      {
        name: 'Auto scale condition'
        capacity: {
          minimum: '2'
          maximum: '10'
          default: '2'
        }
        rules: [
          {
            metricTrigger: {
              metricName: 'CpuPercentage'
              metricResourceUri: appServicePlan.id
              timeGrain: 'PT1M'
              statistic: 'Average'
              timeWindow: 'PT5M'
              timeAggregation: 'Average'
              operator: 'GreaterThan'
              threshold: 70
            }
            scaleAction: {
              direction: 'Increase'
              type: 'ChangeCount'
              value: '1'
              cooldown: 'PT5M'
            }
          }
        ]
      }
    ]
  }
}

// Azure SQL Database
resource sqlServer 'Microsoft.Sql/servers@2022-05-01-preview' = {
  name: '${appName}-sql'
  location: location
  properties: {
    administratorLogin: 'sqladmin'
    administratorLoginPassword: keyVault.getSecret('sql-admin-password')
    version: '12.0'
    minimalTlsVersion: '1.2'
  }
}

resource sqlDatabase 'Microsoft.Sql/servers/databases@2022-05-01-preview' = {
  parent: sqlServer
  name: 'maindb'
  location: location
  sku: {
    name: 'S1'
    tier: 'Standard'
  }
  properties: {
    collation: 'SQL_Latin1_General_CP1_CI_AS'
    maxSizeBytes: 268435456000
    catalogCollation: 'SQL_Latin1_General_CP1_CI_AS'
  }
}

// Redis Cache
resource redisCache 'Microsoft.Cache/redis@2022-06-01' = {
  name: '${appName}-redis'
  location: location
  properties: {
    sku: {
      name: 'Standard'
      family: 'C'
      capacity: 1
    }
    enableNonSslPort: false
    minimumTlsVersion: '1.2'
  }
}

// Key Vault
resource keyVault 'Microsoft.KeyVault/vaults@2022-07-01' = {
  name: '${appName}-kv'
  location: location
  properties: {
    tenantId: subscription().tenantId
    sku: {
      family: 'A'
      name: 'standard'
    }
    accessPolicies: [
      {
        tenantId: subscription().tenantId
        objectId: webApp.identity.principalId
        permissions: {
          secrets: ['get', 'list']
        }
      }
    ]
  }
}
```

### 2. Microservices on AKS
```
Architecture:
┌──────────────────────────────────────────┐
│   Azure Front Door                       │
└────────────┬─────────────────────────────┘
             │
┌────────────▼─────────────────────────────┐
│   Application Gateway Ingress            │
└────────────┬─────────────────────────────┘
             │
┌────────────▼─────────────────────────────┐
│   AKS Cluster                            │
│   ┌──────────┐  ┌──────────┐           │
│   │Service A │  │Service B │           │
│   └──────────┘  └──────────┘           │
└────────────┬─────────────────────────────┘
             │
┌────────────▼─────────────────────────────┐
│   Cosmos DB + Azure SQL + Redis          │
└──────────────────────────────────────────┘

AKS Terraform:
```hcl
resource "azurerm_kubernetes_cluster" "aks" {
  name                = "myaks"
  location            = azurerm_resource_group.rg.location
  resource_group_name = azurerm_resource_group.rg.name
  dns_prefix          = "myaks"
  kubernetes_version  = "1.27"

  default_node_pool {
    name                = "default"
    node_count          = 3
    vm_size             = "Standard_D2s_v3"
    enable_auto_scaling = true
    min_count           = 3
    max_count           = 10
    os_disk_size_gb     = 30
    type                = "VirtualMachineScaleSets"
    vnet_subnet_id      = azurerm_subnet.aks.id
  }

  identity {
    type = "SystemAssigned"
  }

  network_profile {
    network_plugin    = "azure"
    network_policy    = "calico"
    load_balancer_sku = "standard"
    service_cidr      = "10.2.0.0/24"
    dns_service_ip    = "10.2.0.10"
  }

  addon_profile {
    oms_agent {
      enabled                    = true
      log_analytics_workspace_id = azurerm_log_analytics_workspace.aks.id
    }

    azure_policy {
      enabled = true
    }

    ingress_application_gateway {
      enabled   = true
      gateway_id = azurerm_application_gateway.appgw.id
    }
  }

  role_based_access_control_enabled = true

  azure_active_directory_role_based_access_control {
    managed                = true
    admin_group_object_ids = [var.admin_group_id]
    azure_rbac_enabled     = true
  }

  tags = {
    Environment = "Production"
  }
}

resource "azurerm_kubernetes_cluster_node_pool" "compute" {
  name                  = "compute"
  kubernetes_cluster_id = azurerm_kubernetes_cluster.aks.id
  vm_size               = "Standard_D4s_v3"
  enable_auto_scaling   = true
  min_count             = 1
  max_count             = 5
  os_disk_size_gb       = 30

  node_labels = {
    "workload" = "compute-intensive"
  }

  node_taints = [
    "workload=compute:NoSchedule"
  ]
}
```

### 3. Serverless with Azure Functions
```
Architecture:
┌──────────────────────────────────────────┐
│   API Management                         │
└────────────┬─────────────────────────────┘
             │
┌────────────▼─────────────────────────────┐
│   Azure Functions                        │
│   (Consumption/Premium Plan)             │
└────────────┬─────────────────────────────┘
             │
┌────────────▼─────────────────────────────┐
│   Cosmos DB + Service Bus                │
└──────────────────────────────────────────┘

Azure Function (TypeScript):
```typescript
import { AzureFunction, Context, HttpRequest } from "@azure/functions";
import { CosmosClient } from "@azure/cosmos";

const cosmosClient = new CosmosClient(process.env.COSMOS_CONNECTION_STRING);
const database = cosmosClient.database("mydb");
const container = database.container("items");

const httpTrigger: AzureFunction = async function (
  context: Context,
  req: HttpRequest
): Promise<void> {
  context.log("HTTP trigger function processed a request.");

  const { id } = req.params;

  try {
    if (req.method === "GET") {
      const { resource: item } = await container.item(id, id).read();
      context.res = {
        status: 200,
        body: item,
      };
    } else if (req.method === "POST") {
      const item = req.body;
      const { resource: created } = await container.items.create(item);
      context.res = {
        status: 201,
        body: created,
      };
    }
  } catch (error) {
    context.log.error("Error:", error);
    context.res = {
      status: 500,
      body: { error: "Internal server error" },
    };
  }
};

export default httpTrigger;
```

Function Configuration:
```json
{
  "bindings": [
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": ["get", "post"],
      "route": "items/{id?}"
    },
    {
      "type": "http",
      "direction": "out",
      "name": "res"
    }
  ]
}
```

## Security Best Practices

### 1. Azure AD Integration
```bicep
resource webApp 'Microsoft.Web/sites@2022-03-01' = {
  name: 'myapp'
  properties: {
    siteConfig: {
      azureStorageAccounts: {}
    }
  }
  identity: {
    type: 'SystemAssigned'
  }
}

resource keyVaultAccessPolicy 'Microsoft.KeyVault/vaults/accessPolicies@2022-07-01' = {
  parent: keyVault
  name: 'add'
  properties: {
    accessPolicies: [
      {
        tenantId: subscription().tenantId
        objectId: webApp.identity.principalId
        permissions: {
          secrets: ['get']
        }
      }
    ]
  }
}
```

### 2. Network Security
- Use Virtual Networks for isolation
- Network Security Groups (NSGs)
- Application Security Groups (ASGs)
- Azure Firewall for centralized protection
- Private Endpoints for PaaS services

### 3. Azure Policy
```json
{
  "properties": {
    "displayName": "Require HTTPS for web apps",
    "policyType": "Custom",
    "mode": "All",
    "parameters": {},
    "policyRule": {
      "if": {
        "allOf": [
          {
            "field": "type",
            "equals": "Microsoft.Web/sites"
          },
          {
            "field": "Microsoft.Web/sites/httpsOnly",
            "equals": "false"
          }
        ]
      },
      "then": {
        "effect": "deny"
      }
    }
  }
}
```

## Cost Optimization

1. **Reserved Instances** - Up to 72% savings
2. **Azure Hybrid Benefit** - Use existing licenses
3. **Spot VMs** - Up to 90% savings
4. **Auto-shutdown** - Stop VMs when not in use
5. **Right-sizing** - Azure Advisor recommendations
6. **Storage tiers** - Hot, Cool, Archive

## Monitoring & Logging

```csharp
using Microsoft.ApplicationInsights;
using Microsoft.ApplicationInsights.DataContracts;

public class MyService
{
    private readonly TelemetryClient _telemetry;

    public MyService(TelemetryClient telemetry)
    {
        _telemetry = telemetry;
    }

    public async Task ProcessOrder(Order order)
    {
        var operation = _telemetry.StartOperation<RequestTelemetry>("ProcessOrder");

        try
        {
            _telemetry.TrackEvent("OrderProcessing", new Dictionary<string, string>
            {
                { "OrderId", order.Id },
                { "CustomerId", order.CustomerId }
            });

            // Process order
            await _orderRepository.Save(order);

            _telemetry.TrackMetric("OrderValue", order.Total);
            operation.Telemetry.Success = true;
        }
        catch (Exception ex)
        {
            _telemetry.TrackException(ex);
            operation.Telemetry.Success = false;
            throw;
        }
        finally
        {
            _telemetry.StopOperation(operation);
        }
    }
}
```

## DevOps Integration

```yaml
# azure-pipelines.yml
trigger:
  - main

pool:
  vmImage: 'ubuntu-latest'

variables:
  azureSubscription: 'MyAzureSubscription'
  webAppName: 'myapp'

stages:
  - stage: Build
    jobs:
      - job: BuildJob
        steps:
          - task: NodeTool@0
            inputs:
              versionSpec: '18.x'

          - script: |
              npm install
              npm run build
              npm test
            displayName: 'Build and Test'

          - task: PublishBuildArtifacts@1
            inputs:
              PathtoPublish: 'dist'
              ArtifactName: 'drop'

  - stage: Deploy
    dependsOn: Build
    jobs:
      - deployment: DeployWeb
        environment: 'production'
        strategy:
          runOnce:
            deploy:
              steps:
                - task: AzureWebApp@1
                  inputs:
                    azureSubscription: $(azureSubscription)
                    appName: $(webAppName)
                    package: '$(Pipeline.Workspace)/drop'
```

This skill ensures well-architected, secure, and cost-effective Azure solutions.
