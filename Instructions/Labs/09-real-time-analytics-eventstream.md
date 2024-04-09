---
lab:
    title: 'Get started with Eventstream in Microsoft Fabric'
    module: 'Get started with Eventstream in Microsoft Fabric'
---
# Get started with Eventstream in Real-Time Analytics (RTA)

Eventstream is a feature in Microsoft Fabric that captures, transforms, and routes real-time events to various destinations with a no-code experience. You can add event data sources, routing destinations, and the event processor, when the transformation is needed, to the eventstream. Microsoft Fabric's EventStore is a monitoring option that maintains events from the cluster and provides a way to understand the state of your cluster or workload at a given point in time. The EventStore service can be queried for events that are available for each entity and entity type in your cluster. This means you can query for events on different levels, such as clusters, nodes, applications, services, partitions, and partition replicas. The EventStore service also has the ability to correlate events in your cluster. By looking at events that were written at the same time from different entities that may have impacted each other, the EventStore service can link these events to help with identifying causes for activities in your cluster. Another option for monitoring and diagnostics of Microsoft Fabric clusters is aggregating and collecting events using EventFlow.

This lab takes approximately **30** minutes to complete.

> **Note**: You need a [Microsoft Fabric trial](https://learn.microsoft.com/fabric/get-started/fabric-trial) to complete this exercise.

## Create a workspace

Before working with data in Fabric, create a workspace with the Fabric trial enabled.

1. Sign into [Microsoft Fabric](https://app.fabric.microsoft.com) at `https://app.fabric.microsoft.com` and select **Power BI**.
2. In the menu bar on the left, select **Workspaces** (the icon looks similar to &#128455;).
3. Create a new workspace with a name of your choice, selecting a licensing mode that includes Fabric capacity (*Trial*, *Premium*, or *Fabric*).
4. When your new workspace opens, it should be empty, as shown here:

   ![Screenshot of an empty workspace in Power BI.](./Images/new-workspace.png)
5. At the bottom left of the Power BI portal, select the **Power BI** icon and switch to the **Real-Time Analytics** experience.

## Scenario

With Fabric eventstreams, you can easily manage your event data in one place. You can collect, transform, and send real-time event data to different destinations in the format you want. You can also connect your eventstreams with Azure Event Hubs, KQL database, and Lakehouse without any hassle.

This lab is based on sample streaming data called Stock Market Data. The Stock Market sample data is a dataset of a stock exchange with a preset schema column such as time, symbol, price, volume and more. You'll use this sample data to simulate real-time events of stock prices and analyze them with various destinations, such as the KQL database.

Use Real-Time Analytics streaming and query capabilities to answer key questions about the stock statistics. In this scenario, we're going to take full advantage of the wizard instead of manually creating some components independently, such as the KQL Database.

In this tutorial, you'll learn how to:

- Create a KQL database
- Enable data copy to OneLake
- Create an eventstream
- Stream data from an eventstream to your KQL database
- Explore data with KQL and SQL

## Create a KQL Database

1. Within the **Real-Time Analytics**, select the **KQL Database** box.

   ![Image of choose kqldatabase](./Images/select-kqldatabase.png)

2. You'll be prompted to **Name** the KQL Database

   ![Image of name kqldatabase](./Images/name-kqldatabase.png)

3. Give the KQL Database a name that you'll remember, such as **MyStockData**, press **Create**.

1. In the **Database details** panel, select the pencil icon to turn on availability in OneLake.

   ![Image of enable onlake](./Images/enable-onelake-availability.png)

2. Make sure to toggle the button to **Active** and then select **Done**.

 > **Note:** You don't need to select a folder, Fabric will create it for you.

   ![Image of enable onelake toggle](./Images/enable-onelake-toggle.png)

## Create an Eventstream

1. In the menu bar, select **Real-Time Analytics** (the icon looks similar to ![rta logo](./Images/rta_logo.png))
2. Under **New**, select **EventStream (Preview)**

   ![Image of choose eventstream](./Images/select-eventstream.png)

3. You'll be prompted to **Name** your eventstream. Give the EventStream a name that you'll remember, such as **MyStockES**, press the **Create** button.

   ![Image of name eventstream](./Images/name-eventstream.png)

## Establish an eventstream source and destination

1. In the Eventstream canvas, select **New source** from the drop-down list, then select **Sample Data**.

   ![Image of EventStream canvas](./Images/real-time-analytics-canvas.png)

2. Enter the values for your Sample Data as shown in the following table and then select **Add**.

   | Field       | Recommended Value |
   | ----------- | ----------------- |
   | Source name | StockData         |
   | Sample data | Stock Market      |

3. Now add a destination by selecting **New destination** and then select **KQL Database**

   ![Image of EventStream destination](./Images/new-kql-destination.png)

4. In the KQL Database configuration use the following table to complete the configuration.

   | Field            | Recommended Value                              |
   | ---------------- | ---------------------------------------------- |
   | Destination Name | MyStockData                                    |
   | Workspace        | The workspace where you created a KQL database |
   | KQL Database     | MyStockData                                    |
   | Destination Table| MyStockData                                    |
   | Input data format| Json                                           |

3. Select **Add**.

> **Note**: Your data ingestion will begin immediately.

Wait for all the steps to be marked with green check marks. You should see the page title **Continuous ingestion from Eventsream established.** After that, select **Close** to return to your Eventstream page.

> **Note**: It may be necessary to refresh the page to view your table after the Eventstream connection has been built and established.

## KQL Queries

Kusto Query Language (KQL) is a read-only request to process data and return results. The request is stated in plain text, using a data-flow model that is easy to read, author, and automate. Queries always run in the context of a particular table or database. At a minimum, a query consists of a source data reference and one or more query operators applied in sequence, indicated visually by the use of a pipe character (|) to delimit operators. For more information on the Kusto Query Language, see [Kusto Query Language (KQL) Overview](https://learn.microsoft.com/en-us/azure/data-explorer/kusto/query/?context=%2Ffabric%2Fcontext%2Fcontext)

> **Note**: The KQL Editor comes with both syntax and Inellisense highlighting, which allows you to quickly gain knowledge of the Kusto Query Language (KQL).

1. Browse to your newly created and hydrated KQL Database named **MyStockData**.
2. In the Data tree, select the More menu [...] on the MyStockData table. Then select Query table > Show any 100 records.

   ![Image of KQL Query set](./Images/kql-query-sample.png)

3. The sample query opens in the **Explore your data** pane with the table context already populated. This first query uses the take operator to return a sample number of records, and is useful to get a first look at the data structure and possible values. The auto populated sample queries are automatically run. You can see the query results in the results pane.

   ![Image of KQL Query results](./Images/kql-query-results.png)

4. Return to the data tree to select the next query, which uses the where operator and between operator to return records ingested in the last 24 hours.

   ![Image of KQL Query Results last 24](./Images/kql-query-results-last24.png)

> **Note**: You may see a warning that you have exceeded query limits. This behavior will vary depending on the amount of data streamed into your database.

You can continue to navigate using the built-in query functions to familiarize yourself with your data.

## Sample SQL Queries

The query editor supports the use of T-SQL in addition to its primary query Kusto Query Language (KQL). T-SQL can be useful for tools that are unable to use KQL. For more information, see [Query data using T-SQL](https://learn.microsoft.com/en-us/azure/data-explorer/t-sql)

1. Back In the Data tree, select the **More menu** [...] on the MyStockData table. Select **Query table > SQL > Show any 100 records**.

   ![Image of sql query sample](./Images/sql-query-sample.png)

2. Place your cursor somewhere within the query and select **Run** or press **Shift + Enter**.

   ![Image of sql query results](./Images/sql-query-results.png)

You can continue to navigate using the build-in functions and familiarize yourself with the data using SQL or KQL. This ends the lesson.

## Clean up resources

In this exercise, you have created a KQL database and set up continuous streaming with eventstream. After that you queried the data using KQL and SQL. When you've finished exploring your KQL database, you can delete the workspace you created for this exercise.
1. In the bar on the left, select the icon for your workspace to view all of the items it contains.
2. In the workspace page, select **Workspace settings**.
3. At the bottom of the **General** section, select **Remove this workspace**.
