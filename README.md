# 📊 **Azure Database Services Guide: SQL Server on VM, Azure SQL, Elastic Pools, Managed Instance, Geo-Replication, & Pricing**

---

## 📖 **Understanding Database as a Service (DBaaS)**

* **DBaaS** is a cloud service model providing database management without needing infrastructure setup.
* Managed by the cloud provider: scalability, patching, backups handled by Azure.
* Azure DBaaS options:

  * **Azure SQL Database (PaaS)**
  * **Azure SQL Managed Instance (MI)**
  * **SQL Server on Azure Virtual Machine (IaaS)**

---

## ⚡ **Difference: SQL Server on Azure VM vs Azure SQL Database**

| Feature               | SQL Server on Azure VM                | Azure SQL Database (PaaS)                   |
| --------------------- | ------------------------------------- | ------------------------------------------- |
| **Service Model**     | IaaS (Infrastructure as a Service)    | PaaS (Platform as a Service)                |
| **Control**           | Full OS and SQL Server control        | Limited (Only DB level control)             |
| **Maintenance**       | You manage patching, backups, updates | Microsoft handles backups, patching         |
| **Use Case**          | Legacy apps requiring OS-level access | Cloud-native apps with minimal admin effort |
| **Scaling**           | Manual (VM resize)                    | Auto-scale, Elastic Pools                   |
| **High Availability** | You configure AlwaysOn AG, Clusters   | Built-in with SLA                           |

---

## 🗄️ **Azure SQL Database Types**

* **Single Database (Isolated resources)**
* **Elastic Pool (Share resources across DBs)**
* **Managed Instance (Full SQL Server engine compatibility)**

---

## 🏊 **SQL Elastic Pools**

* Shared resource model for multiple SQL databases.
* Best for SaaS apps with unpredictable usage per DB.
* Auto-distributes performance (DTUs/vCores) across pooled databases.

---

## 🏢 **Azure SQL Managed Instance**

* Close to **full SQL Server compatibility**.
* Supports SQL Agent, Linked Servers, SSIS.
* VNET support for isolation and better security.
* Lift-and-shift migrations (minimal changes).

---

## 🔄 **Backups in Azure SQL**

* Automated backups (7-35 days retention).
* Point-in-Time Restore (PITR).
* Long-Term Retention (LTR) for compliance.

**Azure CLI Example**:

```bash
az sql db ltr-policy set \
  --resource-group MyResourceGroup \
  --server MySqlServer \
  --database MyDatabase \
  --weekly-retention P12W
```

---

## 🌐 **Multi-region Setup & Geo-Replication**

* **Active Geo-Replication**: Replicate Azure SQL DB to different regions.
* Supports manual failover in disaster recovery.
* Up to 4 readable secondary databases.

**Azure CLI Example**:

```bash
az sql db replica create \
  --name MyDatabase \
  --resource-group MyResourceGroup \
  --server MySqlServer \
  --partner-server MySqlServerSecondary \
  --partner-resource-group MyResourceGroupSecondary
```

---

## 🏢 **Azure Synapse Analytics (Azure Data Warehouse)**

### Steps to Create Azure Synapse (Data Warehouse):

1. **Login to Azure Portal**
2. **Create a Resource Group**:

   ```bash
   az group create --name MySynapseRG --location eastus
   ```
3. **Create Synapse Workspace**:

   ```bash
   az synapse workspace create \
     --name MySynapseWS \
     --resource-group MySynapseRG \
     --storage-account MyStorageAccount \
     --file-system MyFileSystem \
     --sql-admin-login-user adminuser \
     --sql-admin-login-password strongpassword123
   ```
4. **Create SQL Pool (Data Warehouse)**:

   ```bash
   az synapse sql pool create \
     --name MySQLDW \
     --workspace-name MySynapseWS \
     --performance-level DW100c
   ```

### Connect via SSMS:

1. Open **SQL Server Management Studio (SSMS)**.
2. Connect using **Synapse SQL Pool endpoint**.
3. Authenticate with admin username & password.

---

## 💰 **Azure SQL Pricing Models**

* **DTU-based Model (Basic, Standard, Premium)**

  * Combines CPU, Memory, IO into a single unit.
* **vCore-based Model (General Purpose, Business Critical, Hyperscale)**

  * More flexibility, predictable performance.
  * Pay for compute & storage separately.
* **Elastic Pools**: Shared pricing for group of databases.
* **Managed Instance Pricing**: Reserved capacity discounts available.

**Azure Pricing Calculator**: [https://azure.microsoft.com/en-us/pricing/calculator/](https://azure.microsoft.com/en-us/pricing/calculator/)

---

## 🛠️ **Azure CLI Key Commands**

```bash
# Create Azure SQL Server
az sql server create \
  --name my-sql-server \
  --resource-group MyResourceGroup \
  --location eastus \
  --admin-user adminuser \
  --admin-password StrongP@ssw0rd

# Create Azure SQL Database
az sql db create \
  --resource-group MyResourceGroup \
  --server my-sql-server \
  --name my-database \
  --service-objective S1
```

---

Here’s an **Updated Azure Database Services Guide** with **More Azure CLI Commands** added for SQL Firewalls, Users, Private Endpoints, and Automation Examples. After this, I can structure it into a **Full README.md layout** if you want.

---

# 📊 **Azure Database Services Guide: SQL Server on VM, Azure SQL, Elastic Pools, Managed Instance, Geo-Replication, & Pricing**

---

## 🔐 **SQL Server Firewall Rules (Allow Azure Services & IPs)**

```bash
# Allow Azure Services to access SQL Server
az sql server firewall-rule create \
  --resource-group MyResourceGroup \
  --server my-sql-server \
  --name AllowAzureServices \
  --start-ip-address 0.0.0.0 \
  --end-ip-address 0.0.0.0

# Add Client IP to SQL Server firewall
az sql server firewall-rule create \
  --resource-group MyResourceGroup \
  --server my-sql-server \
  --name AllowMyIP \
  --start-ip-address <YOUR_PUBLIC_IP> \
  --end-ip-address <YOUR_PUBLIC_IP>
```

---

## 👤 **Create SQL Database Users & Roles (via CLI + SQLCMD)**

```bash
# Login to Azure SQL DB using sqlcmd
sqlcmd -S my-sql-server.database.windows.net -U adminuser -P StrongP@ssw0rd

# Inside SQLCMD - Create User & Grant Permissions
CREATE USER appuser WITH PASSWORD = 'AppUser@123';
ALTER ROLE db_datareader ADD MEMBER appuser;
ALTER ROLE db_datawriter ADD MEMBER appuser;
GO
```

---

## 🔒 **Private Endpoint for Azure SQL**

```bash
az network private-endpoint create \
  --name MySqlPrivateEndpoint \
  --resource-group MyResourceGroup \
  --vnet-name MyVNet \
  --subnet MySubnet \
  --private-connection-resource-id /subscriptions/<sub-id>/resourceGroups/MyResourceGroup/providers/Microsoft.Sql/servers/my-sql-server \
  --group-id sqlServer \
  --connection-name MySqlPrivateConnection
```

---

## 🔄 **Automate Geo-Replication Failover**

```bash
az sql db replica set-primary \
  --name MyDatabase \
  --resource-group MyResourceGroupSecondary \
  --server MySqlServerSecondary
```

---

## 🏷️ **Tagging Resources for Cost Management**

```bash
az tag create --name "Environment" --value "Production"

az resource tag \
  --tags Environment=Production Owner=DBTeam \
  --ids $(az sql db show --resource-group MyResourceGroup --server my-sql-server --name my-database --query id -o tsv)
```

---

## 🚧 **Deleting SQL Server & Database (Cleanup)**

```bash
# Delete SQL Database
az sql db delete \
  --resource-group MyResourceGroup \
  --server my-sql-server \
  --name my-database \
  --yes

# Delete SQL Server
az sql server delete \
  --name my-sql-server \
  --resource-group MyResourceGroup \
  --yes
```

---

## 🏗️ **Deploy SQL Resources with ARM Template (via CLI)**

```bash
az deployment group create \
  --resource-group MyResourceGroup \
  --template-file azure-sql-template.json
```

---

## 🗂️ **Export Azure SQL Database (BACPAC to Storage Account)**

```bash
az sql db export \
  --admin-user adminuser \
  --admin-password StrongP@ssw0rd \
  --storage-key-type StorageAccessKey \
  --storage-key <STORAGE_ACCOUNT_KEY> \
  --storage-uri https://<STORAGE_ACCOUNT>.blob.core.windows.net/<CONTAINER>/mydatabase.bacpac \
  --name my-database \
  --server my-sql-server \
  --resource-group MyResourceGroup
```

---

## 🛠️ **Azure PowerShell (Optional Commands)**

```powershell
# Install Azure SQL Module
Install-Module -Name Az.Sql

# Create SQL Database using PowerShell
New-AzSqlDatabase -ResourceGroupName MyResourceGroup -ServerName my-sql-server -DatabaseName my-database -RequestedServiceObjectiveName S1
```

---

## 🚀 **Summary Steps Flow**

1. Understand DBaaS model (Azure SQL, Managed Instance, SQL on VM)
2. Decide deployment type (Single DB, Elastic Pool, MI)
3. Provision SQL Server, Database, Elastic Pool (CLI, Portal, ARM/Bicep/Terraform)
4. Configure Firewall Rules, Private Endpoints, Security Policies
5. Enable Automated Backups & Geo-Replication
6. Create Users/Roles & Assign Permissions
7. Monitor Performance, Setup Alerts & Diagnostic Logs
8. Export/Import Database (BACPAC)
9. Estimate Pricing via Azure Calculator (vCore or DTU)
10. Automate Deployment & Configuration using CLI, ARM, Bicep, Terraform pipelines

---
