---
lab:
    title: 'Create and use Dataflows (Gen2) in Microsoft Fabric'
    module: 'Use Dataflows (Gen2) in Microsoft Fabric'
---


# Create a Dataflow (Gen2) in Microsoft Fabric

In Microsoft Fabric, Dataflows (Gen2) connect to various data sources and perform transformations in Power Query Online. They can then be used in Data Pipelines or Power BI report development to increase code structure and data consistency.

**The estimated time to complete the lab is 30 minutes**

In this lab, you will create a Dataflow (Gen2) to connect to multiple data sources, transform the data, and curate a homogenized dataset for the business to consume.

In this lab, you learn how to:

- Use Power Query Online to develop a dataflow.

- Use Power BI Desktop to consume a dataflow.

## Before you start

You'll need a Power BI Premium subscription with access to the Microsoft Fabric preview.

## Create a workspace

Before working with data in Fabric, you will need a Premium capacity workspace.

1. Sign into your Power BI service at [https://app.powerbi.com](https://app.powerbi.com).
1. In the menu bar on the left, select **Workspaces** (the icon looks similar to &#128455;).
1. Create a new workspace with a name of your choice, selecting the **Premium per user** licensing mode.
1. When your new workspace opens, it should be empty, as shown here:

    ![Screenshot of an empty workspace in Power BI.](./Images/new-workspace.png)

### Download data

1. Download and extract the data files for this exercise from [https://github.com/MicrosoftLearning/dp-data/raw/main/orders.zip](https://github.com/MicrosoftLearning/dp-data/raw/main/orders.zip).
1. After extracting the zipped archive, verify that you have a folder named **orders** that contains CSV files named **2019.csv**, **2020.csv**, and **2021.csv**.

## Develop a dataflow

In this exercise, you will develop a dataflow to support Power BI model development. It will provide a consistent representation of the data warehouse date dimension table.

### Create a dataflow

In this task, you will create a dataflow << finish thought >>.

1. In the Power BI service, select **New**, **Dataflow**.

	![](../images/dp500-create-a-dataflow-image10.png)

1. To create a dataflow, select the **Dataflows** tile.

	![](../images/dp500-create-a-dataflow-image11.png)

1. In the **Define new tables** tile, select **Add new tables**.

	![](../images/dp500-create-a-dataflow-image12.png)

	*Adding new tables involves using Power Query Online to define queries.*

1. To choose a data source, select **Azure Synapse Analytics (SQL DW)**.

	![](../images/dp500-create-a-dataflow-image13.png)

	*Tip: You can use the Search box (located at the top-right) to help find the data source.*

1. Enter the Synapse Connection settings.

     - Enter the Server name from the Azure Portal
     
     ![](../images/synapse-sql-pool-connection-string.png)
     
      The Server name should look similar to:
      
      synapsewsxxxxx.sql.azuresynapse.net
      
     - Ensure the Authentication kind is **Organizational account**. If you are prompted to sign in, use the lab provided credentials.
     ![](../images/synapse-sql-pool-sign-in.png)

1. At the bottom-right, select **Next**.

	![](../images/dp500-create-a-dataflow-image14.png)

1. In the Power Query navigation pane, expand the sqldw and select (do not check) the **DimDate** table.

	![](../images/dp500-create-a-dataflow-image15.png)

1. Notice the preview of table data.

1. To create a query, check the **DimDate** table.

	![](../images/dp500-create-a-dataflow-image16.png)

1. At the bottom-right, select **Transform data**.

	![](../images/dp500-create-a-dataflow-image17.png)

	*Power Query Online will now be used to apply transformations to the table. It provides an almost-identical experience to the Power Query Editor in Power BI Desktop.*

1. In the **Query Settings** pane (located at the right), to rename the query, in the **Name** box, replace the text with **Date**, and then press **Enter**.

	![](../images/dp500-create-a-dataflow-image18.png)

1. To remove unnecessary columns, on the **Home** ribbon tab, from inside the **Manage Columns** group, select the **Choose Columns** icon.

	![](../images/dp500-create-a-dataflow-image19.png)

1. In the **Choose Columns** window, to uncheck all checkboxes, uncheck the first checkbox.

	![](../images/dp500-create-a-dataflow-image20.png)


1. Check the following five columns.

	- DateKey

	- FullDateAlternateKey

	- MonthNumberOfYear

	- FiscalQuarter

	- FiscalYear

	![](../images/dp500-create-a-dataflow-image21.png)

1. Select **OK**.

	![](../images/dp500-create-a-dataflow-image22.png)

  
1. In the **Query Settings** pane, in the **Applied Steps** list, notice that a step was added to remove other columns.

	![](../images/dp500-create-a-dataflow-image23.png)

	*Power Query defines steps to achieve the desired structure and data. Each transformation is a step in the query logic.*

1. To rename the **FullDateAlternateKey** column, double-click the **FullDateAlternateKey** column header.

1. Replace the text with **Date**, and then press **Enter**.

	![](../images/dp500-create-a-dataflow-image24.png)

1. To add a calculated column, on the **Add Column** ribbon tab, from inside the **General** group, select **Custom Column**.

	![](../images/dp500-create-a-dataflow-image25.png)

   

1. In the **Custom column** window, in the **New column name** box, replace the text with **Year**.

1. In the **Data type** dropdown list, select **Text**.

	![](../images/dp500-create-a-dataflow-image26.png)

1. In the **Custom column formula** box, enter the following formula:

	*Tip: All formulas are available to copy and paste from the **D:\DP500\Allfiles\05\Assets\Snippets.txt**.*


	```
	"FY" & Number.ToText([FiscalYear])
	```


1. Select **OK**.

	*You will now add four more custom columns.*

1. Add another custom column named **Quarter** with the **Text** data type, using the following formula:


	```
	[Year] & " Q" & Number.ToText([FiscalQuarter])
	```


1. Add another custom column named **Month** with the **Text** data type, using the following formula:


	```
	Date.ToText([Date], "yyyy-MM")
	```

1. Add another custom column named **Month Offset** (include a space between the words) with the **Whole number** data type, using the following formula:


	```
	((Date.Year([Date]) * 12) + Date.Month([Date])) - ((Date.Year(DateTime.LocalNow()) * 12) + Date.Month(DateTime.LocalNow()))
	```


	*This formula determines the number of months from the current month. The current month is zero, past months are negative, and future months are positive. For example, last month has a value of -1.*

   

1. Add another custom column named **Month Offset Filter** (include spaces between the words) with the **Text** data type, using the following formula:


	```
	if [Month Offset] > 0 then Number.ToText([Month Offset]) & " month(s) future"

	else if [Month Offset] = 0 then "Current month"

	else Number.ToText(-[Month Offset]) & " month(s) ago"
	```


	*This formula transposes the numeric offset to a friendly text format.*

	*Tip: All formulas are available to copy and paste from the **D:\DP500\Allfiles\05\Assets\Snippets.txt**.*

1. To remove unnecessary columns, on the **Home** ribbon tab, from inside the **Manage Columns** group, select the **Choose Columns** icon.

	![](../images/dp500-create-a-dataflow-image27.png)

1. In the **Choose Columns** window, to uncheck the following columns:

	- MonthNumberOfYear

	- FiscalQuarter

	- FiscalYear

	![](../images/dp500-create-a-dataflow-image28.png)

1. Select **OK**.

1. At the bottom-right, select **Save &amp; close**.

	![](../images/dp500-create-a-dataflow-image29.png)

1. In the **Save your dataflow** window, in the **Name** box, enter **Corporate Date**.

1. In the **Description** box, enter: **Consistent date definition for use in all Adventure Works datasets**

1. Tip: The description is available to copy and paste from the **D:\DP500\Allfiles\05\Assets\Snippets.txt**.

	![](../images/dp500-create-a-dataflow-image30.png)

1. Select **Save**.

	![](../images/dp500-create-a-dataflow-image31.png)

1. In the Power BI service, in the **Navigation** pane, select your workspace name.

	*This action opens the landing page for the workspace.*

1. To refresh the dataflow, hover the cursor over the **Corporate Date** dataflow, and then select the **Refresh now** icon.

	![](../images/dp500-create-a-dataflow-image32.png)

  

1. To go to the dataflow settings, hover the cursor over the **Corporate Date** dataflow, select the ellipsis, and then select **Settings**.

	![](../images/dp500-create-a-dataflow-image33.png)

1. Notice the configuration options.

	![](../images/dp500-create-a-dataflow-image34.png)

	*There are two settings that should be configured. First, scheduled refresh should be configured to update the dataflow data every day. That way, the month offsets will be calculated using the current date. Second, the dataflow should be endorsed as certified (by an authorized reviewer). A certified dataflow declares to others that it meets quality standards and can be regarded as reliable and authoritative.*

	*In addition to configuring settings, permission should be granted to all content creators to consume the dataflow.*

## Consume a dataflow

In this exercise, in the Power BI Desktop solution, you will replace the existing **Date** table with a new table that sources its data from the dataflow.

### Remove the original Date table

In this task, you will remove the original **Date** table.

1. Switch to the Power BI Desktop solution.

1. In the model diagram, right-click the **Date** table, and then select **Delete from model**.

	![](../images/dp500-create-a-dataflow-image35.png)

1. When prompted to delete the table, select **OK**.

	![](../images/dp500-create-a-dataflow-image36.png)

  


### Add a new Date table

In this task, you will add a new **Date** table that sources its data from the dataflow.

1. On the **Home** ribbon, from inside the **Data** group, select the **Get data** icon.

	![](../images/dp500-create-a-dataflow-image37.png)

1. In the **Get Data** window, at the left, select **Power Platform**, and then select **Power BI dataflows**.

	![](../images/dp500-create-a-dataflow-image38.png)

1. Select **Connect**.

	![](../images/dp500-create-a-dataflow-image39.png)

  

1. In the **Power BI dataflows** window, select **Sign in**.

	![](../images/dp500-create-a-dataflow-image40.png)

1. Use the lab credentials to complete the sign in process.

	*Important: You must use the same credentials used to sign in to the Power BI service.*

1. Select **Connect**.

	![](../images/dp500-create-a-dataflow-image41.png)

1. In the **Navigator** window, in the left pane, expand your workspace folder, and then expand the **Corporate Date** dataflow folder.

	![](../images/dp500-create-a-dataflow-image42.png)


1. Check the **Date** table.

	![](../images/dp500-create-a-dataflow-image43.png)

1. Select **Load**.

	![](../images/dp500-create-a-dataflow-image44.png)

	*It is possible to transform the data using the Power Query Editor.*

1. When the new table is added to the model, create a relationship by dragging the **DateKey** column from the **Date** table to the **OrderDateKey** column of the **Sales** table.

	![](../images/dp500-create-a-dataflow-image45.png)

	*There are many other model configurations, like hiding columns or creating a hierarchy, that can be done.*

### Validate the model

In this task, you will test the model by creating a simple report layout.

1. At the left, switch to **Report** view.

	![](../images/dp500-create-a-dataflow-image46.png)

1. To add a visual to the page, in the **Visualizations** pane, select the stack bar chart visual.

	![](../images/dp500-create-a-dataflow-image47.png)

1. Resize the visual to fill the report page.

  

1. In the **Fields** pane, expand the **Date** table, and then drag the **Month Offset Filter** field into the bar chart visual.

	![](../images/dp500-create-a-dataflow-image48.png)

1. In the **Fields** pane, expand the **Sales** table, and then drag the **Sales Amount** field into the bar chart visual.

	![](../images/dp500-create-a-dataflow-image49.png)


1. To sort the vertical axis, at the top-right of the visual, select the ellipsis, and then select **Sort axis** > **Month Offset Filter**.

	![](../images/dp500-create-a-dataflow-image50.png)

1. To ensure the month offset filter values sort chronologically, in the **Fields** pane, select the **Month Offset Filter** field.

1. On the **Column Tools** ribbon tab, from inside the **Sort** group, select **Sort**, and then select **Month Offset**.

	![](../images/dp500-create-a-dataflow-image51.png)

1. Review the updated bar chart visual that now sorts chronologically.

	*The main benefit of using date offset columns is that reports can filter by relative dates in a customized way. (Slicers and filters and also filter by relative date and time periods, but this behavior cannot be customized. They also don't allow filtering by quarters.)*

1. Save the Power BI Desktop file.

1. Close Power BI Desktop.

### Pause the SQL pool

In this task, you will stop the SQL pool.

1. In a web browser, go to [https://portal.azure.com](https://portal.azure.com/).

1. Locate the SQL pool.

1. Pause the SQL pool.
