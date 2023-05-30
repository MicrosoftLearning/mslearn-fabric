---
lab:
    title: 'Create and use Dataflows (Gen2) in Microsoft Fabric'
    module: 'Ingest Data with Dataflows Gen2 in Microsoft Fabric'
---

# Create a Dataflow (Gen2) in Microsoft Fabric

In Microsoft Fabric, Dataflows (Gen2) connect to various data sources and perform transformations in Power Query Online. They can then be used in Data Pipelines to ingest data into a lakehouse or other analytical store, or to define a dataset for a Power BI report.

This lab is designed to introduce the different elements of Dataflows (Gen2), and not create a complex solution that may exist in an enterprise. This lab takes **approximately 30 minutes** to complete.

## Before you start

You'll need a Microsoft Fabric license to complete this exercise.

See [Getting started with Fabric](https://learn.microsoft.com/fabric/get-started/fabric-trial) for details of how to enable a free Fabric trial license. You will need a Microsoft *school* or *work* account to do this. If you don't have one, you can [sign up for a trial of Microsoft Office 365 E3 or higher](https://www.microsoft.com/microsoft-365/business/compare-more-office-365-for-business-plans).

After enabling the Fabric trial, when you sign into the Fabric portal at [https://app.fabric.microsoft.com](https://app.fabric.microsoft.com), you should see a Power BI logo at the bottom left that you can use to switch to the various workload experiences supported in Microsoft Fabric. If this logo is not visible, you may need to ask your organization's administrator to [enable Fabric trial functionality](https://learn.microsoft.com/fabric/get-started/fabric-trial#administer-user-access-to-a-fabric-preview-trial).

## Create a workspace

Before working with data in Fabric, create a workspace with the Fabric trial enabled.

1. Sign into [Microsoft Fabric](https://app.fabric.microsoft.com) at `https://app.fabric.microsoft.com` and select **Power BI**.
2. In the menu bar on the left, select **Workspaces** (the icon looks similar to &#128455;).
3. Create a new workspace with a name of your choice, selecting the **Trial** licensing mode.
4. When your new workspace opens, it should be empty, as shown here:

    ![Screenshot of an empty workspace in Power BI.](./Images/new-workspace.png)

## Create a lakehouse

Now that you have a workspace, it's time to switch to the **Data Engineering** experience in the portal and create a data lakehouse into which you'll ingest data.

1. At the bottom left of the Power BI portal, select the **Power BI** icon and switch to the **Data Engineering** experience.

2. In the **Data engineering** home page, create a new **Lakehouse** with a name of your choice.

    After a minute or so, a new empty lakehouse will be created.

 ![New lakehouse.](./Images/new-lakehouse.png)

## Create a Dataflow (Gen2) to ingest data

Now that you have a lakehouse, you need to ingest some data into it. One way to do this is to define a dataflow that encapsulates an *extract, transform, and load* (ETL) process.

1. In the home page for your workspace, select **New Dataflow Gen2**. After a few seconds, the Power Query editor for your new dataflow opens as shown here.

 ![New dataflow.](./Images/new-dataflow.png)

2. Select **Import from a Text/CSV file**, and create a new data source with the following settings:
 - **Link to file**: *Selected*
 - **File path or URL**: `https://raw.githubusercontent.com/MicrosoftLearning/dp-data/main/orders.csv`
 - **Connection**: Create new connection
 - **data gateway**: (none)
 - **Authentication kind**: Anonymous

3. Select **Next** to preview the file data, and then **Create** the data source. The Power Query editor shows the data source and an initial set of query steps to format the data, as shown here:

 ![Query in the Power Query editor.](./Images/power-query.png)

4. On the toolbar ribbon, select the **Add column** tab. Then select **Custom column** and create a new column named **MonthNo** that contains a whole number based on the formula `Date.Month([OrderDate])` - as shown here:

 ![Custom column in Power Query editor.](./Images/custom-column.png)

 The step to add the custom column is added to the query and the resulting column is displayed in the data pane:

 ![Query with a custom column step.](./Images/custom-column-added.png)

> **Tip:** In the Query Settings pane on the right side, notice the **Applied Steps** include each transformation step. At the bottom, you can also toggle the **Diagram flow** button to turn on the Visual Diagram of the steps.
>
> Steps can be moved up or down, edited by selecting the gear icon, and you can select each step to see the transformations apply in the preview pane.

![Applied steps pane with example steps.](Images/pq-applied-steps.png)

## Add data destination for Dataflow

1. On the toolbar ribbon, select the **Home** tab. Then in the **Add data destination** drop-down menu, select **Lakehouse**.

   > **Note:** You can also add a data destination by selecting the **Add data destination** button in the query settings pane.

2. In the **Connect to data destination** dialog box, edit the connection and sign in using your Power BI organizational account to set the identity that the dataflow uses to access the lakehouse.

 ![Data destination configuration page.](./Images/dataflow-connection.png)

3. Select **Next** and in the list of available workspaces, find your workspace and select the lakehouse you created in it at the start of this exercise. Then specify a new table named **orders**:

   ![Data destination configuration page.](./Images/data-destination-target.png)

   > **Note:** On the **Destination settings** page, notice how OrderDate and MonthNo are not selected in the Column mapping and there is an informational message: *Change to date/time*.

   ![Data destination settings page.](./Images/destination-settings.png)

1. Cancel this action, then go back to OrderDate and MonthNo columns in Power Query online. Right-click on the column header and **Change Type**.

    - OrderDate = Date/Time
    - MonthNo = Whole number

1. Now repeat the process outlined earlier to add a lakehouse destination.

8. On the **Destination settings** page, select **Append**, and then save the settings.  The **Lakehouse** destination is indicated as an icon in the query in the Power Query editor.

   ![Query with a lakehouse destination.](./Images/lakehouse-destination.png)

9. Select **Publish** to publish the dataflow. Then wait for the **Dataflow 1** dataflow to be created in your workspace.

1. Once published, you can right-click on the dataflow in your workspace, select **Properties**, and rename your dataflow.

## Add a dataflow to a pipeline

You can include a dataflow as an activity in a pipeline. Pipelines are used to orchestrate data ingestion and processing activities, enabling you to combine dataflows with other kinds of operation in a single, scheduled process. Pipelines can be created in a few different experiences, including Data Factory experience.

1. From your Fabric-enabled workspace, make sure you're still in the **Data Engineering** experience. Select **New**, **Data pipeline**, then when prompted, create a new pipeline named **Load data**.

   The pipeline editor opens.

   ![Empty data pipeline.](./Images/new-pipeline.png)

   > **Tip**: If the Copy Data wizard opens automatically, close it!

2. Select **Add pipeline activity**, and add a **Dataflow** activity to the pipeline.

3. With the new **Dataflow1** activity selected, on the **Settings** tab, in the **Dataflow** drop-down list, select **Dataflow 1** (the data flow you created previously)

   ![Pipeline with a dataflow activity.](./Images/dataflow-activity.png)

4. On the **Home** tab, use the **&#128427;** (*Save*) icon to save the pipeline as **Ingest Sales Data**.
5. Use the **&#9655; Run** button to run the pipeline, and wait for it to complete. It may take a few minutes.

   ![Pipeline with a dataflow that has completed successfully.](./Images/dataflow-pipeline-succeeded.png)

6. In the menu bar on the left edge, select your lakehouse.
7. In the **...** menu for **Tables**, select **refresh**. Then expand **Tables** and select the **orders** table, which has been created by your dataflow.

   ![Table loaded by a dataflow.](./Images/loaded-table.png)

> **Tip**: Use the Power BI Desktop *Dataflows connector* to connect directly to the data transformations done with your dataflow.
>
> You can also make additional transformations, publish as a new dataset, and distribute with intended audience for specialized datasets.
>
>![Power BI data source connectors](Images/pbid-dataflow-connectors.png)

## Clean up resources

If you've finished exploring dataflows in Microsoft Fabric, you can delete the workspace you created for this exercise.

1. Navigate to Microsoft Fabric in your browser.
1. In the bar on the left, select the icon for your workspace to view all of the items it contains.
1. In the **...** menu on the toolbar, select **Workspace settings**.
1. In the **Other** section, select **Remove this workspace**.
1. Don't save the changes to Power BI Desktop, or delete the .pbix file if already saved.
