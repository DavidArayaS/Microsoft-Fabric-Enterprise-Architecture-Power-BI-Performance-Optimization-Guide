# Microsoft-Fabric-Enterprise-Architecture-Power-BI-Performance-Optimization-Guide

## 1. Fabric Capacity Architecture – Enterprise Considerations

This section outlines key architectural decisions for deploying and managing Microsoft Fabric in an enterprise environment, with a focus on performance, scalability, and workload governance.

### 1.1 Capacity Planning

- Choose a Fabric SKU that aligns with your expected data and analytics needs (e.g., F64–F128 for production-grade usage).
- As the organization grows and analytics adoption increases, **capacity needs will also increase**.
- Plan for **scaling out** across multiple capacities when:
  - Workloads require isolation (e.g., Power BI vs Spark)
  - Business units require dedicated performance or SLAs
  - Data engineering workloads affect report performance

- Note: **Fabric capacities do not support auto-scaling**. You must size and manage workloads manually based on usage trends and business growth.

---

### 1.2 Workload Segregation

- Allocate **dedicated capacities for specific workload types** to avoid resource contention **when it makes sense**:
  - Power BI (semantic models, reports, dashboards)
  - Data Engineering (Spark, Lakehouses, Notebooks)
  - Real-Time Analytics (KQL, streaming jobs)

- Disable unused workloads on a capacity to preserve available Compute Units (CUs).
- Monitor CU consumption per workload using the **Metrics App** or APIs.

---

### 1.3 Workspace and Domain Strategy

- Use **domain-based workspaces** (e.g., Finance, Sales, HR) to streamline governance, access control, and resource monitoring.
- Avoid mixing data engineering and BI workloads in the same workspace when on shared capacity.
- Align workspaces with business or data domains that consume or produce data, not lifecycle stages.

---

### 1.4 Spark and Lakehouse Guidelines

- Avoid scheduling Spark-based notebooks or pipelines during peak Power BI usage windows if using shared capacity.
- Limit concurrent Spark job execution within a workspace to reduce CU starvation.
- **Adopt the Medallion Architecture** for scalable and governed Lakehouse implementation:
  - **Bronze**: Raw ingestion from source systems
  - **Silver**: Cleaned, conformed, and validated data
  - **Gold**: Business-ready, aggregated, and analytical data

This layered approach enables data quality assurance and versioning **within the same workspace**, reducing the need for Dev/Test/Prod workspace separation.

---

### 1.5 Notebook Optimization (Spark)

Notebooks in Fabric run on Spark compute and consume capacity. To avoid performance issues:

- Minimize full scans and joins across large datasets
- Use caching and partitioning wisely to optimize memory and execution time
- Structure notebooks into logical, modular steps (e.g., ingestion, transformation, validation)
- Monitor Spark job performance using Fabric’s execution details panel

Properly optimized notebooks help ensure Spark workloads do not interfere with concurrent Power BI or real-time operations.

---

### 1.6 Governance and Platform Features

- Apply role-based access control (RBAC) at the workspace and Lakehouse item levels.
- Use Fabric's **Surge Protection** feature to manage and throttle background operations (e.g., dataset refreshes) during capacity pressure:
  - [Surge Protection Announcement](https://blog.fabric.microsoft.com/en-US/blog/announcing-surge-protection-for-background-operation-is-generally-available-ga/)

---

## 2. Power BI Performance Optimization (Fabric Capacity)

This section outlines a structured approach to optimizing Power BI performance when running on Fabric Capacity.

---

### Step 1: Use the Fabric Capacity Metrics App

Start by identifying which reports, datasets, and users are consuming the most capacity.

- Install and configure the official **Fabric Capacity Metrics App**.
- Review:
  - Dataset memory usage
  - Query duration
  - Dataset refresh performance
  - Report usage trends

> [Install the Metrics App](https://learn.microsoft.com/es-es/fabric/enterprise/metrics-app)

![alt text](https://github.com/DavidArayaS/Microsoft-Fabric-Enterprise-Architecture-Power-BI-Performance-Optimization-Guide/blob/97b2404b9e1f9f990ebb80fc18d5f87309df16b2/Pictures/%7BB1B9A143-A3B2-4AE2-87C7-36841FC48D31%7D.png)
---

### Step 2: Identify High-Impact Reports

Using the Metrics App:
- Determine which reports and datasets are consuming the **most Compute Units (CUs)**.
- Prioritize those for optimization.

---

### Step 3: Analyze with Power BI Desktop Performance Analyzer

Open the identified reports in **Power BI Desktop** and run the **Performance Analyzer**.

This tool allows you to inspect:
- Time spent on DAX query execution
- Time spent rendering visuals
- Other processing and interaction delays

> [Using Performance Analyzer in Power BI Desktop](https://learn.microsoft.com/es-es/power-bi/create-reports/desktop-performance-analyzer)
>
> ![alt text](https://github.com/DavidArayaS/Microsoft-Fabric-Enterprise-Architecture-Power-BI-Performance-Optimization-Guide/blob/97b2404b9e1f9f990ebb80fc18d5f87309df16b2/Pictures/%7B2BB35744-A81D-4A74-8DFF-8F61A0F111C7%7D.png)

---

### Step 4: Use Best Practice and Memory Analyzer Tools

After identifying bottlenecks:
- Run the **Best Practice Analyzer** and **Memory Analyzer Notebooks** provided in Fabric for Power BI Premium workspaces.
- These tools help assess:
  - Inefficient DAX patterns
  - High-memory tables and columns
  - Relationships, data types, and column cardinality
  - Compression and storage distribution

Run these notebooks directly in Fabric to generate actionable diagnostics.
![alt text](https://github.com/DavidArayaS/Microsoft-Fabric-Enterprise-Architecture-Power-BI-Performance-Optimization-Guide/blob/97b2404b9e1f9f990ebb80fc18d5f87309df16b2/Pictures/%7B48CEABC2-E26D-46AB-BB59-A976C4BA0172%7D.png)

![alt text](https://github.com/DavidArayaS/Microsoft-Fabric-Enterprise-Architecture-Power-BI-Performance-Optimization-Guide/blob/97b2404b9e1f9f990ebb80fc18d5f87309df16b2/Pictures/%7B79575542-3886-456E-8F68-14AD06271658%7D.png)
![alt text](https://github.com/DavidArayaS/Microsoft-Fabric-Enterprise-Architecture-Power-BI-Performance-Optimization-Guide/blob/97b2404b9e1f9f990ebb80fc18d5f87309df16b2/Pictures/%7B96605045-6E95-4B73-A7FA-AECC1EDBA547%7D.png)

## Summary

| Focus Area               | Recommendation                                                                 |
|--------------------------|----------------------------------------------------------------------------------|
| Capacity Planning        | Choose right SKU; plan for manual scaling; isolate workloads when needed       |
| Workload Management      | Use separate capacities for Spark, BI, and Real-Time workloads                  |
| Workspace Strategy       | Organize by business domain; separate data engineering and BI workloads         |
| Lakehouse Architecture   | Implement Medallion pattern to enforce data quality and simplify lifecycle      |
| Notebook Optimization    | Apply Spark notebook best practices to reduce contention and resource usage     |
| Power BI Optimization    | Use Metrics App, Desktop Analyzer, and Fabric notebooks to optimize performance|
| Surge Protection         | Consider enabling for background task throttling during peak times             |
| Auto-scaling             | Not available — monitor and scale manually based on growth                      |

---

> Continuous performance optimization in Fabric starts with architecture. Design with scalability, workload isolation, and observability from the start.
