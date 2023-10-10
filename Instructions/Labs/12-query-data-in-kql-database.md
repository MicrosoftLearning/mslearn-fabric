---
lab:
    title: 'Query data in KQL database'
    module: 'Query data from a Kusto Query database in Microsoft Fabric'
---
# Get started with querying a Kusto database in Microsoft Fabric
A KQL Queryset is a tool that allows you to execute queries, modify, and display query results from a KQL database. You can link each tab in the KQL Queryset to a different KQL database, and save your queries for future use or share them with others for data analysis. You can also switch the KQL database for any tab, so you can compare the query results from different data sources.

The KQL Queryset uses the Kusto Query language, which is compatible with many SQL functions, to create queries. To learn more about the [kusto query (KQL)language](https://learn.microsoft.com/en-us/azure/data-explorer/kusto/query/?context=%2Ffabric%2Fcontext%2Fcontext), 

## Create a workspace

Before working with data in Fabric, create a workspace with the Fabric trial enabled.

1. Sign into [Microsoft Fabric](https://app.fabric.microsoft.com) at `https://app.fabric.microsoft.com` and select **Power BI**.
2. In the menu bar on the left, select **Workspaces** (the icon looks similar to &#128455;).
3. Create a new workspace with a name of your choice, selecting a licensing mode that includes Fabric capacity (*Trial*, *Premium*, or *Fabric*).
4. When your new workspace opens, it should be empty, as shown here:

    ![Screenshot of an empty workspace in Power BI.](./Images/new-workspace.png)

In this lab, you´ll use the Data Activator in Fabric to create a *reflex*. Data Activator conveniently provides a sample dataset that you can use to explore Data Activator's capabilities. You´ll use this sample data to create a *reflex* that analyzes some real-time data and creates a trigger to send an email out when a condition is met.


## Scenario
In this scenario, you're an analyst that's tasked with querying a

A Kusto query is a way to read data, process it, and show the results. The query is written in plain text that is easy to work with. A Kusto query can have one or more statements that show data as a table or a graph.

A table statement has some operators that work on table data. Each operator takes a table as input and gives a table as output. Operators are joined by a pipe (|). Data moves from one operator to another. Each operator changes the data in some way and passes it on.

You can imagine it like a funnel, where you start with a whole table of data. Each operator filters, sorts, or summarizes the data. The order of the operators matters because they work one after another. At the end of the funnel, you get a final output.

These operators are specific to KQL, but they may be similar to SQL or other languages.