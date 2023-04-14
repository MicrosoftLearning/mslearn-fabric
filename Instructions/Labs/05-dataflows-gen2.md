---
lab:
    title: 'Create and use Dataflows (Gen2) in Microsoft Fabric'
    module: 'Ingest Data with Dataflows Gen2 in Fabric'
---


# Create a Dataflow (Gen2) in Microsoft Fabric

In Microsoft Fabric, Dataflows (Gen2) connect to various data sources and perform transformations in Power Query Online. They can then be used in Data Pipelines to ingest data into a lakehouse or other analytical store, or to define a dataset for a Power BI report.

## Before you start

You'll need a Power BI Premium subscription with access to the Microsoft Fabric preview. This lab is designed to introduce the different elements of Dataflows (Gen2), and not create a complex solution that may exist in an enterprise. This lab will take **approximately 30 minutes** to complete.

## Create a workspace

Before working with data in Fabric, create a workspace with premium capacity enabled.

1. Sign into your Power BI service at [https://app.powerbi.com](https://app.powerbi.com).
2. In the menu bar on the left, select **Workspaces** (the icon looks similar to &#128455;).
3. Create a new workspace with a name of your choice, selecting the **Premium per user** licensing mode.
4. When your new workspace opens, it should be empty, as shown here:

    ![Empty workspace in Power BI.](./Images/new-workspace.png)

## Create a lakehouse

Now that you have a workspace, it's time to switch to the **Data Engineering** workload in the portal and create a data lakehouse into which you will ingest data.

1. At the bottom left of the Power BI portal, select the **Power BI** icon and switch to the **Data Factory** experience, as shown here:

    ![Workload experience menu in Power BI.](./Images/data-engineering.png)

2. In the **Data engineering** home page, create a new **Lakehouse** with a name of your choice.

    After a minute or so, a new empty lakehouse will be created.

	![New lakehouse.](./Images/new-lakehouse.png)

## Create a Dataflow (Gen2) to ingest data

Now that you have a lakehouse, you need to ingest some data into it. One way to do this is to define a dataflow that encapsulates an *extract, transform, and load* (ETL) process.

1. In the home page for your lakehouse, select **New Dataflow Gen2**. After a few seconds, the Power Query editor for your new dataflow opens as shown here.

	![New dataflow.](./Images/new-dataflow.png)

2. Select **Import from a Text/CSV file**, and create a new data source with the following settings:
	- **Link to file**: *Selected*
	- **File path or URL**: `https://raw.githubusercontent.com/MicrosoftLearning/dp-data/main/orders.csv`
	- **Connection**: Create new connection
	- **data gateway**: (none)
	- **Authentication kind**: Anonymous

3. Select **Next** to preview the file data, and then create the data source. The Power Query editor will show the data source and an initial set of query steps to format the data, as shown here:

	![Query in the Power Query editor.](./Images/power-query.png)

4. On the toolbar ribbon, select the **Add column** tab. Then select **Custom column** and create a new column named **MonthNo** that contains a whole number based on the formula `Date.Month([OrderDate])` - as shown here:

	![Custom column in Power Query editor.](./Images/custom-column.png)

	The step to add the custom column is added to the query and the resulting column is displayed in the data pane:

	![Query with a custom column step.](./Images/custom-column-added.png)

> [!TIP]
> In the Query Settings pane on the right side, notice the **Applied Steps** include each transformation step. At the bottom, you can also toggle the **Diagram flow** button to turn on the Visual Diagram of the steps.
>
> Steps can be moved up or down, edited by selecting the gear icon, and you can select each step to see the transformations apply in the preview pane.

![Applied steps pane with example steps.](Images/pq-applied-steps.png)

## Add data destination for Dataflow

1. On the toolbar ribbon, select the **Home** tab. Then in the **Add data destination** drop-down menu, select **Lakehouse**.

6. In the **Connect to data destination** dialog box, edit the connection and sign in using your Power BI organizational account to set the identity that the dataflow will use to access the lakehouse.

	![Data destination configuration page.](./Images/dataflow-connection.png)

7. Select **Next** and in the list of available workspaces, find your workspace and select the lakehouse you created in it at the start of this exercise. Then specify a new table named **orders**:

	![Data destination configuration page.](./Images/data-destination-target.png)

    > **Note**: On the **Destination settings** page, notice how OrderDate and MonthNo are not selected in the Column mapping and there is an informational message: *Change to date/time*.

	![Data destination settings page.](./Images/destination-settings.png)

1. Cancel this action, then go back OrderDate and MonthNo columns. Right-click on the column header and **Change Type**.

    - OrderDate = Date/Time

    - MonthNo = Whole number

1. Now repeat the process outlined earlier to add a lakehouse destination.

8. On the **Destination settings** page, select **Append**, and then save the settings.

	The **Lakehouse** destination is indicated as an icon in the query in the Power Query editor.

	![Query with a lakehouse destination.](./Images/lakehouse-destination.png)

9. Select **Publish** to publish the dataflow. Then wait for the **Dataflow 1** dataflow to be created in your workspace.

1. Once published, you can right-click on the dataflow in your workspace, select **Properties**, and rename your dataflow.

## Add a dataflow to a pipeline

You can include a dataflow as an activity in a pipeline. Pipelines are used to orchestrate data ingestion and processing activities, enabling you to combine dataflows with other kinds of operation in a single, scheduled process. Pipelines can be created in a few different workloads, including Data Factory workload.

1. From your Fabric-enabled workspace, make sure you are still in the **Data Engineering** workload. Select **Data pipeline**, then, when prompted, create a new pipeline named **Load data**.

	The pipeline editor opens.

	![Empty data pipeline.](./Images/new-pipeline.png)

	> **Tip**: If the Copy Data wizard opens automatically, close it!

2. Select **Add pipeline activity**, and add a **Dataflow** activity to the pipeline.

3. With the new **Dataflow1** activity selected, on the **Settings** tab, in the **Dataflow** drop-down list, select **Dataflow 1** (the data flow you created previously)

	![Pipeline with a dataflow activity.](./Images/dataflow-activity.png)

4. On the **Home** tab, use the **&#128427;** (*Save*) icon to save the pipeline as **Ingest Sales Data**.
5. Use the **&#9655; Run** button to run the pipeline, and wait for it to complete. It may take a few minutes.

	![Pipeline with a dataflow that has completed successfully.](./Images/dataflow-pipeline-succeeded.png)

6. In the menu bar on the left edge, select the page for your workspace. Then in the list of items in your workspace, select your lakehouse.
7. In the **...** menu for **Tables**, select **refresh**. Then expand **Tables** and select the **sales** table, which has been created by your dataflow.

	![Table loaded by a dataflow.](./Images/loaded-table.png)

## Use Dataflow Gen2 with Power BI

You've created a Dataflow Gen2 to load data into a lakehouse and include in a pipeline. The transformations happen upstream with the dataflow, so Power BI Data Analysts connecting to the dataflow as a dataset will spend less time on data preparation and will have consistent data integrity.

1. Open Power BI Desktop.
1. From the Home ribbon, select **Get Data** and choose **Dataflows** connector.
1. Navigate to the workspace, then dataflow, and select the queries you'd like to load.
1. **Load** will bring you back to the canvas view; **Transform Data** will open Power Query Editor.
1. Now you're ready to extend your data model with DAX calculations and create visualizations.

    > **Tip**: If you wanted to customize this core dataset, you could make specialized transformations, and then publish this dataset, and distribute with intended audience.

  ![Power BI data source connectors](Images/pbid-dataflow-connectors.png)

## Clean up resources

If you've finished exploring dataflows in Microsoft Fabric, you can delete the workspace you created for this exercise.

1. Navigate to Microsoft Fabric in your browser.
1. In the bar on the left, select the icon for your workspace to view all of the items it contains.
1. In the **...** menu on the toolbar, select **Workspace settings**.
1. In the **Other** section, select **Delete this workspace**.
1. Don't save the changes to Power BI Desktop, or delete the .pbix file if already saved.
