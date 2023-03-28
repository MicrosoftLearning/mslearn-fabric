---
lab:
    title: 'Create a dataflow'
    module: 'Prepare data for tabular models in Power BI'
---

# Create a dataflow

## Overview

**The estimated time to complete the lab is 45 minutes**

In this lab, you will create a dataflow to deliver date dimension data sourced from the Azure Synapse Adventure Works data warehouse. The dataflow will provide a consistent definition of date-related data for use by the organization's business analysts.

In this lab, you learn how to:

- Use Power Query Online to develop a dataflow.

- Use Power BI Desktop to consume a dataflow.

## Get started

In this exercise, you will prepare your environment.

### Load data into Azure Synapse Analytics

   > **Note**: If you have already loaded data into Azure Synapse Analytics using a git clone, you can skip this task and proceed to **Set up Power BI.**

1. Sign into the [Azure portal](https://portal.azure.com) using the login information located on the Resources tab on the right side of the VM.
2. Use the **[\>_]** button to the right of the search bar at the top of the page to create a new Cloud Shell in the Azure portal, selecting a ***PowerShell*** environment and creating storage if prompted. The cloud shell provides a command line interface in a pane at the bottom of the Azure portal, as shown here:

    ![Azure portal with a cloud shell pane](../images/cloud-shell.png)

    > **Note**: If you have previously created a cloud shell that uses a *Bash* environment, use the the drop-down menu at the top left of the cloud shell pane to change it to ***PowerShell***.

3. Note that you can resize the cloud shell by dragging the separator bar at the top of the pane, or by using the **&#8212;**, **&#9723;**, and **X** icons at the top right of the pane to minimize, maximize, and close the pane. For more information about using the Azure Cloud Shell, see the [Azure Cloud Shell documentation](https://docs.microsoft.com/azure/cloud-shell/overview).

4. In the PowerShell pane, enter the following command to clone this repo:

    ```
    rm -r dp500 -f
    git clone https://github.com/MicrosoftLearning/DP-500-Azure-Data-Analyst dp500
    ```

5. After the repo has been cloned, enter the following commands to change to the **setup** folder and run the **setup.ps1** script it contains:

    ```
    cd dp500/Allfiles/04
    ./setup.ps1
    ```

6. When prompted, enter a suitable password to be set for your Azure Synapse SQL pool.

    > **Note**: Be sure to remember this password!

7. Wait for the script to complete - this typically takes around 20 minutes; but in some cases may take longer.

1. After creating the Synapse workspace and SQL Pool and loading the data, the script pauses the pool to prevent unnecessary Azure charges. When you're ready to work with your data in Azure Synapse Analytics, you'll need to resume the SQL Pool.

### Clone the repository for this course

1. On the start menu, open the Command Prompt

    ![](../images/command-prompt.png)

1. In the command prompt window, navigate to the D drive by typing:

    `d:` 

   Press enter.

    ![](../images/command-prompt-2.png)


1. In the command prompt window, enter the following command to download the course files and save them to a folder called DP500.
    
    `
    git clone https://github.com/MicrosoftLearning/DP-500-Azure-Data-Analyst DP500
    `
   
2. When the repository has been cloned, close the command prompt window. 
   
3. Open the D drive in the file explorer to ensure the files have been downloaded.

### Set up Power BI Desktop

In this task, you will set up Power BI Desktop.

1. To open File Explorer, on the taskbar, select the **File Explorer** shortcut.

	![](../images/dp500-create-a-dataflow-image1.png)

1. Go to the **D:\DP500\Allfiles\05\Starter** folder.

1. To open a pre-developed Power BI Desktop file, double-click the **Sales Analysis - Create a dataflow.pbix** file.

1. If you're not already signed in, at the top-right corner of Power BI Desktop, select **Sign In**. Use the lab credentials to complete the sign in process.

	![](../images/dp500-create-a-dataflow-image2.png)

1. To save the file, on the **File** ribbon, select **Save as**.

1. In the **Save As** window, go to the **D:\DP500\Allfiles\05\MySolution** folder.

1. Go to Power BI Desktop and select **File** then **Options and settings** then **Options** then **Security** and under Authentication Browser check **Use my default web browser** and select **Save**.

	*You will update the Power BI Desktop solution to use a dataflow to source date dimension data.*

### Sign in to the Power BI service

In this task, you will sign in to the Power BI service, start a trial license, and create a workspace.

*Important: If you have already setup Power BI in your VM environment, continue to the next task.*

1. In a web browser, go to [https://powerbi.com](https://powerbi.com/).

1. Use the lab credentials to complete the sign in process.

	*Important: You must use the same credentials used to sign in from Power BI Desktop.*

1. At the top-right, select the profile icon, and then select **Start trial**.

	![](../images/dp500-create-a-dataflow-image3.png)

1. When prompted, select **Start trial**.

	![](../images/dp500-create-a-dataflow-image4.png)

1. Do any remaining tasks to complete the trial setup.

	*Tip: The Power BI web browser experience is known as the **Power BI service**.*

  

### Create a workspace

In this task, you will create a workspace.

1. In the Power BI service, to create a workspace, in the **Navigation** pane (located at the left), select **Workspaces**, and then select **Create workspace**.

	![](../images/dp500-create-a-dataflow-image5.png)


1. In the **Create a workspace** pane (located at the right), in the **Workspace name** box, enter a name for the workspace.

	*The workspace name must be unique within the tenant.*

	![](../images/dp500-create-a-dataflow-image6.png)

1. Select **Save**.

	![](../images/dp500-create-a-dataflow-image7.png)

	*Once created, the workspace is opened. In a later exercise, you will create a dataflow for this workspace.*

### Start the SQL pool

In this task, you will start the SQL pool.

1. In a web browser, go to [https://portal.azure.com](https://portal.azure.com/).

1. Use the lab credentials to complete the sign in process.

1. Use the search bar to locate Azure Synapse Analytics. 

1. Select the Azure Synapse Analytics instance.
    ![](../images/synapse-instance.png)

1. Locate and select the dedicated SQL pool.
    ![](../images/dedicated-sql-pool.png)

1. Resume the dedicated SQL pool.

	![](../images/resume-sql-pool.png)

	*Important: The SQL pool is a costly resource. Please limit the use of this resource when working on this lab. The final task in this lab will instruct you to pause the resource.*

## Develop a dataflow

In this exercise, you will develop a dataflow to support Power BI model development. It will provide a consistent representation of the data warehouse date dimension table.

### Review the data model

In this task, you will review the data model developed in Power BI Desktop.

1. Switch to the Power BI Desktop solution.

1. At the left, switch to **Model** view.

	![](../images/dp500-create-a-dataflow-image8.png)

1. In the model diagram, notice the **Date** table.

	![](../images/dp500-create-a-dataflow-image9.png)

	*The **Date** table was created by the business analyst. It doesn't represent a consistent definition of date-related data, and it does not include helpful offset columns to support relative date filters. In a later exercise, you will replace this table with a new table sourced from a dataflow.*

### Create a dataflow

In this task, you will create a dataflow that represents a consistent definition of date-related data.

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
