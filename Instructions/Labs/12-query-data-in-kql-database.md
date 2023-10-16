---
lab:
    title: 'Query data in KQL database'
    module: 'Query data from a Kusto Query database in Microsoft Fabric'
---
# Get started with querying a Kusto database in Microsoft Fabric
A KQL Queryset is a tool that allows you to execute queries, modify, and display query results from a KQL database. You can link each tab in the KQL Queryset to a different KQL database, and save your queries for future use or share them with others for data analysis. You can also switch the KQL database for any tab, so you can compare the query results from different data sources.

The KQL Queryset uses the Kusto Query language, which is compatible with many SQL functions, to create queries. To learn more about the [kusto query (KQL)language](https://learn.microsoft.com/en-us/azure/data-explorer/kusto/query/?context=%2Ffabric%2Fcontext%2Fcontext), 

This lab will take approximately **25** minutes to complete.

## Create a workspace

Before working with data in Fabric, create a workspace with the Fabric trial enabled.

1. Sign into [Microsoft Fabric](https://app.fabric.microsoft.com) at `https://app.fabric.microsoft.com` and select **Power BI**.
2. In the menu bar on the left, select **Workspaces** (the icon looks similar to &#128455;).
3. Create a new workspace with a name of your choice, selecting a licensing mode that includes Fabric capacity (*Trial*, *Premium*, or *Fabric*).
4. When your new workspace opens, it should be empty, as shown here:

    ![Screenshot of an empty workspace in Power BI.](./Images/new-workspace.png)

In this lab, you´ll use the Real-Time Analytics (RTA) in Fabric to create a KQL database from a sample eventstream. Real-Time Analytics  conveniently provides a sample dataset that you can use to explore RTA's capabilities. You´ll use this sample data to create KQL | SQL queryes and querysets that analyzes some real-time data and allow for additional use in downstream processes.

## Create a KQL Database

1. Within the **Real-Time Analytics**, select the **KQL Database** box.

   ![Image of choose kqldatabase](./Images/select-kqldatabase.png)

2. You'll be prompted to **Name** the KQL Database

   ![Image of name kqldatabase](./Images/name-kqldatabase.png)

3. Give the KQL Database a name that you'll remember, such as **MyStockData**, press **Create**.

4. In the **Database details** panel, select the pencil icon to turn on availability in OneLake.

   ![Image of enable onelake](./Images/enable-onelake-availability.png)

5. Select **sample data** box from the options of ***Start by getting data***.
 
   ![Image of selection options with sample data highlighted](./Images/load-sample-data.png)

6. choose the **Metrics analytics** box from the options for sample data.

   ![Image of choosing analytics data for lab](./Images/create-sample-data.png)

7. Once the data is loaded, verfiy the data is loaded into the KQL database. You can accomplish this by selecting the elipses to the right of the table, navigating to **Query table** and selecting **Show any 100 records**.

    ![Image of selecting the top 100 files from the RawServerMetrics table](./Images/rawservermetrics-top-100.png)

> **NOTE**: The first time you run this, it can take several seconds to allocate compute resources.

## Scenario
In this scenario, you're an analyst that's tasked with querying a sample dataset of raw metrics from a hypothetical SQL Serverthat you will implement from the Fabric environment. You use KQL and T-SQL to query this data and gather information in order to gain informational insights about the data.

