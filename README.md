# TraceGuardian
Azure Cloud Security Project - Monitoring and Sentinel Integration without RBAC

## 🧠 Overview  
TraceGuardian is a cloud-native security blueprint designed to demonstrate real-world Azure monitoring and protection techniques — using only Azure free-tier services and without Role-Based Access Control (RBAC).

---

## 📦 Project Architecture

- **Azure App Service (Linux, Node.js)**
- **Azure SQL Database**
- **Azure Key Vault**
- **Log Analytics Workspace**
- **Microsoft Sentinel**
- **Azure Monitor Workbooks**

---

## 🔐 Security Features

- Managed Identity (App Service → Key Vault)
- Secrets stored in Key Vault (no inline secrets)
- Diagnostic logging enabled across all services
- Real-time monitoring with Log Analytics and Sentinel
- Custom Workbook for visualization

---

## 🚀 Deployment Steps

1. **Create Resources**
   - Resource Group: `TraceGuardianRG`
   - Key Vault: `tg-kv-kelvin`
   - App Service: `trace-weba`
   - SQL DB: `trace-sqldb`
   - Log Analytics Workspace: `Tglaw`

2. **Secure with Key Vault**
   - Store `SQLCONNSTRING` and `SQLUSERPASS`
   - Enable App Service Managed Identity
   - Add Key Vault references in Application Settings

3. **Deploy App via Kudu**
   - Access Kudu Console
   - Upload Node.js files to `/home/site/wwwroot`
   - Restart app to reflect changes

4. **Enable Diagnostics**
   - App Service: All logs to Log Analytics
   - Key Vault: Enable `AuditEvent`
   - Validate with Log Analytics queries

5. **Integrate Sentinel**
   - Create Sentinel workspace in `Tglaw`
   - Add Key Vault and App Service connectors

6. **Create Workbook**
   - Use “Azure Monitor Workbook”
   - Build charts for access logs, HTTP status codes, failures, etc.

---

## 📊 Sample Queries

```kusto
// Key Vault Audit Events
AzureDiagnostics
| where Category == "AuditEvent"
| where ResourceType == "VAULTS"
| project TimeGenerated, Identity, OperationName, ResultType

// App Service Access Logs
AppServiceHTTPLogs
| summarize count() by cs-status, bin(TimeGenerated, 1h)
