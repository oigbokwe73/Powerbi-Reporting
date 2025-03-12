# Powerbi-Reporting

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
    B -->|Visualized Reports| G[System Health Dashboard (SHD)]
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
