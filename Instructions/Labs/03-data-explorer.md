---
lab:
    title: 'Analyze data with Kusto'
    module: 'Use Kusto in Trident'
---

# Analyze data with Kusto

*Trident* provides a runtime that you can use to store and query data by using Kusto Query Language (KQL). Kusto is optimized for data that includes a time series component, such as realtime data from log files or IoT devices.

This lab will take approximately **30** minutes to complete.

## Before you start

You'll need a Power BI Premium subscription with access to the Trident preview.

## Create a workspace

Before working with data in *Trident*, you should create a workspace with support for premium features.

1. Sign into your Power BI service at [https://app.powerbi.com](https://app.powerbi.com).
2. In the menu bar on the left, select **Workspaces** (the icon looks similar to &#128455;).
3. Create a new workspace with a name of your choice, selecting the **Premium per user** licensing mode.
4. When your new workspace opens, it should be empty, as shown here:

    ![Screenshot of an empty workspace in Power BI.](./Images/new-workspace.png)

## Create a lakehouse and upload files

Now that you have a workspace, it's time to switch to the *Data engineering* experience in the portal and create a data lakehouse for the data files you're going to analyze.

1. At the bottom left of the Power BI portal, select the **Power BI** icon and switch to the **Data engineering** experience, as shown here:

    ![Screenshot of the experience menu in Power BI.](./Images/data-engineering.png)

2. In the **Data engineering** home page, create a new **Lakehouse** with a name of your choice.

    After a minute or so, a new lakehouse with no **Tables** or **Files** will be created. You need to ingest some data into the data lakehouse for analysis. There are multiple ways to do this, but in this exercise you'll simply download and extract a folder of text files your local computer and then upload them to your lakehouse.

3. Download the data file for this exercise from [https://raw.githubusercontent.com/MicrosoftLearning/dp-data/main/sales.csv](https://raw.githubusercontent.com/MicrosoftLearning/dp-data/main/sales.csv), saving it as **sales.csv** on your local computer
4. Return to the web browser tab containing your lakehouse, and in the **...** menu for the **Files** node in the **Lake view** pane, select **Upload** and **Upload file**, and then upload the **sales.csv** file from your local computer to the lakehouse.
5. After the file has been uploaded, select **Files** verify that the **sales.csv** file has been uploaded, as shown here:

    ![Screenshot of uploaded sales.csv file in a lakehouse.](./Images/uploaded-file.png)

6. In the **...** menu for the **sales.csv** file, select **Properties**. Then copy the **URL** for your file to the clipboard - you will use this later.

## Create a KQL database

Kusto query language (KQL) is used to query static or streaming data in a table that is defined in a KQL database. To analyze the sales data you uploaded to your lakehouse, you must create a table in a KQL database and ingest the data from the file.

1. At the bottom left of the Power BI portal, select the **Data engineering** icon and switch to the **Kusto** experience.
2. On the **Home** page for the Kusto experience, select **KQL database** and create a new database with a name of your choice.
3. When the new database has been created, select the option to get data from **OneLake**. Then use the wizard to import the data into a new table by selecting the following options:
    - **Destination**:
        - **Database**: *The database you just created is already selected*
        - **Table**: *Create a new table named* **sales**.
    - **Source**:
        - **Source type**: OneLake
        - **Link to source**: *Paste the URL path to your sales.csv file, which you copied to the clipboard previously*
    - **Schema**:
        - **Compression type**: Uncompressed
        - **Data format**: CSV
        - **Ignore the first record**: *Selected*
    - **Summary**:
        - *Review the preview of the table and close the wizard.*

> **Note**: In this example, you imported a very small amount of static data from a file, which is fine for the purposes of this exercise. In reality, you can use Kusto to analyze much larger volumes of data; including real-time data from a streaming source such as Azure Event Hubs.

## Use KQL to query the sales table

Now that you have a table of data in your database, you can use KQL code to query it.

1. Select **Quick query** to open the query editor pane. The query editor contains some code comments as shown here:

    ```sql
    //***********************************************************************************************************
    //Recommended reading:
    //KQL reference guide - https://docs.microsoft.com/en-us/azure/data-explorer/kql-quick-reference
    //SQL - KQL conversions - https://docs.microsoft.com/en-us/azure/data-explorer/kusto/query/sqlcheatsheet
    //***********************************************************************************************************
    ```

2. Under the existing comments, add the following code:

    ```kusto
    sales
    | take 1000
    ```

3. Use the **&#9655; Run** button to run the query and review the results, which contain the first 1000 rows of data from the **sales** table.

4. Modify the query as follows:

    ```kusto
    sales
    | where Item == 'Road-250 Black, 48'
    ```

5. Run the query. Then review the results, which should contain only the rows for sales orders for the *Road-250 Black, 48* product.

6. Modify the query as follows:

    ```kusto
    sales
    | where Item == 'Road-250 Black, 48'
    | where datetime_part('year', OrderDate) > 2020
    ```

7. Run the query and review the results, which should contain only sales orders for *Road-250 Black, 48* made after 2020.

8. Modify the query as follows:

    ```kusto
    sales
    | where OrderDate between (datetime(2020-01-01 00:00:00) .. datetime(2020-12-31 23:59:59))
    | summarize TotalNetRevenue = sum(UnitPrice) by Item
    | sort by Item asc
    ```

9. Run the query and review the results, which should contain the total net revenue for each product between January 1st and December 31st 2020 in ascending order of product name.

10. Select **Save as query set** and save the query as **Revenue by Product**.

## Create a Power BI report from a KQL query set

You can use your KQL query set as the basis for a Power BI report.

1. In the query workbench editor for your query set, run the query and wait for the results.
2. Select **Build Power BI report** and wait for the report to be generated.
3. Review the preview of the report, then save it as **Sales Revenue By Product.pbix** in your **My Workspace** workspace.
4. At the bottom left of the Power BI portal, select the **Kusto** icon and switch to the **Power BI** experience.
5. Use the **Workspaces** icon to switch to **My Workspace**.
6. In your workspace, find the **Sales Revenue By Product** dataset.
7. Create a report from the dataset, using the **Auto-create** option, and view the report.

