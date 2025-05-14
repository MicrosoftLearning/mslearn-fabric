---
lab:
    title: 'Monitor Fabric activity in the monitoring hub'
    module: 'Monitoring Fabric'
---

# Monitor Fabric activity in the monitoring hub

The *monitoring hub* in Microsoft Fabric provides a central place where you can monitor activity. You can use the monitoring hub to review events related to items you have permission to view.

This lab takes approximately **30** minutes to complete.

> **Note**: You need access to a [Microsoft Fabric tenant](https://learn.microsoft.com/fabric/get-started/fabric-trial) to complete this exercise.

## Create a workspace

Before working with data in Fabric, create a workspace in a tenant with the Fabric capacity enabled.

1. Navigate to the [Microsoft Fabric home page](https://app.fabric.microsoft.com/home?experience=fabric-developer) at `https://app.fabric.microsoft.com/home?experience=fabric-developer` in a browser and sign in with your Fabric credentials.
1. In the menu bar on the left, select **Workspaces** (the icon looks similar to &#128455;).
1. Create a new workspace with a name of your choice, selecting a licensing mode in the **Advanced** section that includes Fabric capacity (*Trial*, *Premium*, or *Fabric*).
1. When your new workspace opens, it should be empty.

    ![Screenshot of an empty workspace in Fabric.](./Images/new-workspace.png)

## Create a lakehouse

Now that you have a workspace, it's time to create a data lakehouse for your data.

1. On the menu bar on the left, select **Create**. In the *New* page, under the *Data Engineering* section, select **Lakehouse**. Give it a unique name of your choice.

    >**Note**: If the **Create** option is not pinned to the sidebar, you need to select the ellipsis (**...**) option first.

    After a minute or so, a new lakehouse will be created:

    ![Screenshot of a new lakehouse.](./Images/new-lakehouse.png)

1. View the new lakehouse, and note that the **Lakehouse explorer** pane on the left enables you to browse tables and files in the lakehouse:

    Currently, there are no tables or files in the lakehouse.

## Create and monitor a Dataflow

In Microsoft Fabric, you can use a Dataflow (Gen2) to ingest data from a wide range of sources. In this exercise, you'll use a dataflow to get data from a CSV file and load it into a table in your lakehouse.

1. On the **Home** page for your lakehouse, in the **Get data** menu, select **New Dataflow Gen2**.
1. Name the new dataflow `Get Product Data` and select **Create**.

    ![Screenshot of a new dataflow.](./Images/new-data-flow.png)

1. In the dataflow designer, select **Import from a Text/CSV file**. Then complete the Get Data wizard to create a data connection by linking to `https://raw.githubusercontent.com/MicrosoftLearning/dp-data/main/products.csv` using anonymous authentication. When you have completed the wizard, a preview of the data will be shown in the dataflow designer like this:

    ![Screenshot of a dataflow query.](./Images/data-flow-query.png)

1. Publish the dataflow.
1. In the navigation bar on the left, select **Monitor** to view the monitoring hub and observe that your dataflow is in-progress (if not, refresh the view until you see it).

    ![Screenshot of the monitoring hub with a dataflow in-progress.](./Images/monitor-dataflow.png)

1. Wait for a few seconds, and then refresh the page until the status of the dataflow is **Succeeded**.
1. In the navigation pane, select your lakehouse. Then expand the **Tables** folder to verify that a table named **products** has been created and loaded by the dataflow (you may need to refresh the **Tables** folder).

    ![Screenshot of the products table in the lakehouse page.](./Images/products-table.png)

## Create and monitor a Spark notebook

In Microsoft Fabric, you can use notebooks to run Spark code.

1. On the menu bar on the left, select **Create**. In the *New* page, under the *Data Engineering* section, select **Notebook**.

    A new notebook named **Notebook 1** is created and opened.

    ![Screenshot of a new notebook.](./Images/new-notebook.png)

1. At the top left of the notebook, select **Notebook 1** to view its details, and change its name to `Query Products`.
1. In the notebook editor, in the **Explorer** pane, select **Add data items** and then select **Existing data sources**.
1. Add the lakehouse you created previously.
1. Expand the lakehouse item until you reach the **products** table.
1. In the **...** menu for the **products** table, select **Load data** > **Spark**. This adds a new code cell to the notebook as shown here:

    ![Screenshot of a notebook with code to query a table.](./Images/load-spark.png)

1. Use the **&#9655; Run all** button to run all cells in the notebook. It will take a moment or so to start the Spark session, and then the results of the query will be shown under the code cell.

    ![Screenshot of a notebook with query results.](./Images/notebook-output.png)

1. On the toolbar, use the **&#9723;** (*Stop session*) button to stop the Spark session.
1. In the navigation bar, select **Monitor** to view the monitoring hub, and note that the notebook activity is listed.

    ![Screenshot of the monitoring hub with a notebook activity.](./Images/monitor-notebook.png)

## Monitor history for an item

Some items in a workspace might be run multiple times. You can use the monitoring hub to view their run history.

1. In the navigation bar, return to the page for your workspace. Then use the **&#8635;** (*Refresh now*) button for your **Get Product Data** dataflow to re-run it.
1. In the navigation pane, select the **Monitor** page to view the monitoring hub and verify that the dataflow is in-progress.
1. In the **...** menu for the **Get Product Data** dataflow, select **Historical runs** to view the run history for the dataflow:

    ![Screenshot of the monitoring hub historical runs view.](./Images/historical-runs.png)

1. In the **...** menu for any of the historical runs select **View detail** to see details of the run.
1. Close the **Details** pane and use the **Back to main view** button to return to the main monitoring hub page.

## Customize monitoring hub views

In this exercise you've only run a few activities, so it should be fairly easy to find events in the monitoring hub. However, in a real environment you may need to search through a large number of events. Using filters and other view customizations can make this easier.

1. In the monitoring hub, use the **Filter** button to apply the following filter:
    - **Status**: Succeeeded
    - **Item type**: Dataflow Gen2

    With the filter applied, only successful runs of dataflows are listed.

    ![Screenshot of the monitoring hub with a filter applied.](./Images/monitor-filter.png)

1. Use the **Column Options** button to include the following columns in the view (use the **Apply** button to apply the changes):
    - Activity name
    - Status
    - Item type
    - Start time
    - Submitted by
    - Location
    - End time
    - Duration
    - Refresh type

    You may need to scroll horizontally to see all of the columns:

    ![Screenshot of the monitoring hub with custom columns.](./Images/monitor-columns.png)

## Clean up resources

In this exercise, you have created a lakehouse, a dataflow, and a Spark notebook; and you've used the monitoring hub to view item activity.

If you've finished exploring your lakehouse, you can delete the workspace you created for this exercise.

1. In the bar on the left, select the icon for your workspace to view all of the items it contains.
2. In the **...** menu on the toolbar, select **Workspace settings**.
3. In the **General** section, select **Remove this workspace**.
