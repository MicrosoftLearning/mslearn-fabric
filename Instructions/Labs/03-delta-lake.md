---
lab:
    title: 'Use delta tables in Apache Spark'
    module: 'Work with Delta Lake tables in Microsoft Fabric'
---

---
*The UI is changing frequently -we'll need to update (or remove) screenshots prior to release.*

---

# Use delta tables in Apache Spark

Tables in a Microsoft Fabric lakehouse are based on the open source *Delta Lake* format for Apache Spark. Delta Lake adds support for relational semantics for both batch and streaming data operations, and enables the creation of a Lakehouse architecture in which Apache Spark can be used to process and query data in tables that are based on underlying files in a data lake.

This exercise should take approximately **40** minutes to complete

## Before you start

You'll need a Power BI Premium subscription with access to the Microsoft Fabric preview.

## Create a workspace

Before working with data in Fabric, create a workspace with premium capacity enabled.

1. Sign into your Power BI service at [https://app.powerbi.com](https://app.powerbi.com).
2. In the menu bar on the left, select **Workspaces** (the icon looks similar to &#128455;).
3. Create a new workspace with a name of your choice, selecting the **Premium per user** licensing mode.
4. When your new workspace opens, it should be empty, as shown here:

    ![Screenshot of an empty workspace in Power BI.](./Images/new-workspace.png)

## Create a lakehouse and upload data

Now that you have a workspace, it's time to switch to the *Data engineering* experience in the portal and create a data lakehouse for the data you're going to analyze.

1. At the bottom left of the Power BI portal, select the **Power BI** icon and switch to the **Data engineering** experience, as shown here:

    ![Screenshot of the experience menu in Power BI.](./Images/data-engineering.png)

2. In the **Data engineering** home page, create a new **Lakehouse** with a name of your choice.

    After a minute or so, a new empty lakehouse. You need to ingest some data into the data lakehouse for analysis. There are multiple ways to do this, but in this exercise you'll simply download a text file to your local computer and then upload it to your lakehouse.

3. Download the data file for this exercise from [https://github.com/MicrosoftLearning/dp-data/raw/main/products.csv](https://github.com/MicrosoftLearning/dp-data/raw/main/products.csv), saving it as **products.csv** on your local computer.

4. Return to the web browser tab containing your lakehouse, and in the **...** menu for the **Files** folder in the **Lakehouse explorer** pane, select **New subfolder** and create a folder named **products**.

5. In the **...** menu for the **products** folder, select **Upload** and **Upload files**, and then upload the **products.csv** file from your local computer to the lakehouse.
6. After the file has been uploaded, select the **products** folder; and verify that the **products.csv** file has been uploaded, as shown here:

    ![Screenshot of uploaded products.csv file in a lakehouse.](./Images/products-file.png)

## Explore data in a dataframe

1. On the **Home** page while viewing the contents of the **products** folder in your datalake, in the **Open notebook** menu, select **New notebook**.

    After a few seconds, a new notebook containing a single *cell* will open. Notebooks are made up of one or more cells that can contain *code* or *markdown* (formatted text).

2. Select the existing cell in the notebook, which contains some simple code, and then use its **&#128465;** (*Delete*) icon at its top-right to remove it - you will not need this code.
3. In the **Explorer** pane on the left, expand **Files** and select **products** to reveal a new pane showing the **products.csv** file you uploaded previously:

    ![Screenshot of a notebook with a Files pane.](./Images/notebook-products.png)

4. In the **...** menu for **products.csv**, select **Load data** > **Spark**. A new code cell containing the following code should be added to the notebook:

    ```python
    df = spark.read.format("csv").option("header","true").load("Files/products.csv")
    # df now is a Spark DataFrame containing CSV data from "Files/products.csv".
    display(df)
    ```

    > **Tip**: You can hide the pane containing the files on the left by using its **<** icon. Doing so will help you focus on the notebook.

5. Use the **&#9655;** (*Run cell*) button on the left of the cell to run it.

    > **Note**: Since this is the first time you've run any Spark code in this notebook, a Spark session must be started. This means that the first run can take a minute or so to complete. Subsequent runs will be quicker.

6. When the cell command has completed, review the output below the cell, which should look similar to this:

    | Index | ProductID | ProductName | Category | ListPrice |
    | -- | -- | -- | -- | -- |
    | 1 | 771 | Mountain-100 Silver, 38 | Mountain Bikes | 3399.9900 |
    | 2 | 772 | Mountain-100 Silver, 42 | Mountain Bikes | 3399.9900 |
    | 3 | 773 | Mountain-100 Silver, 44 | Mountain Bikes | 3399.9900 |
    | ... | ... | ... | ... | ... |

## Create delta tables

You can save the dataframe as a delta table by using the `saveAsTable` method. Delta Lake supports the creation of both *managed* and *external* tables.

### Create a *managed* table

1. Under the results returned by the first code cell, use the **+ Code** button to add a new code cell if one doesn't already exist. Then enter the following code in the new cell and run it:

    ```python
    df.write.format("delta").saveAsTable("managed_products")
    ```

2. In the **Lakehouse explorer** pane, in the **...** menu for the **Tables** folder, select **Refresh**. Then expand the **Tables** node and verify that the **managed_products** table has been created.

    ---
    *If refreshing the Tables folder doesn't work, refresh the entire web page!*

    ---

### Create an *external* table

1. Add another new code cell, and use it to run the following code:

    ```python
    df.write.format("delta").saveAsTable("external_products", path="Files/external_products")
    ```

2. In the **Lakehouse explorer** pane, in the **...** menu for the **Tables** folder, select **Refresh**. Then expand the **Tables** node and verify that the **external_products** table has been created.

    ---
    *If refreshing the Tables folder doesn't work, refresh the entire web page!*

    ---

### Compare *managed* and *external* tables

1. In the **Lakehouse explorer** pane, expand the **Files** folder and verify that a folder named **external_products** has been created. Select this folder to view the Parquet data files and **_delta_log** folder for the **external_products** table.

2. Add another code cell and run the following code:

    ```sql
    %%sql

    DESCRIBE FORMATTED extended_products;
    ```

    In the results, view the **Location** property for the table, which should be a path to the OneLake storage for the lakehouse ending with **/Files/external_products** (you may need to widen the **Data type** column to see the full path). Note also in the **Table properties** that the table type is **EXTERNAL**.

3. Modify the `DESCRIBE` command to show the details of the **managed_products** tables as shown here:

    ```sql
    %%sql

    DESCRIBE FORMATTED managed_products;
    ```

    In the results, note that there is no **Location** property for the table. Note also in the **Table properties** that the table type is **MANAGED**.

    So where are the data files for the managed table?

4. Add another code cell and run the following code:

    ```python
    from notebookutils import mssparkutils

    objs = mssparkutils.fs.ls('Tables')
    for obj in objs:
        print(obj.name)
    ```

    The files for managed table are stored in the **Tables** folder in the OneLake storage for the lakehouse. In this case, a folder named **managed_products** has been created to store the Parquet files and **delta_log** folder for the table you created.

5. Add another code cell and run the following code:

    ```sql
    %%sql

    DROP TABLE managed_products;
    DROP TABLE external_products;
    ```

6. In the **Lakehouse explorer** pane, in the **...** menu for the **Tables** folder, select **Refresh**. Then expand the **Tables** node and verify that no tables are listed.

    ---
    *If refreshing the Tables folder doesn't work, refresh the entire web page!*

    ---

7. In the **Lakehouse explorer** pane, expand the **Files** folder and verify that the **external_products** has not been deleted. Select this folder to view the Parquet data files and **_delta_log** folder for the data that was previously in the **external_products** table. The table metadata for the external table was deleted, but the files were not affected.

8. Re-run the cell that lists the contents of the **Tables** folder (shown below).

    ```python
    from notebookutils import mssparkutils

    objs = mssparkutils.fs.ls('Tables')
    for obj in objs:
        print(obj.name)
    ```

    Verify that the **managed_products** folder is no longer listed - the folder for the managed table was deleted along with the table metadata.

### Use SQL to create a table

1. Add another code cell and run the following code:

    ```sql
    %%sql

    CREATE TABLE products
    USING DELTA
    LOCATION 'Files/external_products';
    ```

2. In the **Lakehouse explorer** pane, in the **...** menu for the **Tables** folder, select **Refresh**. Then expand the **Tables** node and verify that a new table named **products** is listed. Then expand the table to verify that it's schema matches the original dataframe that was saved in the **external_products** folder.

    ---
    *If refreshing the Tables folder doesn't work, refresh the entire web page!*

    ---

3. Add another code cell and run the following code:

    ```sql
    %%sql

   SELECT * FROM products;
   ```

## Explore table versioning

Transaction history for delta tables is stored in JSON files in the **delta_log** folder. You can use this transaction log to manage data versioning.

1. Add a new code cell to the notebook and run the following code:

    ```Python
    %%sql

    UPDATE products
    SET ListPrice = ListPrice * 0.9
    WHERE Category = 'Mountain Bikes';
    ```

    This code implements a 10% reduction in the price for mountain bikes.

2. Add another code cell and run the following code:

    ```sql
    %%sql

    DESCRIBE HISTORY products;
    ```

    The results show the history of transactions recorded for the table.

3. Add another code cell and run the following code:

    ```python
    delta_table_path = 'Files/external_products'

    # Get the current data
    current_data = spark.read.format("delta").load(delta_table_path)
    display(current_data)

    # Get the version 0 data
    original_data = spark.read.format("delta").option("versionAsOf", 0).load(delta_table_path)
    display(original_data)
    ```

    The results show two dataframes - one containing the data after the price reduction, and the other showing the original version of the data.

## Use delta tables for streaming data

Delta lake supports streaming data. Delta tables can be a *sink* or a *source* for data streams created using the Spark Structured Streaming API. In this example, you'll use a delta table as a sink for some streaming data in a simulated internet of things (IoT) scenario.

1. Add a new code cell in the notebook. Then, in the new cell, add the following code and run it:

    ```python
    from notebookutils import mssparkutils
    from pyspark.sql.types import *
    from pyspark.sql.functions import *

    # Create a folder
    inputPath = 'Files/data/'
    mssparkutils.fs.mkdirs(inputPath)

    # Create a stream that reads data from the folder, using a JSON schema
    jsonSchema = StructType([
    StructField("device", StringType(), False),
    StructField("status", StringType(), False)
    ])
    iotstream = spark.readStream.schema(jsonSchema).option("maxFilesPerTrigger", 1).json(inputPath)

    # Write some event data to the folder
    device_data = '''{"device":"Dev1","status":"ok"}
    {"device":"Dev1","status":"ok"}
    {"device":"Dev1","status":"ok"}
    {"device":"Dev2","status":"error"}
    {"device":"Dev1","status":"ok"}
    {"device":"Dev1","status":"error"}
    {"device":"Dev2","status":"ok"}
    {"device":"Dev2","status":"error"}
    {"device":"Dev1","status":"ok"}'''
    mssparkutils.fs.put(inputPath + "data.txt", device_data, True)
    print("Source stream created...")
    ```

    Ensure the message *Source stream created...* is printed. The code you just ran has created a streaming data source based on a folder to which some data has been saved, representing readings from hypothetical IoT devices.

2. In a new code cell, add and run the following code:

    ```python
    # Write the stream to a delta table
    delta_stream_table_path = 'Files/delta/iotdevicedata'
    checkpointpath = 'Files/delta/checkpoint'
    deltastream = iotstream.writeStream.format("delta").option("checkpointLocation", checkpointpath).start(delta_stream_table_path)
    print("Streaming to delta sink...")
    ```

    This code writes the streaming device data in delta format.

3. In a new code cell, add and run the following code:

    ```python
    # Read the data in delta format into a dataframe
    df = spark.read.format("delta").load(delta_stream_table_path)
    display(df)
    ```

    This code reads the streamed data in delta format into a dataframe. Note that the code to load streaming data is no different to that used to load static data from a delta folder.

4. In a new code cell, add and run the following code:

    ```python
    # create a catalog table based on the streaming sink
    spark.sql("CREATE TABLE IotDeviceData USING DELTA LOCATION '{0}'".format(delta_stream_table_path))
    ```

    This code creates a catalog table named **IotDeviceData** (in the **default** database) based on the delta folder. Again, this code is the same as would be used for non-streaming data.

5. In a new code cell, add and run the following code:

    ```sql
    %%sql

    SELECT * FROM IotDeviceData;
    ```

    This code queries the **IotDeviceData** table, which contains the device data from the streaming source.

6. In a new code cell, add and run the following code:

    ```python
    # Add more data to the source stream
    more_data = '''{"device":"Dev1","status":"ok"}
    {"device":"Dev1","status":"ok"}
    {"device":"Dev1","status":"ok"}
    {"device":"Dev1","status":"ok"}
    {"device":"Dev1","status":"error"}
    {"device":"Dev2","status":"error"}
    {"device":"Dev1","status":"ok"}'''

    mssparkutils.fs.put(inputPath + "more-data.txt", more_data, True)
    ```

    This code writes more hypothetical device data to the streaming source.

7. In a new code cell, add and run the following code:

    ```sql
    %%sql

    SELECT * FROM IotDeviceData;
    ```

    This code queries the **IotDeviceData** table again, which should now include the additional data that was added to the streaming source.

8. In a new code cell, add and run the following code:

    ```python
    deltastream.stop()
    ```

    This code stops the stream.

## Clean up resources

In this exercise, you've learned how to work with delta tables in Microsoft Fabric.

If you've finished exploring your lakehouse, you can delete the workspace you created for this exercise.

1. In the bar on the left, select the icon for your workspace to view all of the items it contains.
2. In the **...** menu on the toolbar, select **Workspace settings**.
3. In the **Other** section, select **Delete this workspace**.