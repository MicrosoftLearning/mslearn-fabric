---
lab:
    title: 'Work with model relationships (optional)'
    module: 'Design and build tabular models'
---

# Work with model relationships

> **IMPORTANT**: This exercise is deprecated, and will be removed or uodated soon. The instructions might no longer be accurate, and the exercise is unsupported.

In this exercise, you will work with model relationships specifically to address the need for role-playing dimensions. It will involve working with active and inactive relationships, and also Data Analysis Expressions (DAX) functions that modify relationship behavior.

In this exercise, you learn how to:

- Interpret relationship properties in the model diagram.
- Set relationship properties.
- Use DAX functions that modify relationship behavior.

This lab takes approximately **45** minutes to complete.

> **Note**: You need a [Microsoft Fabric trial](https://learn.microsoft.com/fabric/get-started/fabric-trial) to complete this exercise.

## Explore model relationships

In this exercise, you will open a pre-developed Power BI Desktop solution to learn about the data model. You will then explore the behavior of active model relationships.

### Download starter file

1. Download the [Sales Analysis starter file](https://aka.ms/fabric-relationships-starter) from `https://aka.ms/fabric-relationships-starter` and save it on your local computer (in any folder).

1. Navigate to the downloaded file and open it in Power BI Desktop.
    >Note : Ignore and close the warning asking to apply changes.

### Review the data model

1. In Power BI Desktop, at the left, switch to **Model** view.

    ![](Images/work-with-model-relationships-image2.png)
2. Use the model diagram to review the model design.

    ![](Images/work-with-model-relationships-image3.png)

    *The model comprises six dimension tables and one fact table. The **Sales** fact table stores sales order details. It’s a classic star schema design.*

3. Notice that there are three relationships between the **Date** and **Sales** tables.

    ![](Images/work-with-model-relationships-image4.png)

    *The **DateKey** column in the **Date** table is a unique column representing the "one” side of the relationships. Filters applied to any column of the **Date** table propagate to the **Sales** table using one of the relationships.*

4. Hover the cursor over each of the three relationships to highlight the "many” side column in the **Sales** table.

5. Notice that the relationship to the **OrderDateKey** column is a solid line, while the other relationships are represented by a dotted line.

    *A solid line represents an active relationship. There can only be one active relationship path between two model tables, and the path is used by default to propagate filters between tables. Conversely, a dotted line represents an inactive relationship. Inactive relationships are used only when explicitly invoked by DAX formulas.*

    *The current model design indicates that the **Date** table is a role-playing dimension. This dimension could play the role of order date, due date, or ship date. Which role depends on the analytical requirements of the report.*

    *In this lab, you will learn how to design a model to support role playing dimensions.*

### Visualize date data

In this task, you will visualize sales data by date and switch the active status of relationships.

1. Switch to **Report** view.

    ![](Images/work-with-model-relationships-image5.png)

2. To add a table visual, in the **Visualizations** pane, select the **Table** visual icon.

    ![](Images/work-with-model-relationships-image6.png)

3. To add columns to the table visual, in the **Data** pane (located at the right), first expand the **Date** table.

    ![](Images/work-with-model-relationships-image7.png)

4. Drag the **Fiscal Year** column and drop it into the table visual.

    ![](Images/work-with-model-relationships-image8.png)

5. Expand open the **Sales** table, and then drag and drop the **Sales Amount** column into the table visual.

    ![](Images/work-with-model-relationships-image9.png)

6. Review the table visual.

    ![](Images/work-with-model-relationships-image10.png)

    *The table visual shows the sum of the **Sales Amount** column grouped by year. But what does **Fiscal Year** mean? Because there’s an active relationship between the **Date** and **Sales** tables to the **OrderDateKey** column, **Fiscal Year** means the fiscal year in which the orders were made.*

    *To clarify which fiscal year, it’s a good idea to rename the visual field (or add a title to the visual).*

7. In the **Visualizations** pane for the table visual, from inside the **Columns** well, select the down-arrow, and then select **Rename for this visual**.

    ![](Images/work-with-model-relationships-image11.png)

8. Replace the text with **Order Year**, and then press **Enter**.

    ![](Images/work-with-model-relationships-image12.png)

    *Note: It’s quicker to rename a visual field by double-clicking its name.*

9. Notice that the table visual column header updates to the new name.

    ![](Images/work-with-model-relationships-image13.png)

### Modify relationship active status

In this task, you will modify the active status of two relationships.

1. On the **Modeling** ribbon, select **Manage Relationships**.

    ![](Images/work-with-model-relationships-image14.png)

2. In the **Manage relationships** window, for the relationship between the **Sales** and **Date** tables for the **OrderDateKey** column (third in the list), uncheck the **Active** checkbox.

    ![](Images/work-with-model-relationships-image15.png)

3. Check the **Active** checkbox for the relationship between the **Sales** and **Date** tables for the **ShipDateKey** column (last in the list).

    ![](Images/work-with-model-relationships-image16.png)

4. Select **Close**.

    ![](Images/work-with-model-relationships-image17.png)

    *These configurations have switched the active relationship between the **Date** and **Sales** tables to the **ShipDateKey** column.*

5. Review the table visual that now shows sales amounts grouped by ship years.

    ![](Images/work-with-model-relationships-image18.png)

6. Rename the first column as **Ship Year**.

    ![](Images/work-with-model-relationships-image19.png)

    *The first row represents a blank group because some orders have not yet shipped. In other words, there are BLANKs in the **ShipDateKey** column of the **Sales** table.*

7. In the **Manage relationships** window, revert the **OrderDateKey** relationship back to active by using the following steps:

    - Open the **Manage relationships** window

    - Uncheck the **Active** checkbox for the **ShipDateKey** relationship (last in the list)

    - Check the **Active** checkbox for the **OrderDateKey** relationship (third in the list)

    - Close the **Manage relationships** window

    - Rename the first visual field in the table visual as **Order Year**

    ![](Images/work-with-model-relationships-image20.png)

    *In the next exercise, you will learn how to make a relationship active in a DAX formula.*

## Use inactive relationships

In this exercise, you will learn how to make a relationship active in a DAX formula.

### Use inactive relationships

In this task, you will use the USERELATIONSHIP function to make an inactive relationship active.

1. In the **Data** pane, right-click the **Sales** table, and then select **New measure**.

    ![](Images/work-with-model-relationships-image21.png)

2. In the formula bar (located beneath the ribbon), replace the text with the following measure definition, and then press **Enter**.

    ```DAX
    Sales Shipped =
    CALCULATE (
    SUM ( 'Sales'[Sales Amount] ),
    USERELATIONSHIP ( 'Date'[DateKey], 'Sales'[ShipDateKey] )
    )
    ```

    *This formula uses the CALCULATE function to modify the filter context. It’s the USERELATIONSHIP function that, for the purpose of this calculation, makes the **ShipDateKey** relationship active.*

3. On the **Measure tools** contextual ribbon, from inside the **Formatting** group, set the decimals places to **2**.

    ![](Images/work-with-model-relationships-image22.png)

4. Add the **Sales Shipped** measure to the table visual.

    ![](Images/work-with-model-relationships-image23.png)

5. Widen the table visual so all columns are fully visible.

    ![](Images/work-with-model-relationships-image24.png)

    *Creating measures that temporarily set relationships as active is one way to work with role-playing dimensions. However, it can become tedious when there’s a need to create role-playing versions for many measures. For example, if there were 10 sales-related measures and three role-playing dates, it could mean creating 30 measures. Creating them with calculation groups could make the process easier.*

    *Another approach is to create a different model table for each role-playing dimension. You will do that in the next exercise.*

6. To remove the measure from the table visual, in the **Visualizations** pane, from inside the **Columns** well, for the **Sales Shipped** field, press **X**.

    ![](Images/work-with-model-relationships-image25.png)

## Add another Date table

In this exercise, you will add a date table to support ship date analysis.

### Remove the inactive relationships

In this task, you will remove the existing relationship to the **ShipDateKey** column.

1. Switch to **Model** view.

    ![](Images/work-with-model-relationships-image26.png)

2. In the model diagram, right-click the **ShipDateKey** relationship, and then select **Delete**.

    ![](Images/work-with-model-relationships-image27.png)

3. When prompted to confirm the deletion, select **Yes**.

    *Deleting the relationship results in an error with the **Sales Shipped** measure. You will rewrite the measure formula later in this lab.*

### Disable relationship options

In this task, you will disable two relationship options.

1. On the **File** ribbon tab, select **Options and settings**, and then select **Options**.

    ![](Images/work-with-model-relationships-image29.png)

2. In the **Options** window, at the bottom-left, from inside the **CURRENT FILE** group, select **Data Load**.

    ![](Images/work-with-model-relationships-image30.png)

3. In the **Relationships** section, uncheck the two enabled options.

    ![](Images/work-with-model-relationships-image31.png)

    *Generally, in your day-to-day work it’s okay to keep these options enabled. However, for the purposes of this lab, you will create relationships explicitly.*

4. Select **OK**.

    ![](Images/work-with-model-relationships-image32.png)

### Add another date table

In this task, you will create a query to add another date table to the model.

1. On the **Home** ribbon tab, from inside the **Queries** group, select the **Transform data** icon, which opens the **Power Query Editor**.

    ![](Images/work-with-model-relationships-image33.png)

    *If you are prompted to specify how to connect, **Edit Credentials**.*

    ![](Images/work-with-model-relationships-image52.png)

    *Leave the default connection settings for Windows with "Use my current credentials", then **Connect**.*

     ![](Images/work-with-model-relationships-image53.png)
    *Select **Yes** to close the warning message.*

2. In the **Power Query Editor** window, in the **Queries** pane (located at the left), right-click the **Date** query, and then select **Reference**.

    ![](Images/work-with-model-relationships-image34.png)

    *A referencing query is one that uses another query as its source. So, this new query sources its date from the **Date** query.*

3. In the **Query Settings** pane (located at the right), in the **Name** box, replace the text with **Ship Date**.

    ![](Images/work-with-model-relationships-image35.png)

4. To rename the **DateKey** column, double-click the **DateKey** column header.

5. Replace the text with **ShipDateKey**, and then press **Enter**.

    ![](Images/work-with-model-relationships-image36.png)

6. Also rename the **Fiscal Year** column as **Ship Year**.

    *If possible, it’s a good idea to rename all columns so they describe the role they’re playing. In this lab, to keep things simple you will rename only two columns.*

7. To load the table to the model, on the **Home** ribbon tab, select the **Close &amp; Apply** icon.

    ![](Images/work-with-model-relationships-image37.png)

8. When the table has added to the model, to create a relationship, from the **Ship Date** table drag the **ShipDateKey** column to the **ShipDateKey** column of the **Sales** table.

    ![](Images/work-with-model-relationships-image38.png)

9. Notice that an active relationship now exists between the **Ship Date** and **Sales** tables.

### Visualize ship date data

In this task, you will visualize the ship date data in a new table visual.

1. Switch to **Report** view.

    ![](Images/work-with-model-relationships-image39.png)

2. To clone the table visual, first select the visual.

3. On the **Home** ribbon tab, from inside the **Clipboard** group, select **Copy**.

    ![](Images/work-with-model-relationships-image40.png)

4. To paste the copied visual, on the **Home** ribbon tab, from inside the **Clipboard** group, select **Paste**.

    *Tip: You can also use the **Ctrl+C** and **Ctrl+V** shortcuts.*

    ![](Images/work-with-model-relationships-image41.png)

5. Move the new table visual to the right of the existing table visual.

6. Select the new table visual, and then in the **Visualizations** pane, from inside the **Columns** well, remove the **Order Year** field.

    ![](Images/work-with-model-relationships-image42.png)

7. In the **Data** pane, expand open the **Ship Date** table.

8. To add a new field to the new table visual, from the **Ship Date** table, drag the **Ship Year** field to the **Columns** well, above the **Sales Amount** field.

    ![](Images/work-with-model-relationships-image43.png)

9. Verify that the new table visual shows sales amount grouped by ship year.

    ![](Images/work-with-model-relationships-image44.png)

    *The model now has two date tables, each with an active relationship to the **Sales** table. The benefit of this design approach is that it’s flexible. It’s now possible to use all measures and summarizable fields with either date table.*

    *There are, however, some disadvantages. Each role-playing table will contribute to a larger model size—although dimension table aren’t typically large in terms of rows. Each role-playing table will also require duplicating model configurations, like marking the date table, creating hierarchies, and other settings. Also, additional tables contribute to a possible overwhelming number of fields. Users may find it more difficult to find the model resources they need.*

    *Lastly, it’s not possible to achieve a combination of filters in the one visual. For example, it’s not possible to combine sales ordered and sales shipped in the same visual without creating a measure. You will create that measure in the next exercise.*

## Explore other relationship functions

In this exercise, you will work with other DAX relationship functions.

### Explore other relationship functions

In this task, you will work with the CROSSFILTER and TREATAS functions to modify relationship behavior during calculations.

1. In the **Data** pane, from inside the **Sales** table, select the **Sales Shipped** measure.

    ![](Images/work-with-model-relationships-image45.png)

2. In the formula base, replace the text with the following definition:

    ```dax
    Sales Shipped =
    CALCULATE (
    SUM ( 'Sales'[Sales Amount] ),
    CROSSFILTER ( 'Date'[DateKey], 'Sales'[OrderDateKey], NONE ),
    TREATAS (
    VALUES ( 'Date'[DateKey] ),
    'Ship Date'[ShipDateKey]
        )
    )
    ```

    *This formula uses the CALCULATE function to sum the **Sales Amount** column by using modified relationship behaviors. The CROSSFILTER function disables the active relationship to the **OrderDateKey** column (this function can also modify filter direction). The TREATAS function creates a virtual relationship by applying the in-context **DateKey** values to the **ShipDateKey** column.*

3. Add the revised **Sales Shipped** measure to the first table visual.

    ![](Images/work-with-model-relationships-image46.png)

4. Review the first table visual.

    ![](Images/work-with-model-relationships-image47.png)

5. Notice that there is no BLANK group.

    *Because there are no BLANKs in the **OrderDateKey** column, a BLANK group wasn’t generated. Showing unshipped sales will require a different approach.*

### Show unshipped sales

In this task, you will create a measure to show the unshipped sales amount.

1. Create a measure named **Sales Unshipped** in the **Sales** table by using the following definition:

    ```DAX
    Sales Unshipped =
    CALCULATE (
    SUM ( 'Sales'[Sales Amount] ),
    ISBLANK ( 'Sales'[ShipDateKey] )
    )
    ```

    *This formula sums the **Sales Amount** column where the **ShipDateKey** column is BLANK.*

2. Format the measure to use two decimal places.

3. To add a new visual to the page, first select a blank area of the report page.

4. In the **Visualizations** pane, select the **Card** visual icon.

    ![](Images/work-with-model-relationships-image48.png)

5. Drag the **Sales Unshipped** measure into the card visual.

    ![](Images/work-with-model-relationships-image49.png)

6. Verify that the final report page layout looks like the following.

    ![](Images/work-with-model-relationships-image50.png)

### Finish up

To finish the exercise, close Power BI Desktop - no need to save the file.

