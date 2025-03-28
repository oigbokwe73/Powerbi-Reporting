# Powerbi-Reporting

### 📊 Summary: SIP Dashboard Candidate Metrics Review

The **Agency** conducted a comprehensive review of **candidate metrics** proposed for the **System Integration Platform (SIP)** dashboard, to be visualized in **Power BI**. The primary objective was to assess which metrics offer the most actionable insights for monitoring, troubleshooting, and optimizing the SIP pipeline, particularly focusing on data ingestion, transformation, delivery, and error handling.

---

### ✅ Metrics Presented for Review

The following key metric categories were presented for dashboard consideration:

#### 1. **File and Record Processing Metrics**
- **Total Records Received**
- **Total Records Passed Validation**
- **Total Records Accepted**
- **Total Records Failed**
- **Total Records Delivered**
- **Total Records Sent to DLQ**
- **Total Errors Encountered**

#### 2. **Request Performance Metrics**
- **Total Requests**
- **Successful vs. Failed Requests**
- **Failure Rate (%)**
- **Average Response Time**
- **Top Error Codes**

#### 3. **System Utilization Metrics**
- **CPU Usage %**
- **Memory Usage (MB)**
- **Disk I/O Read/Write (MBps)**
- **Network Bandwidth Usage (%)**

#### 4. **Azure Entra-ID & Identity Metrics**
- **User Creation & Group Events**
- **Role Assignment Changes**
- **Identity Protection Risk Detections**
- **High-Risk Sign-ins**
- **Directory Changes**

---

### 🧠 Decision Outcome

After reviewing the proposed metrics and associated visual mockups:

- ✅ **Approved for Dashboard Inclusion**:
  - **End-to-End Record Metrics** (Received, Passed, Accepted, Failed, Delivered, DLQ)
  - **Request Performance** (Success/Fail, Response Time, Error Codes)
  - **System Utilization Trends** (CPU, Memory, I/O)
  - **Azure Identity Events** for security visibility

- ❌ **Deferred for Future Consideration**:
  - Real-time streaming telemetry (pending infrastructure support)
  - Low-frequency administrative audit logs (to be reviewed quarterly)

---

### 🔍 Rationale for Selections

- **Operational Relevance**: Metrics selected provide visibility into SIP data flow and delivery reliability.
- **Actionability**: Highlights areas where failures occur (DLQ, Errors, Failed Records), enabling faster triage.
- **Performance Optimization**: System metrics support root cause analysis for delays or service degradation.
- **Security and Compliance**: Entra-ID metrics bring auditability and access oversight into daily operations.

---

### 📌 Next Steps

- Power BI development team to begin implementing approved metrics.
- Set up scheduled refresh from Azure Monitor, Log Analytics, and DLQ queues.
- Define alert thresholds for high error rates and DLQ spikes.
- Review initial dashboard prototype with business and technical stakeholders.

Let me know if you'd like a visual version of this summary in PowerPoint or if you need help building the first Power BI report!

# **Performance Testing: Power BI Connecting to Data Sources**  

## **Introduction**  
Power BI performance testing is essential to **ensure optimal report loading times, query execution efficiency, and scalability** when connecting to large and complex data sources such as **Azure SQL, Azure Storage, Log Analytics, and ServiceNow**. **Bottlenecks in Power BI performance** typically arise due to **slow queries, inefficient data models, or high-frequency data refreshes**. This guide covers **best practices, testing methodologies, and optimization techniques** for Power BI performance when connecting to various data sources.

---

## **1️⃣ Key Performance Factors in Power BI Connectivity**
When testing Power BI performance, consider these critical factors:

### **1.1 Data Source Type & Query Mode**
✅ **DirectQuery**  
   - Queries **live data** from the data source without storing it in Power BI.  
   - Ideal for **real-time dashboards**, but **slow performance if queries are inefficient**.  

✅ **Import Mode**  
   - **Pre-loads data into Power BI memory (VertiPaq Engine)**.  
   - Fast performance but **requires scheduled refreshes** to stay updated.  

✅ **Hybrid Mode (Composite Models)**  
   - Uses **Import Mode** for frequently accessed data.  
   - Uses **DirectQuery** for real-time data without excessive memory usage.  

### **1.2 Query Complexity & Execution Time**
✅ **Long-running SQL queries** slow down Power BI performance.  
✅ **Nested joins, cross-database queries, and large result sets** impact efficiency.  
✅ **Optimized indexing and partitioning** on the source database improves response time.  

### **1.3 Data Refresh Frequency**
✅ **Frequent refreshes (e.g., every 5 min)** cause performance degradation.  
✅ **Use Incremental Refresh** instead of full dataset refresh.  

### **1.4 Network Latency & Data Transfer Size**
✅ Large data transfers from **Azure SQL, ServiceNow, or APIs** impact Power BI performance.  
✅ **Minimize data load size** with optimized queries and filtering.  

---

## **2️⃣ Performance Testing Scenarios for Power BI**
### **2.1 Query Execution Performance (DirectQuery Mode)**
**Goal:** Measure response time for Power BI when querying **Azure SQL, ServiceNow, or Log Analytics** in **DirectQuery mode**.  

✅ **Testing Steps**  
1. **Run Power BI Performance Analyzer** → Identify long-running queries.  
2. **Execute KQL/SQL queries manually** → Measure execution time.  
3. **Apply query optimizations** (e.g., indexing, query folding).  
4. **Re-run Power BI report & compare results**.  

✅ **KQL Query Test Example (Azure Log Analytics)**
```kql
AzureDiagnostics
| where ResponseTime > 500
| summarize AvgLatency=avg(ResponseTime) by bin(TimeGenerated, 1h)
```

✅ **SQL Query Test Example (Azure SQL)**
```sql
SELECT ServiceName, AVG(ResponseTime) 
FROM Transactions 
WHERE ResponseTime > 500 
GROUP BY ServiceName;
```

---

### **2.2 Data Model Performance (Import Mode)**
**Goal:** Optimize **Power BI import performance** when handling **large datasets** from **Azure SQL, Blob Storage, and Data Lake**.

✅ **Testing Steps**  
1. **Measure dataset load time** for **100K, 500K, 1M records**.  
2. **Enable compression techniques** using **Power BI aggregations**.  
3. **Reduce table size** by removing unused columns.  
4. **Use Power Query Diagnostics** to analyze **query folding behavior**.  

✅ **Optimization Techniques**
- Use **Parquet instead of CSV** in **Azure Storage** (faster query execution).  
- Apply **VertiPaq Analyzer** in Power BI to identify **large tables consuming excessive memory**.  
- **Partition data by date/time** for fast retrieval.  

---

### **2.3 Scheduled Refresh Performance**
**Goal:** Measure how **Power BI handles scheduled refresh** workloads from **Azure SQL, ServiceNow, and Azure Storage**.

✅ **Testing Steps**
1. **Set dataset refresh frequency** to **15 minutes, 30 minutes, and 1 hour**.  
2. **Enable incremental refresh** to reduce refresh times.  
3. **Monitor refresh duration in Power BI Service**.  
4. **Identify bottlenecks in Power BI Gateway performance** (if using on-premise data).  

✅ **Optimization Techniques**
- **Avoid full dataset refreshes**; instead, use **partitioned incremental refresh**.  
- Use **Azure Data Factory** to pre-aggregate data before Power BI queries it.  
- Offload **heavy transformation logic to the source database** instead of Power Query.  

✅ **Power BI Incremental Refresh Configuration**
1. **Enable DateTime Filtering in SQL Query**
   ```sql
   SELECT * FROM Transactions WHERE CreatedAt >= @StartDate AND CreatedAt <= @EndDate;
   ```
2. **Set Incremental Refresh Policy in Power BI**
   - Store 2 years of data.  
   - Refresh **only the last 7 days**.  

---

## **3️⃣ Optimization Techniques for Power BI Performance**
### **3.1 DirectQuery Optimization**
✅ **Optimize SQL Indexing**
   - **Create indexed views** for frequently queried tables.  
   - Example:
     ```sql
     CREATE INDEX idx_latency ON Transactions(ResponseTime);
     ```

✅ **Push Calculations to Database**
   - Perform aggregations at the **SQL level instead of Power BI**.
   - Example:
     ```sql
     SELECT ServiceName, AVG(ResponseTime) FROM Transactions GROUP BY ServiceName;
     ```

✅ **Reduce Data Model Complexity**
   - Avoid unnecessary relationships between tables.  
   - Use **denormalized tables** where possible.  

---

### **3.2 Import Mode Optimization**
✅ **Reduce Dataset Size**
   - Remove unused columns in Power BI Query Editor.  
   - Use **aggregated tables instead of detailed raw data**.  

✅ **Enable Data Compression**
   - **Use Parquet instead of CSV** when reading from **Azure Data Lake**.  

✅ **Optimize DAX Queries**
   - Use **SUMX instead of SUM** for large table calculations.  
   - Example:
     ```DAX
     TotalResponseTime = SUMX(Transactions, Transactions[ResponseTime])
     ```

---

### **3.3 Scheduled Refresh Optimization**
✅ **Enable Power BI Gateway Performance Logging**
   - If using **on-premises SQL Server**, ensure **Power BI Gateway logs refresh times**.

✅ **Use Power Automate to Control Refresh Timing**
   - **Trigger refresh** only **when new data is available**.  

✅ **Reduce API Calls for ServiceNow Data**
   - Fetch **only the latest records**:
     ```
     https://your-instance.service-now.com/api/now/table/incident?sys_updated_on>2025-03-01
     ```

---

## **4️⃣ Performance Benchmarking & Monitoring**
### **4.1 Monitor Power BI Performance Metrics**
✅ **Key Performance Metrics to Track**
| **Metric** | **Description** | **Target Value** |
|------------|----------------|------------------|
| **Query Execution Time** | Time taken for DirectQuery execution | **< 2 sec** |
| **Report Load Time** | Time taken to render a report in Power BI | **< 3 sec** |
| **Scheduled Refresh Duration** | Time taken to refresh dataset in Power BI Service | **< 5 min** |
| **Gateway CPU Usage** | Resource consumption during data refresh | **< 50%** |
| **DAX Query Performance** | Execution time for calculated measures | **< 2 sec** |

✅ **Use Power BI Performance Analyzer**
1. **Open Power BI Desktop**.
2. Navigate to **View → Performance Analyzer**.
3. Click **Start Recording**.
4. **Analyze slow-loading visuals**.

---

## **5️⃣ Conclusion**
✅ **Key Takeaways for Power BI Performance Testing**
- **Use Import Mode for large datasets** (optimize storage & refresh times).  
- **Optimize DirectQuery with indexed views & query folding**.  
- **Enable Incremental Refresh to reduce full dataset loads**.  
- **Use Power BI Performance Analyzer to detect slow queries**.  
- **Optimize network bandwidth** for large-scale Azure SQL queries.  

🚀 **By following these best practices, Power BI dashboards will load faster, consume fewer resources, and provide real-time insights efficiently!**
# **Security Considerations for System Health Dashboard (SHD) Logging and Monitoring**  

Ensuring **robust security** in the **System Health Dashboard (SHD)** is critical for **protecting sensitive system logs, preventing unauthorized access, and maintaining compliance** with regulatory standards (e.g., **HIPAA, SOC 2, NIST**). Below are detailed security considerations for **logging, data access, encryption, monitoring, and compliance**.

---

## **1️⃣ Secure Logging Practices**  
### **1.1 Log Content Security**  
✅ **Mask or Remove Sensitive Data**  
   - Never log **PII (Personally Identifiable Information)** such as **usernames, passwords, or medical records**.  
   - Implement **field-level masking** for logs containing sensitive data:  
     ```sql
     ALTER TABLE system_logs ALTER COLUMN user_id ADD MASKED;
     ```

✅ **Use Secure Logging Levels**  
   - **INFO** → General system status.  
   - **WARN** → Potential issues requiring review.  
   - **ERROR** → System failure or service outage.  
   - **DEBUG** → (Only enabled in **development environments**, never in production).  

✅ **Restrict Debug Logging in Production**  
   - **Disable DEBUG logs** in live environments to prevent leakage of sensitive information.

---

## **2️⃣ Access Control & Authentication**  
### **2.1 Implement Role-Based Access Control (RBAC)**
✅ **Limit log access to authorized personnel**  
   - **Security Team** → Full access to security logs.  
   - **Developers** → Application logs only.  
   - **Business Users** → Limited access to system health reports.  

✅ **Azure RBAC Implementation**  
   - Assign roles in **Azure Monitor & Log Analytics**:
     ```powershell
     New-AzRoleAssignment -ObjectId <UserID> -RoleDefinitionName "Log Analytics Reader"
     ```
   - Use **Azure Entra ID (formerly Azure AD)** for authentication and **Single Sign-On (SSO)**.

✅ **ServiceNow ITSM Integration for Secure Log Access**  
   - Allow only **authorized IT personnel** to access **incident-related logs** via ServiceNow API.  
   - Define **least privilege permissions** for ServiceNow users:
     ```json
     {
       "role": "ITSM Analyst",
       "permissions": ["View Security Logs", "Create Incident Reports"]
     }
     ```

---

## **3️⃣ Encryption & Data Protection**  
### **3.1 Encrypt Logs in Transit and At Rest**
✅ **Use TLS 1.2+ for Secure Log Transmission**  
   - Enforce **HTTPS/TLS 1.2+ encryption** for API and log transmission:  
     ```yaml
     security:
       tls_version: "1.2"
       encryption: "AES-256"
     ```

✅ **Enable Transparent Data Encryption (TDE) for Azure SQL Logs**  
   ```sql
   ALTER DATABASE system_logs SET ENCRYPTION ON;
   ```

✅ **Encrypt Logs Stored in Azure Storage**  
   - Enable **Server-Side Encryption (SSE)** for logs stored in **Azure Blob Storage**.
   - **Use customer-managed keys (CMK)** in **Azure Key Vault** for added security.

✅ **Mask Personal & System Identifiers in Power BI Logs**  
   - Configure **Row-Level Security (RLS)** in Power BI:
     ```DAX
     UserTable[UserID] = USERPRINCIPALNAME()
     ```

---

## **4️⃣ Security Logging & Threat Detection**  
### **4.1 Implement Azure Sentinel for Security Monitoring**
✅ **Enable Azure Sentinel to Detect Anomalies in Logs**  
   - Monitor logs for **suspicious access patterns, brute-force attempts, and API failures**.
   - Example **KQL Query for Failed Logins**:
     ```kql
     SigninLogs
     | where ResultType != "Success"
     | summarize FailedAttempts = count() by bin(TimeGenerated, 1h)
     ```

✅ **Set Up Real-Time Security Alerts**  
   - Create **Azure Monitor alerts** for:
     - **Multiple failed login attempts (>10 per hour)**.  
     - **Unusual API access from unknown locations**.  
     - **Sudden spikes in error logs**.

✅ **Automate Incident Creation in ServiceNow for Critical Failures**  
   - Configure **Azure Monitor → ServiceNow Integration**:
     ```json
     {
       "incident": {
         "category": "Security",
         "impact": "High",
         "urgency": "Critical",
         "assigned_to": "Security Team"
       }
     }
     ```

---

## **5️⃣ Log Retention & Compliance**  
### **5.1 Define Secure Log Retention Policies**
✅ **Short-Term Retention (30-90 Days)**  
   - Keep logs in **Azure Log Analytics** for **real-time monitoring**.

✅ **Long-Term Retention (1-7 Years for Compliance)**  
   - Archive logs in **Azure Blob Storage (Cold Tier)**.
   - Use **automatic lifecycle policies** to manage log retention:
     ```json
     {
       "retention_policy": {
         "days": 365,
         "delete_after_expiry": true
       }
     }
     ```

✅ **Regulatory Compliance Requirements**  
   - Ensure **HIPAA, SOC 2, and NIST 800-53** compliance:
     - **Encrypt logs at rest and in transit**.  
     - **Limit access to log data using RBAC**.  
     - **Enable audit trails for log access**.

✅ **Audit Log Access & Review Periodically**  
   - Use **Azure Security Center** to track log access:
     ```kql
     AuditLogs
     | where ActivityType == "ReadLogData"
     ```

---

## **6️⃣ Incident Response & Recovery**
### **6.1 Security Incident Workflow**
✅ **Incident Response Using ServiceNow**
   - **Step 1:** Azure Monitor **detects an anomaly** in API logs.  
   - **Step 2:** Logs are sent to **ServiceNow ITSM** via webhook.  
   - **Step 3:** ServiceNow **automatically creates an incident**.  
   - **Step 4:** Security Team **analyzes logs and takes action**.  

✅ **Example ServiceNow Incident Automation**
```json
{
   "short_description": "Unauthorized API Access Detected",
   "priority": "Critical",
   "state": "New",
   "assigned_to": "Cybersecurity Analyst"
}
```

✅ **Disaster Recovery Plan for Logging System**
   - **Use redundant Azure Log Analytics Workspaces**.  
   - **Backup logs daily to secondary storage in a different Azure region**.  
   - **Simulate security breach scenarios quarterly**.

---

# **🚀 Summary: Security Considerations for SHD Logging**
| **Security Measure** | **Implementation** |
|---------------------|-------------------|
| **RBAC & Access Control** | Restrict log access via **Azure Entra ID (Azure AD)**. |
| **Encryption** | Use **TLS 1.2+ for data in transit** and **AES-256 for data at rest**. |
| **Threat Detection** | Enable **Azure Sentinel to monitor failed logins, API anomalies**. |
| **Real-Time Alerts** | Set up **Azure Monitor alerts for security failures**. |
| **Compliance & Retention** | Store logs securely for **HIPAA, SOC 2, and NIST 800-53 compliance**. |
| **Incident Response** | Automate **ServiceNow incident creation for security threats**. |

🚀 **By following these best practices, SHD logs remain secure, compliant, and accessible only to authorized users!**
# **Detailed Steps for Implementing Logging in System Health Dashboard (SHD) with Azure & ServiceNow**  

Logging is essential for **tracking system performance, API transactions, security events, and service reliability** in the **System Health Dashboard (SHD)**. This guide details the **end-to-end logging process**, integrating **Azure Monitor, Log Analytics, Azure Storage, Power BI, and ServiceNow** for efficient monitoring and issue resolution.

---

## **1️⃣ Logging Architecture Overview**  

```mermaid
graph TD
    A[Azure Services] -->|Diagnostic Logs| B[Azure Monitor]
    B -->|Log Collection| C[Azure Log Analytics]
    C -->|Store & Archive| D[Azure Storage]
    C -->|Send Incidents| E[ServiceNow]
    C -->|Power BI Logs| F[Power BI Service]
    E -->|ITSM Alerts| G[Service Desk]
    F -->|Real-Time Visualization| H[System Health Dashboard]
```

### **Key Components:**
- **Azure Monitor**: Collects logs from **MFTS, SIP, MES Portal, Azure SQL, and Azure Services**.
- **Azure Log Analytics**: Stores logs and enables advanced querying.
- **Azure Storage**: Provides **long-term retention** for compliance and audit logs.
- **ServiceNow ITSM**: Generates **incidents for system failures, security breaches, and SLAs**.
- **Power BI**: **Visualizes logs** for monitoring and analysis.

---

## **2️⃣ Configuring Azure Logging for System Health Dashboard**
### **Step 1: Enable Diagnostic Logging for Azure Services**
✅ **Enable Logging for MFTS, SIP, MES Portal & Azure SQL**
1. Go to **Azure Portal → Resource Groups → Select Service**.
2. Navigate to **Monitoring → Diagnostic Settings**.
3. Click **"Add Diagnostic Setting"**.
4. Select **Log Categories**:
   - **MFTS Logs** (File Transfers, Success/Failure Rate)
   - **SIP API Logs** (Latency, Throughput, Error Rate)
   - **MES Portal Logs** (User Access, Authentication)
   - **Azure SQL Logs** (Query Performance, Deadlocks)
5. **Send logs to**:
   - ✅ **Azure Monitor**
   - ✅ **Azure Log Analytics**
   - ✅ **Azure Storage (For archival & compliance)**
   - ✅ **Event Hub (For real-time processing in ServiceNow)**

---

### **Step 2: Configure Log Analytics for Querying Logs**
✅ **Enable Log Collection in Log Analytics Workspace**
1. Navigate to **Azure Monitor → Log Analytics Workspaces**.
2. Create a new workspace or select an existing one.
3. Go to **Data Sources** → Add:
   - **Azure SQL Logs**
   - **Storage Logs**
   - **Function App Logs (For MFTS Processing)**
   - **Service Bus Logs (For SIP Transactions)**
4. Use **Kusto Query Language (KQL)** to retrieve logs:
   ```kql
   AzureDiagnostics
   | where ResourceType == "APIManagementService"
   | summarize AvgLatency=avg(ResponseTime), ErrorCount=countif(ResponseCode >= 400)
   | by bin(TimeGenerated, 1h)
   ```

---

### **Step 3: Store & Archive Logs in Azure Storage**
✅ **Enable Log Retention in Azure Storage**
1. Navigate to **Azure Storage Account**.
2. Enable **Blob Storage Logging**.
3. Configure **Lifecycle Policy**:
   - **Retain logs for 30 days** in Hot Tier.
   - **Move logs older than 90 days** to **Cool Tier**.
   - **Delete logs older than 1 year**.

---

## **3️⃣ Integrating ServiceNow with Azure Logging**
### **Step 4: Configure Azure Monitor to Send Logs to ServiceNow**
✅ **Set Up Azure Monitor Alerts to ServiceNow**
1. Go to **Azure Monitor → Alerts**.
2. Click **"New Alert Rule"**.
3. Define **alert conditions**:
   - **MFTS Failure Rate > 5%**  
   - **SIP API Latency > 500ms**  
   - **MES Portal Login Failures > 50 per hour**  
4. Set **Action Group** → **Send to ServiceNow API**:
   - **Webhook URL**:  
     ```
     https://your-instance.service-now.com/api/now/table/incident
     ```
   - Authentication: **OAuth 2.0**.
   - Payload:
     ```json
     {
       "short_description": "MFTS Failure Detected",
       "category": "IT Services",
       "impact": "High",
       "urgency": "Critical",
       "assigned_to": "Service Desk"
     }
     ```

---

### **Step 5: ServiceNow Incident Automation for Logging Failures**
✅ **Configure Incident Workflows in ServiceNow**
1. Go to **ServiceNow → Workflow Editor**.
2. Create a **New Workflow**:
   - Trigger: **API Request from Azure Monitor**.
   - Action: **Create an Incident**.
3. Define **Incident Severity Based on Logs**:
   - **Priority 1 (Critical)** – System Outage Detected.
   - **Priority 2 (High)** – High API Failure Rate.
   - **Priority 3 (Medium)** – User Login Issues.

---

## **4️⃣ Power BI Integration for Log Visualization**
### **Step 6: Connect Power BI to Log Analytics**
✅ **Get Data from Log Analytics into Power BI**
1. Open **Power BI Desktop** → Click **Get Data**.
2. Select **Azure Monitor Logs**.
3. Enter **Log Analytics Workspace ID**.
4. Use **KQL Queries** to Fetch Logs:
   ```kql
   AzureDiagnostics
   | where ResourceType == "SQLDatabase"
   | summarize FailedQueries=count() by bin(TimeGenerated, 1h)
   ```

✅ **Schedule Power BI Data Refresh**
- Set **refresh frequency** to **15-30 minutes**.

---

### **Step 7: Visualize Log Data in Power BI**
✅ **Create System Health Dashboard Components**
1. **System Uptime Monitoring**  
   - KPI Card: **Availability (%)**  
   - Line Chart: **Downtime Over Time**  

2. **API Performance Metrics**  
   - Line Chart: **Response Time Trends**  
   - Stacked Bar: **Success vs. Failed Transactions**  

3. **Security & Compliance Logs**  
   - Bar Chart: **Failed Logins by Location**  
   - Pie Chart: **Incident Type Distribution**  

4. **ServiceNow Incident Analysis**  
   - Column Chart: **Incident Count by Severity**  
   - Donut Chart: **Resolution Time Trends**  

---

## **5️⃣ Monitoring & Alerts**
### **Step 8: Enable Real-Time Alerts & Automated Responses**
✅ **Configure Azure Sentinel for Advanced Threat Detection**
1. Navigate to **Azure Sentinel**.
2. Create **Alert Rules**:
   - **Multiple Failed Logins from Unusual Locations**
   - **API Response Time Degradation**
   - **SLA Violations in ServiceNow Incidents**
3. Integrate with **Microsoft Defender for Cloud**.

✅ **Enable ServiceNow Ticketing Automation**
- If **incident severity = HIGH**, auto-assign to **on-call engineer**.
- If **repeat failure > 3 times**, escalate to **Tier 2 Support**.

---

## **6️⃣ Summary: End-to-End Logging Flow**
### **🔹 What Happens When an Issue Occurs?**
1️⃣ **Azure SQL Fails to Respond** → Log sent to **Azure Monitor**.  
2️⃣ **Azure Monitor Alert** triggers a webhook → **ServiceNow creates an incident**.  
3️⃣ **Azure Storage Archives Logs** for compliance.  
4️⃣ **Power BI Refreshes Data** → Dashboard updates in real-time.  
5️⃣ **IT Team Investigates via ServiceNow** → Fixes the issue.  

✅ **Outcome**: **Real-time, automated, and actionable logging for system health tracking!** 🚀

### **Detailed Steps for Logging in the System Health Dashboard (SHD) – Power BI, MFTS, SIP, MES Portal, Azure Metrics**  

Logging is essential for **tracking system health, monitoring performance, auditing activities, and ensuring compliance** across **MFTS, SIP, MES Portal, and Azure services**. This guide covers **configuring, collecting, storing, and analyzing logs** in **Power BI, Azure Monitor, Log Analytics, and Azure Storage**.

---

## **1️⃣ Logging Architecture Overview**  
```mermaid
flowchart TD
    A["MFTS File Transfer Logs"] -- Logs to --> B["Azure Storage"]
    C["SIP API Logs"] -- Logs to --> D["Azure Monitor & Log Analytics"]
    E["MES Portal User Activity"] -- Authentication Logs --> F["Azure Entra ID - AAD Logs"]
    G["Azure Resource Logs"] -- VM, SQL, Storage Logs --> H["Azure Monitor"]
    H -- Processed Logs --> I["Power BI"]
    B -- Historical Logs --> I
    D -- "Real-time API Logs" --> I
    F -- User Activity Reports --> I
    n1["Service Now"] --> I
```
---

## **2️⃣ Step-by-Step Configuration of Logging**  

### **Step 1: Enable Logging for MFTS File Transfers**
✅ **Enable Diagnostic Logging for Azure Storage (MFTS Logs)**
1. Navigate to **Azure Portal → Storage Account → Monitoring → Diagnostic Settings**.  
2. Click **"Add Diagnostic Setting"** and enable logging for:  
   - **Blob Storage (File Upload & Access Logs)**  
   - **Queue Storage (MFTS Processing Events)**  
3. Select **"Send to Log Analytics"** for real-time monitoring.  

✅ **Store Logs in Azure Storage for Archival**
- Logs can also be exported to **Azure Data Lake for historical analysis**.  

---

### **Step 2: Configure Logging for SIP API Calls**
✅ **Enable API Logging in Azure API Management**
1. Navigate to **Azure API Management → APIs → Select API**.  
2. Under **"Monitoring"**, enable **Azure Monitor Diagnostics**.  
3. Configure **logging categories**:  
   - **Request & Response Time**  
   - **HTTP Status Codes (Success/Failure)**  
   - **Error Rate %**  

✅ **Enable Log Analytics for SIP API Logs**
- SIP API logs are sent to **Azure Monitor Log Analytics** using the **KQL query below**:  
  ```kql
  AzureDiagnostics
  | where ResourceType == "APIManagementService"
  | summarize AvgLatency=avg(ResponseTime), ErrorRate=avg(ErrorCount) by bin(TimeGenerated, 1h)
  ```

✅ **Create Alerts for API Failures**
- Set up **Azure Monitor Alerts** to trigger notifications when:
  - **API latency > 500ms**
  - **Error rate > 5%**
  - **SIP transaction failures exceed threshold**

---

### **Step 3: Capture MES Portal User Authentication Logs**
✅ **Enable Sign-In & Access Logs in Azure Entra ID**
1. Go to **Azure Portal → Azure Entra ID → Monitoring → Sign-In Logs**.  
2. Enable **audit logs for login failures, MFA authentication, and access changes**.  
3. Send logs to **Log Analytics Workspace for analysis**.  

✅ **Monitor User Access Logs in Power BI**
- Use **KQL query** to extract failed authentication attempts:  
  ```kql
  SigninLogs
  | where ResultType != "Success"
  | summarize FailedLogins=count() by bin(TimeGenerated, 1h)
  ```

✅ **Enable Role-Based Logging**
- Restrict logging access to **MES Admins, Security Teams, and IT Operations** using **RBAC policies**.

---

### **Step 4: Collect & Store Azure Resource Logs (VMs, SQL, Storage)**
✅ **Enable Diagnostic Settings for Azure Virtual Machines**
1. Navigate to **Azure Portal → Virtual Machines → Monitoring → Diagnostic Settings**.  
2. Enable logs for:  
   - **CPU, Memory, Disk Utilization**  
   - **Network Traffic**  
   - **Security Logs (Failed Access Attempts)**  
3. Select **"Send to Log Analytics"** for real-time monitoring.  

✅ **Enable SQL Query Performance Logs**
1. Go to **Azure SQL Database → Monitoring → Query Performance Insight**.  
2. Enable logging for:  
   - **Query Duration & Execution Time**  
   - **Deadlocks & Blocking Issues**  
   - **Failed Transactions**  

✅ **Store Logs in Azure Storage**
- Configure **Azure Storage Lifecycle Management** to retain logs for **up to 2 years**.

---

## **3️⃣ Integrating Logs with Power BI**
### **Step 5: Connect Power BI to Log Analytics for System Health Reports**
✅ **Retrieve System Logs in Power BI**  
1. Open **Power BI Desktop → Get Data → Azure Monitor Logs**.  
2. Enter **Log Analytics Workspace ID**.  
3. Use **KQL Queries** to fetch SIP, MFTS, MES, and Azure logs.  

✅ **Transform Log Data for Reporting**  
- Aggregate logs **by service, time, and event types**.  
- Example **DAX formula** for error rate visualization:  
  ```DAX
  ErrorRate = DIVIDE([Failed Transactions], [Total Transactions], 0) * 100
  ```

✅ **Schedule Data Refresh**  
- Set **15-minute refresh intervals** for real-time system health monitoring.  

---

## **4️⃣ Monitoring & Alerting for Logs**
### **Step 6: Enable Alerts & Automated Monitoring**
✅ **Azure Monitor Alerts for Critical Issues**
- **SIP API latency spikes (>500ms)**
- **MFTS file transfer failures (>5%)**
- **MES Portal failed logins (>10 per minute)**  

✅ **Power BI Dashboard Alerts**
- Use **Power BI Service Alerts** to notify admins about **service degradation**.

✅ **Log Retention Policies**
- **Archive logs in Azure Storage (90 days retention for cost savings)**.  
- **Keep critical security logs for 1 year for compliance**.

---

## **5️⃣ Summary of Logging Best Practices**
✅ **Centralized Logging in Azure Monitor & Log Analytics**  
✅ **Store & Archive Logs in Azure Storage for Auditing**  
✅ **Enable Role-Based Access to Sensitive Logs**  
✅ **Use Power BI for Real-Time System Health Dashboards**  
✅ **Configure Alerts for Critical Failures**  

🚀 **Outcome**: A **secure, scalable, and real-time monitoring system** for **MFTS, SIP, MES Portal, and Azure resources** in Power BI.

### **Steps on Application Configuration for the System Health Dashboard (SHD) in Power BI**  

To configure the **System Health Dashboard (SHD)** for monitoring **MFTS, SIP, MES Portal, and Azure Metrics**, follow these steps:

---

## **1️⃣ Set Up Data Sources & Connect to Power BI**
### **Step 1: Configure Data Sources in Azure**  
✅ **Azure Log Analytics Configuration**  
   - Go to **Azure Portal** → Navigate to **Log Analytics Workspace**.  
   - Enable **Diagnostic Settings** for:
     - **MFTS (File Transfer Metrics)**
     - **SIP (API Logs)**
     - **MES Portal (Authentication Logs)**
     - **Azure Resources (VMs, Storage, SQL)**  
   - Select **"Send to Log Analytics"** and define the retention policy.  

✅ **Azure Storage Configuration for MFTS Logs**  
   - Create an **Azure Storage Account**.  
   - Enable **Storage Analytics Logging** to track file transfers.  

✅ **ServiceNow API Configuration (For Incident Management Data)**  
   - In ServiceNow, go to **System Web Services → REST API Explorer**.  
   - Generate API endpoint:  
     ```
     https://your-instance.service-now.com/api/now/table/incident
     ```
   - Create a service account with **read-only permissions** to fetch incident logs.  

✅ **Azure SQL Configuration (For Transaction & SLA Data)**  
   - Deploy **Azure SQL Database**.  
   - Set up **tables for transactional data** (e.g., Claims Processing, Provider Requests).  
   - Configure **Row-Level Security (RLS) for role-based access control**.  

---

## **2️⃣ Power BI Data Connection Setup**
### **Step 2: Connect Data Sources to Power BI**
✅ **Connect to Azure Log Analytics**  
   - Open **Power BI Desktop** → Click **Get Data** → Select **Azure Monitor Logs**.  
   - Enter the **Log Analytics Workspace ID & Key**.  
   - Use **KQL Queries** to pull system health data:  
     ```kql
     AzureDiagnostics
     | where ResourceType == "APIManagementService"
     | summarize AvgLatency=avg(ResponseTime) by bin(TimeGenerated, 1h)
     ```

✅ **Connect to Azure Storage for MFTS Logs**  
   - Go to **Get Data → Azure Blob Storage**.  
   - Enter the **Storage Account Name & Key**.  
   - Load **file transfer logs for MFTS performance tracking**.  

✅ **Connect to ServiceNow API for ITSM Data**  
   - Go to **Get Data → Web Connector**.  
   - Enter **ServiceNow API URL** and credentials.  
   - Select **incident fields** such as **status, priority, resolution time**.  

✅ **Connect to Azure SQL for Transaction Data**  
   - Go to **Get Data → Azure SQL Database**.  
   - Enter **server name, database name, and credentials**.  
   - Use SQL query to fetch key metrics:  
     ```sql
     SELECT 
       TransactionID, ServiceName, ResponseTime, ErrorRate, Timestamp 
     FROM TransactionLogs
     WHERE Timestamp >= DATEADD(DAY, -30, GETDATE())
     ```

---

## **3️⃣ Configure Data Modeling & Relationships**
### **Step 3: Define Data Relationships**
✅ **Establish Relationships in Power BI Model View**  
   - **MFTS Logs ↔ SIP API Logs ↔ MES User Activity ↔ Azure SQL Transactions**.  
   - Use **common keys** (e.g., `TransactionID`, `UserID`, `ServiceName`).  

✅ **Create Custom Measures in DAX for Performance Metrics**  
   - **API Success Rate:**  
     ```DAX
     SuccessRate = DIVIDE([Successful Transactions], [Total Transactions], 0)
     ```
   - **System Uptime Percentage:**  
     ```DAX
     Uptime = (1 - [Downtime Hours] / 24) * 100
     ```
   - **SLA Compliance Calculation:**  
     ```DAX
     SLACompliance = IF([ResponseTime] < 200, "Compliant", "Non-Compliant")
     ```

---

## **4️⃣ Configure Role-Based Access Control (RBAC)**
### **Step 4: Implement Security & Permissions**
✅ **Set Row-Level Security (RLS) in Power BI**  
   - Define roles for different **user groups** (Admins, Analysts, IT Support).  
   - **DAX Example for Restricted Access:**  
     ```DAX
     UserTable[Department] = USERPRINCIPALNAME()
     ```

✅ **Enable Azure Entra ID (Azure AD) for Authentication**  
   - Configure **Power BI Service Authentication** via **Azure AD**.  
   - Restrict access to **authorized Medicaid IT personnel only**.  

✅ **Apply Sensitivity Labels in Power BI**  
   - Use **Microsoft Information Protection (MIP)** to classify reports:
     - **Confidential** – SLA & transaction data.  
     - **Restricted** – Authentication logs & security events.  

---

## **5️⃣ Build Power BI Dashboards & Visuals**
### **Step 5: Design System Health Dashboard**
✅ **Create KPI Indicators for Real-Time Monitoring**  
   - Use **Conditional Formatting** for SLA compliance (✅ Green: OK, ⚠️ Yellow: Warning, ❌ Red: Issue).  
   - **Example KPI Cards:**  
     - **Service Uptime (%)**  
     - **API Latency (ms)**  
     - **MFTS Transfer Success Rate**  

✅ **Implement Drill-Through Reports for Root Cause Analysis**  
   - Users should **click an API failure rate metric** and see **detailed error logs**.  

✅ **Optimize Power BI Dashboard Performance**  
   - Limit visual elements per report page.  
   - Use **Aggregated Tables** for large datasets.  
   - Implement **Query Folding** in Power Query.  

---

## **6️⃣ Configure Scheduled Refresh & Monitoring**
### **Step 6: Automate Data Refresh & Performance Monitoring**
✅ **Set Up Scheduled Refresh in Power BI Service**  
   - **Go to Power BI Service → Datasets → Scheduled Refresh**.  
   - Configure **refresh frequency** based on data latency:
     - **Azure Monitor & API Logs** → Every **15 minutes**.  
     - **MFTS & ServiceNow Logs** → Every **30 minutes**.  
     - **Transaction Data (SQL)** → Every **1 hour**.  

✅ **Enable Alerts for Critical Failures**  
   - Use **Azure Monitor** to trigger alerts when:
     - **Service Uptime < 99%**  
     - **API Latency > 500ms**  
     - **MFTS Error Rate > 5%**  

✅ **Monitor Power BI Report Performance**  
   - Track **dashboard load times** and optimize queries.  

---

## **7️⃣ Deployment & Maintenance**
### **Step 7: Deploy & Maintain SHD Application**
✅ **Use Power BI Deployment Pipelines**  
   - Deploy dashboards across **Dev, Test, and Production environments**.  

✅ **Enable Power BI Usage Analytics**  
   - Monitor **user activity, report interactions, and refresh success rates**.  

✅ **Regularly Review Data Security & Compliance**  
   - Audit access logs to ensure **RBAC policies are enforced**.  
   - Update **data retention policies** for compliance with **HIPAA & Medicaid guidelines**.  

---

## **🎯 Conclusion**
By following these **application configuration steps**, the **System Health Dashboard (SHD) in Power BI** will be fully integrated with **Azure Monitor, Log Analytics, ServiceNow, and SQL databases**, enabling **real-time insights, proactive monitoring, and data-driven decision-making** for Medicaid operations. 🚀

### **Expanded Guide: Connecting Azure SQL for Transaction Data (SQL → ADF → Azure Storage → Power BI)**  

This section provides a detailed step-by-step guide for **ingesting, transforming, and visualizing transaction data from Azure SQL into Power BI** using **Azure Data Factory (ADF) and Azure Storage**. This architecture ensures **scalability, data transformation efficiency, and optimal Power BI performance**.

---

## **1️⃣ Architecture Overview**  

```mermaid
graph TD
    A[Azure SQL Database] -->|Extract| B[Azure Data Factory ADF]
    B -->|Transform & Load| C[Azure Storage Blob/Data Lake]
    C -->|Power BI Dataflow| D[Power BI Service]
    D -->|Scheduled Refresh| E[Power BI Dashboard]
```

### **Key Components in the Architecture**
- **Azure SQL Database**: Stores **transactional data** (e.g., claims, service requests, provider activity).  
- **Azure Data Factory (ADF)**: Extracts, transforms, and loads (ETL) data from **Azure SQL into Azure Storage**.  
- **Azure Storage (Blob or Data Lake Storage Gen2)**: Acts as an intermediary **staging layer** for Power BI to efficiently query data.  
- **Power BI Service**: Connects to **Azure Storage** and visualizes the **transaction data**.  

---

## **2️⃣ Step-by-Step Implementation**
### **Step 1: Configure Azure SQL Database for Transaction Data**
✅ **Create an Azure SQL Database**  
   - Navigate to **Azure Portal → Azure SQL Database**.  
   - Click **"Create Database"**, select **Resource Group**, and configure performance tier.  
   - Define tables for transactional data:  
     ```sql
     CREATE TABLE Transactions (
         TransactionID INT PRIMARY KEY,
         ServiceName NVARCHAR(100),
         ResponseTime INT,
         ErrorRate DECIMAL(5,2),
         CreatedAt DATETIME
     );
     ```

✅ **Enable Firewall & Networking for ADF Access**  
   - Navigate to **SQL Server → Firewall Rules**.  
   - Add **ADF’s IP address range** to allow secure access.  

✅ **Create a Read-Optimized View for ETL**  
   - To improve extraction performance, create a **materialized view**:  
     ```sql
     CREATE VIEW vw_TransactionSummary AS
     SELECT 
         TransactionID, ServiceName, ResponseTime, 
         ErrorRate, CreatedAt
     FROM Transactions
     WHERE CreatedAt >= DATEADD(DAY, -30, GETDATE());
     ```

---

### **Step 2: Configure Azure Data Factory (ADF)**
✅ **Create an ADF Pipeline to Extract Data from SQL**  
   - Navigate to **Azure Portal → Azure Data Factory → Create Pipeline**.  
   - **Add a "Copy Data" Activity**.  
   - **Source**: Connect to **Azure SQL Database**, select **"vw_TransactionSummary"**.  
   - **Destination**: Set as **Azure Blob Storage (Parquet/CSV format)**.  

✅ **Schedule the ADF Pipeline**  
   - In ADF, navigate to **Triggers → New/Edit Schedule**.  
   - Define **incremental refresh** frequency (e.g., **every 30 minutes or hourly**).  

✅ **Optimize Data Transfer with Partitioning**  
   - Use **Incremental Data Load** with **CreatedAt column**:  
     ```sql
     SELECT * FROM vw_TransactionSummary WHERE CreatedAt > ?
     ```

---

### **Step 3: Store Data in Azure Storage (Blob or Data Lake)**
✅ **Choose Storage Type**  
   - **Azure Blob Storage**: Stores **CSV/Parquet files** for cost-effective storage.  
   - **Azure Data Lake Storage Gen2**: Provides **hierarchical namespace and fine-grained security**.  

✅ **Optimize Data Storage Format for Power BI**  
   - **Use Parquet Format** for optimized **query performance** in Power BI.  
   - **Partition Data by Date** to speed up queries:  
     ```
     /transactions/year=2025/month=03/day=10/transactions.parquet
     ```

✅ **Enable Lifecycle Policies**  
   - Configure **automatic deletion** for old files beyond **90 days** to optimize storage costs.  

---

### **Step 4: Connect Azure Storage to Power BI**
✅ **Configure Power BI Dataflow to Read from Azure Storage**  
   - **Power BI Service → Dataflows → New Dataflow**.  
   - Select **"Azure Blob Storage" or "Azure Data Lake"** as the data source.  
   - **Connect using Storage Account Key or Managed Identity**.  
   - Select **"transactions.parquet"** file and load into Power BI.  

✅ **Transform Data in Power Query**  
   - **Filter data** to remove duplicates or unnecessary columns.  
   - Convert **timestamps into local timezone format**.  
   - Aggregate data for **faster report loading** (e.g., average response time by service).  

✅ **Schedule Refresh in Power BI Service**  
   - Navigate to **Dataset Settings → Scheduled Refresh**.  
   - Set refresh frequency **(every 30 minutes or as needed)**.  

---

### **Step 5: Create Power BI Visualizations**
✅ **Design Key Metrics in Power BI Dashboard**  
   - **KPI Cards for Real-Time Monitoring**  
     - 🟢 **Service Uptime (%)**  
     - 📊 **API Latency (ms)**  
     - ✅ **Transaction Throughput (TPS)**  
   - **Time-Series Charts**  
     - **Response Time Trends by Service**  
     - **Error Rate % Over Time**  
   - **Drill-Down Reports**  
     - Click **on failed transactions** to view **detailed API logs**.  

✅ **Apply Row-Level Security (RLS)**  
   - Define **RLS roles for different user groups** in Power BI.  
   - Example:  
     ```DAX
     [Department] = USERPRINCIPALNAME()
     ```

---

## **3️⃣ Summary: Why Use This Approach?**
✅ **Performance Optimization**:  
   - ADF extracts only **incremental changes**, reducing **query load on Azure SQL**.  
   - Parquet files in **Azure Storage** provide **faster read performance for Power BI**.  

✅ **Scalability**:  
   - Supports **millions of transactions** without slowing down Power BI reports.  
   - Power BI **queries pre-processed data** instead of **running expensive SQL queries**.  

✅ **Security & Compliance**:  
   - **RBAC-controlled access to Azure SQL, Storage, and Power BI reports**.  
   - **Audit logs & encryption policies enforced** via Azure Security Center.  

🚀 **Final Outcome**: A **scalable, secure, and real-time transaction monitoring solution** using **Azure SQL, ADF, Storage, and Power BI**.


### **7.1 Best Practices for System Health Dashboard (SHD) in Power BI**  

Ensuring the **System Health Dashboard (SHD)** delivers **accurate, real-time insights** while maintaining **performance, security, and scalability** requires adherence to **best practices**. Below are key recommendations for optimizing **data integration, visualization, security, and efficiency** when working with **Power BI, MFTS, SIP, MES Portal, and Azure Metrics**.

---

## **1️⃣ Data Integration & Connectivity Best Practices**
🔹 **Use DirectQuery for Real-Time Metrics**  
   - Instead of relying on imported datasets, **DirectQuery** enables Power BI to **query data live from Azure Monitor, Log Analytics, and SQL databases**.  
   - **Example:** Fetching **SIP API response times** directly from **Azure Monitor Logs** ensures up-to-date API latency tracking.  

🔹 **Optimize Scheduled Refresh for Near Real-Time Data**  
   - **Set refresh intervals based on data update frequency** (e.g., every **15 minutes for API logs**, every **hour for storage metrics**).  
   - **Use Incremental Refresh** to load only new data instead of reloading the entire dataset.  
   - **Example:** Refresh **Azure Storage MFTS logs** every 30 minutes while keeping incident reports on a **4-hour refresh cycle**.  

🔹 **Use Power BI Dataflows for Pre-Processing**  
   - Offload **heavy data transformations** to **Power BI Dataflows** instead of processing large datasets on the report side.  
   - **Example:** Transforming **ServiceNow incident logs** before loading them into Power BI improves performance.  

🔹 **Use Azure Data Factory for ETL Operations**  
   - For large-scale transformations, use **Azure Data Factory** to **extract, transform, and load (ETL)** data into **Azure SQL or Storage** before Power BI reads it.  
   - **Example:** Collecting **MFTS transaction logs**, filtering out irrelevant data, and storing it in an optimized **Azure SQL table**.

---

## **2️⃣ Performance Optimization for Power BI Reports**
🔹 **Reduce Data Model Size with Aggregations**  
   - Implement **aggregated tables** in Power BI for high-cardinality datasets like **transaction logs and API metrics**.  
   - Use **pre-aggregated data views** in **Azure SQL** to reduce load time for Power BI queries.  

🔹 **Optimize DAX Queries**  
   - Use **variables** (`VAR`) in DAX to improve performance by reducing repeated calculations.  
   - Avoid using **FILTER() inside CALCULATE()**, as it can slow down queries.  
   - **Example DAX Optimization:**  
     ```DAX
     VAR SuccessRate = DIVIDE([Successful Transactions], [Total Transactions], 0)
     RETURN SuccessRate
     ```

🔹 **Leverage Power BI Composite Models**  
   - Combine **Import Mode for static datasets** (e.g., configuration data) and **DirectQuery for dynamic data** (e.g., SIP API logs).  
   - **Example:** Load **historical ServiceNow ticket data** in **Import Mode**, while using **DirectQuery for live SLA compliance tracking**.  

🔹 **Optimize Query Folding in Power Query**  
   - Use query folding to **push data transformations** to the source system, reducing load on Power BI.  
   - **Example:** Instead of filtering data in Power BI, apply **WHERE conditions** in Azure SQL to return only **relevant incident logs**.

---

## **3️⃣ Security & Access Control Best Practices**
🔹 **Implement Row-Level Security (RLS) for Role-Based Access**  
   - Restrict **Power BI dashboard views** based on user roles.  
   - Example RLS Rules:
     - **MES Admins** → Full system health reports.  
     - **SIP Engineers** → API and integration logs only.  
     - **Security Teams** → Authentication failures and compliance reports.  
   - **DAX Example for RLS**:  
     ```DAX
     UserTable[Department] = USERPRINCIPALNAME()
     ```

🔹 **Secure API Keys & Credentials**  
   - Store **ServiceNow API keys, Azure SQL credentials, and Log Analytics workspace IDs** securely using **Azure Key Vault** instead of hardcoding them.  

🔹 **Apply Power BI Sensitivity Labels**  
   - Use **Microsoft Information Protection (MIP)** to classify reports containing **PII or financial data**.  
   - Example:
     - **Confidential** – Financial claims and provider payment data.  
     - **Restricted** – Patient-sensitive logs.  

🔹 **Enable Multi-Factor Authentication (MFA) for Access**  
   - Require **MFA for Power BI Service access**, especially for admins with **privileged access to MES reports**.  

---

## **4️⃣ Visualization & Usability Best Practices**
🔹 **Use KPI Indicators for System Health Monitoring**  
   - Implement **color-coded indicators** to highlight anomalies:
     - **🟢 Green** – System Healthy  
     - **🟡 Yellow** – Performance Degrading  
     - **🔴 Red** – Critical Failure  
   - Example: **SLA compliance below 95% triggers a red warning on the dashboard.**  

🔹 **Enable Drill-Through Reports for Root Cause Analysis**  
   - Users should **click an error count** and drill through to **detailed API failure logs or ServiceNow incident tickets**.  

🔹 **Design Dashboards for Different User Personas**  
   - **Executives** → Summary KPIs & SLA compliance.  
   - **IT Operations** → System uptime & error trends.  
   - **Developers** → Detailed API logs & response times.  

🔹 **Limit Visual Elements Per Page for Performance**  
   - Avoid too many visuals per page; **optimize rendering speed** by **grouping related insights** into separate reports.  
   - **Example:** Separate **MFTS Metrics, SIP API Performance, and MES Portal Logs** into different tabs instead of one crowded dashboard.  

---

## **5️⃣ Monitoring & Maintenance Best Practices**
🔹 **Set Up Power BI Data Refresh Alerts**  
   - Configure **failure alerts** if dataset refresh **fails multiple times**.  
   - Example: **Use Azure Monitor to trigger an email alert if Power BI fails to refresh data from ServiceNow API.**  

🔹 **Monitor Power BI Performance in the Admin Portal**  
   - Regularly check **Power BI capacity usage** to **identify slow-loading reports or bottlenecks**.  

🔹 **Enable Usage Analytics to Track Report Interactions**  
   - Use **Power BI Service Usage Metrics** to track **which users access dashboards and how frequently**.  
   - Example: **Identify underutilized reports to optimize dashboard design.**  

🔹 **Implement Version Control for Power BI Reports**  
   - Use **Power BI Deployment Pipelines** to manage **Dev, Test, and Production versions** of dashboards.  

---

### **Conclusion**
By following these best practices, the **System Health Dashboard (SHD)** will remain **scalable, secure, and high-performing**, enabling **Medicaid IT teams to proactively monitor** the health of **SIP, MFTS, MES Portal, and Azure environments** in Power BI. 🚀

### **Developer’s Guide: System Health Dashboard (SHD) in Power BI for MFTS, SIP, MES Portal, and Azure Metrics**  

#### **Introduction**  
The **System Health Dashboard (SHD)** provides a **real-time operational view** of the **Managed File Transfer System (MFTS)**, **System Integration Platform (SIP)**, **Medicaid Enterprise System (MES) Portal**, and **Azure Metrics**. This guide outlines **data integration, Power BI setup, and best practices** for implementing the SHD.

---

## **1. Architecture Overview**  
The SHD aggregates data from **multiple Azure services**, **log sources**, and **ServiceNow** to create a **unified performance dashboard**.  

### **1.1 Data Flow Overview**  
```mermaid
graph TD
    A[Azure Monitor & Log Analytics] -->|Logs & Metrics| B[Power BI Service]
    C[MFTS Logs] -->|File Transfer Data| B
    D[SIP API Metrics] -->|API Logs & Transactions| B
    E[MES Portal Access Logs] -->|User Activity| B
    F[Azure Metrics VMs, SQL, Storage] -->|Resource Usage| B
    B -->|Visualized Reports| G[System Health Dashboard -SHD]
    G -->|Role-Based Access| H[Authorized Users MES Admins, IT, Support]
```

---

## **2. Data Sources and Integration**  
SHD retrieves data from various sources to monitor **system performance, security, and service reliability**.

| **Source**  | **Data Collected**  | **Integration Method**  |
|-------------|--------------------|------------------------|
| **MFTS Logs**  | File transfer status, success/failure rate, throughput | Azure Storage → Log Analytics → Power BI |
| **SIP API Logs**  | API response time, transaction errors, uptime | Azure API Management → Log Analytics → Power BI |
| **MES Portal**  | User authentication logs, session tracking | Azure Entra ID → Log Analytics → Power BI |
| **Azure Metrics**  | VM CPU, memory, disk, network usage | Azure Monitor → Log Analytics → Power BI |
| **ServiceNow**  | Incident management, SLA compliance | ServiceNow API → Power BI |

---

## **3. Power BI Setup and Configuration**  
### **3.1 Data Connection in Power BI**  
1. **Get Data → Select "Azure Monitor Logs"**  
   - Navigate to **Power BI Desktop** → Click **Get Data** → Choose **Azure Monitor Logs**.  
   - Enter the **Log Analytics Workspace ID & Key**.  
   - Use **KQL queries** to retrieve system metrics.  

2. **Connect to Azure Storage for MFTS Data**  
   - Select **Azure Blob Storage**.  
   - Load **MFTS transfer logs** for processing.  

3. **ServiceNow API Connection** (For Incident Management)  
   - Use the **Power BI Web Connector**.  
   - Enter ServiceNow API URL:  
     ```
     https://your-instance.service-now.com/api/now/table/incident
     ```
   - Authenticate using **OAuth 2.0** or **Basic Auth**.  

4. **Azure API Management Logs for SIP Metrics**  
   - Use **Azure API Management diagnostic settings** to stream logs to **Log Analytics**.  
   - Query API logs in Power BI using:  
     ```kql
     AzureDiagnostics
     | where ResourceType == "APIManagementService"
     | summarize AvgLatency=avg(ResponseTime) by bin(TimeGenerated, 1h)
     ```

---

## **4. Dashboard Development in Power BI**  
### **4.1 Key Visualizations in the System Health Dashboard**  
The dashboard will include **multiple sections** to provide **real-time monitoring insights**.

### **1️⃣ MFTS Monitoring (File Transfer Health)**
📊 **Visuals:**
- **Success vs. Failed Transfers (Stacked Bar Chart)**
- **Transfer Throughput Over Time (Line Chart)**
- **Top File Transfer Errors (Bar Chart)**

📌 **KQL Query for MFTS Logs**
```kql
StorageBlobLogs
| where OperationName == "PutBlob"
| summarize TotalTransfers=count(), FailedTransfers=sumif(ResultType=="Failure",1,0) by bin(TimeGenerated, 1h)
```

---

### **2️⃣ SIP API Performance**
📊 **Visuals:**
- **API Response Time Distribution (Histogram)**
- **Successful vs. Failed API Calls (Pie Chart)**
- **API Latency Trends (Line Chart)**

📌 **KQL Query for SIP API Logs**
```kql
AzureDiagnostics
| where ResourceType == "APIManagementService"
| summarize AvgLatency=avg(ResponseTime) by bin(TimeGenerated, 1h)
```

---

### **3️⃣ MES Portal User Activity**
📊 **Visuals:**
- **Successful vs. Failed Logins (Bar Chart)**
- **High-Risk Logins by Location (Map Visualization)**
- **Session Duration Trends (Line Chart)**

📌 **KQL Query for MES User Logs**
```kql
SigninLogs
| where ResultType != "Success"
| summarize FailedLogins=count() by bin(TimeGenerated, 1h)
```

---

### **4️⃣ Azure Resource Utilization**
📊 **Visuals:**
- **VM CPU & Memory Usage (Gauge Chart)**
- **Network Bandwidth Usage (Line Chart)**
- **Storage Capacity Monitoring (Bar Chart)**

📌 **KQL Query for Azure VM Metrics**
```kql
Perf
| where CounterName in ("CPU Usage", "Available Memory")
| summarize AvgCPU=avg(CounterValue) by bin(TimeGenerated, 1h), Computer
```

---

## **5. Scheduled Data Refresh & Automation**
To keep the dashboard **up to date**, configure **automatic data refresh**.

### **5.1 Enabling Power BI Scheduled Refresh**
1. **Publish Report** → Upload Power BI dashboard to **Power BI Service**.  
2. **Go to "Datasets" → Click "Scheduled Refresh"**.  
3. **Set Refresh Frequency**:  
   - MFTS & SIP Logs → **Every 15 minutes**.  
   - Azure Metrics → **Every hour**.  
   - ServiceNow Incident Data → **Every 4 hours**.  
4. **Enable Data Gateway for On-Prem SQL Connections** (If applicable).  

---

## **6. Security & Role-Based Access Control (RBAC)**
### **6.1 Power BI Row-Level Security (RLS) for MES Data**
✅ **RBAC Configurations:**
- **SIP Admins** → Access to **all API & system health metrics**.
- **MFTS Team** → Can only view **file transfer reports**.
- **Business Analysts** → Access to **MES portal & SLA compliance data**.
- **Security Teams** → View **Azure security logs & authentication failures**.

### **6.2 Azure Entra ID Authentication for Power BI Access**
1. **Enable Power BI Embedded for MES Portal**.
2. **Use Azure Entra ID (Azure AD) for Role-Based Access**.
3. **Restrict PII Data Views** to **authorized users only**.

---

## **7. Performance Optimization**
### **7.1 Best Practices**
✅ Use **DirectQuery for real-time monitoring** where needed.  
✅ **Aggregate large datasets** before visualizing.  
✅ Optimize **KQL queries** for efficiency.  
✅ **Cache API responses** for frequently accessed data.  

---

## **8. Conclusion**
By implementing the **System Health Dashboard (SHD)** in Power BI, Medicaid IT teams can **proactively monitor system health, API performance, file transfer operations, and security events** across **MFTS, SIP, MES Portal, and Azure resources**. 🚀  

This guide provides the **complete roadmap** for integrating Power BI with **Azure Monitor, Log Analytics, and ServiceNow**, enabling **real-time insights, automated reporting, and intelligent decision-making** for Medicaid operations. 🎯

Filtering in Power BI allows you to refine data displayed in reports and visualizations. You can filter data at different levels, including **visual-level, page-level, and report-level** filters. Here’s how you can apply filters in Power BI:

---

### **1. Using Filters Pane**
The Filters pane in Power BI provides a simple way to apply filters to reports.

#### **Steps:**
1. **Open the Filters Pane**: If it's not visible, click **View** > **Filters**.
2. **Choose a Field**: Drag a field from the **Fields pane** into the **Filters pane**.
3. **Apply Filter Type**:
   - **Basic Filtering**: Select one or more values from a dropdown.
   - **Advanced Filtering**: Set conditions like greater than, less than, contains, etc.
   - **Top N Filtering**: Show top N records based on a metric.

4. **Types of Filters**:
   - **Visual-Level Filter**: Applies to a single visualization.
   - **Page-Level Filter**: Applies to all visuals on a report page.
   - **Report-Level Filter**: Applies to all pages in a report.

---

### **2. Using Slicers**
Slicers provide an interactive way to filter data in reports.

#### **Steps:**
1. Go to **Home** > **Visualizations** > **Slicer**.
2. Drag a field (e.g., Date, Category) to the slicer.
3. Adjust settings:
   - Multi-select (Ctrl + Click)
   - Single-select
   - Drop-down or List

---

### **3. Using Visual Interactions**
Filters can be applied dynamically by interacting with visuals.

#### **Steps:**
1. Click on a **visualization** (e.g., a bar chart).
2. Other visuals update based on the selected data.
3. To control how visuals interact:
   - Click on the **Format** tab.
   - Select **Edit Interactions**.
   - Choose from **Filter**, **Highlight**, or **None**.

---

### **4. Using Drill-Through Filters**
Drill-through filters allow users to click on a value and navigate to a detailed report.

#### **Steps:**
1. Create a new report page.
2. Drag a **category field** into the **Drillthrough** area in the Filters pane.
3. Create detailed visuals based on the selected category.
4. Right-click a value in a report and choose **Drillthrough**.

---

### **5. Using DAX for Filtering in Measures**
You can apply filters using **DAX** in calculated measures or columns.

#### **Example: Filtering Sales for a Specific Product**
```DAX
FilteredSales = CALCULATE(SUM(Sales[Amount]), Sales[Product] = "Laptop")
```

#### **Example: Filtering Top 5 Customers**
```DAX
Top5Customers = 
    TOPN(5, SUMMARIZE(Sales, Sales[Customer], "Total Sales", SUM(Sales[Amount])), [Total Sales], DESC)
```

---

### **6. Filtering in Power Query (Data Transformation)**
You can filter data before loading it into Power BI using Power Query.

#### **Steps:**
1. Go to **Home** > **Transform Data**.
2. Click the dropdown next to a column.
3. Choose filter options (e.g., Greater Than, Equals, Contains).
4. Click **Close & Apply**.

---

### **Conclusion**
Power BI provides multiple ways to filter data:
- **Filters Pane**: Simple field-based filtering.
- **Slicers**: Interactive filtering for reports.
- **Visual Interactions**: Clicking on visuals updates others.
- **Drill-Through**: Navigates to detailed reports.
- **DAX**: Advanced filtering in measures.
- **Power Query**: Pre-load filtering at the data transformation stage.

Which method are you looking to use in your Power BI report? 😊
