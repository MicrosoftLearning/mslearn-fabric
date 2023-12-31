---
lab:
    title: 'Create and explore a semantic model'
    module: 'Understand scalability in Power BI'
---

# Create and explore a semantic model

## Overview

**The estimated time to complete the lab is 30 minutes**

In this lab, you'll use the Fabric interface to develop a data model over the sample NY Taxi data in a Fabric data warehouse.

You'll practice:

- Creating a custom semantic model from a Fabric data warehouse.
- Create relationships and organize the model diagram.
- Explore the data in your semantic model directly in Fabric.

## Create a workspace

Before working with data in Fabric, create a workspace with the Fabric trial enabled.

1. On the [Microsoft Fabric home page](https://app.fabric.microsoft.com), select **Synapse Data Engineering**.
1. In the menu bar on the left, select **Workspaces** (the icon looks similar to &#128455;).
1. Create a new workspace with a name of your choice, selecting a licensing mode that includes Fabric capacity (*Trial*, *Premium*, or *Fabric*).
1. When your new workspace opens, it should be empty.

## Create a data warehouse and load sample data

Now that you have a workspace, it's time to create a data warehouse. The Synapse Data Warehouse home page includes a shortcut to create a new warehouse:

1. In the **Synapse Data Warehouse** home page, create a new **Warehouse** with a name of your choice.

    After a minute or so, a new warehouse will be created:
    
    ![Screenshot of a new warehouse](./Images/new-data-warehouse2.png)

1. In the center of the data warehouse user interface, you'll see a few different ways to load data into your warehouse. Select **Sample data** to load NYC Taxi data into your data warehouse. This will take a couple of minutes.

1. After your sample data has loaded, use the **Explorer** pane on the right to see what tables and views already exist in the sample data warehouse.

1. Select the **Reporting** tab of the ribbon and choose **New semantic model**. This enables you to create a new semantic model using only specific tables and views from your data warehouse, for use by data teams and the business to build reports.

1. Name the semantic model "Taxi Revenue", ensure it's in the workspace you just created, and select the following tables:
   - Date
   - Trip
   - Geography
   - Weather
     
   ![Screenshot of the New semantic model interface with four tables selected](./Images/new-semantic-model.png)
     
### Create relationships between tables

Now you'll create relationships between the tables to accurrately analyze and visualize your data. If you're familiar with creating relationships in Power BI desktop, this will look familiar.

1. Navigate back to your workspace and confirm that you see your new semantic model, Taxi Revenue. Notice that the item type is **Semantic model**, as opposed to the **Semantic model (default)** that is automatically created when you create a data warehouse.

     *Note: A default semantic model is created automatically when you create a Warehouse or SQL analytics endpoint in Microsoft Fabric, and it inherits the business logic from the parent Lakehouse or Warehouse. A semantic model that you create yourself, as we've done here, is a custom model that you can design and modify according to your specific needs and preferences. You can create a custom semantic model by using Power BI Desktop, Power BI service, or other tools that connect to Microsoft Fabric.*

1. Select **Open data model from the ribbon**.
   
    Now, you'll create relationships between the tables. If you're familiar with creating relationships in Power BI desktop, this will look familiar!

    *Reviewing the star schema concept, we'll organize the tables in our model into a Fact table and Dimension tables. In this model, the Trip table is our fact table, and our dimensions are date, geography, and weather.*

1. Create a relationship between the **Date** table and the **Trip** table using the **DateID** column. **Select the DateID column** in the **Date** table and **drag and frop it on top of the DateID column in the Trip table**. Alternatively, you can select **Manage relationships** from the ribbon, followed by **New relationship**.

1. Ensure the relationship is a **One to many** relationship from the **Date** table to the **Trip** table.

### Organize the model diagram

In this task, you will organize the model diagram to easily understand the star schema design.

1. In Power BI Desktop, at the left, select **Model** view.

 ![](../images/dp500-create-a-star-schema-model-image43.png)

2. To resize the model diagram to fit to screen, at the bottom-right, select the **Fit to screen** icon.

 ![](../images/dp500-create-a-star-schema-model-image44.png)

3. Drag the tables into position so that the **Sales** fact table is located at the middle of the diagram, and the remaining tables, which are dimension tables, are located around the fact table.

4. If any of the dimension tables aren't related to the fact table, use the following instructions to create a relationship:

- Drag the dimension key column (for example, **ProductKey**) and drop it on the corresponding column of the **Sales** table.

- In the **Create Relationship** window, select **OK**.

5. Review the final layout of the model diagram.

 ![](../images/dp500-create-a-star-schema-model-image45.png)

 *The creation of the star schema model is now complete. There are many modeling configurations that could now be applied, like adding hierarchies, calculations, and setting properties like column visibility.*

6. To save the solution, at the top-left, select the **File** menu and from there select **Save as**.

7. In the **Save As** window, go to the **D:\DP500\Allfiles\04\MySolution** folder.

8. In the **File name** box, enter **Sales Analysis**.

 ![](../images/dp500-create-a-star-schema-model-image46.png)

9. Select **Save**.

10. Close Power BI Desktop.

### Pause the SQL pool

In this task, you will stop the SQL pool.

1. In a web browser, go to [https://portal.azure.com](https://portal.azure.com/).

2. Locate the SQL pool.

3. Pause the SQL pool.
