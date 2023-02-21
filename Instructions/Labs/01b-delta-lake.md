---
lab:
    title: 'Use Delta Lake tables'
    module: 'Use Apache Spark in Trident'
---

# Use Delta Lake tables

Delta Lake is an open source project to build a transactional data storage layer on top of a data lake. Delta Lake adds support for relational semantics for both batch and streaming data operations, and enables the creation of a *Lakehouse* architecture in which Apache Spark can be used to process and query data in tables that are based on underlying files in the data lake.

This exercise should take approximately **40** minutes to complete

## Before you start

You'll need a Power BI Premium subscription with access to the Trident preview.

## Create a workspace

Before working with data in *Trident*, you should create a workspace with support for premium features.

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

    After a minute or so, a new lakehouse with no **Tables** or **Files** will be created. You need to ingest some data into the data lakehouse for analysis. There are multiple ways to do this, but in this exercise you'll simply download a text file to your local computer and then upload it to your lakehouse.

3. Download the data file for this exercise from [https://github.com/MicrosoftLearning/dp-data/raw/main/products.csv](https://github.com/MicrosoftLearning/dp-data/raw/main/products.csv), saving it as **products.csv** on your local computer.

4. Return to the web browser tab containing your lakehouse, and in the **...** menu for the **Files** node in the **Lake view** pane, select **New subsfolder** and create a folder named **products**.

5. In the **...** menu for the **products** folder, select **Upload** and **Upload files**, and then upload the **products.csv** file from your local computer to the lakehouse.
6. After the files have been uploaded, select the **products** folder; and verify that the **products.csv** file has been uploaded, as shown here:

    ![Screenshot of uploaded products.csv file in a lakehouse.](./Images/products-file.png)

## Explore data in a dataframe

1. On the **Home** page while viewing the contents of the **products** folder in your datalake, in the **Open notebook** menu, select **New notebook**.

    After a few seconds, a new notebook containing a single *cell* will open. Notebooks are made up of one or more cells that can contain *code* or *markdown* (formatted text).

2. Select the existing cell in the notebook, which contains some simple code, and then use its **&#128465;** (*Delete*) icon at its top-right to remove it - you will not need this code.
3. In the pane on the left, expand **Files** and select **products** to reveal a new pane showing the **products.csv** file you uploaded previously:

    ![Screenshot of a notebook with a Files pane.](./Images/notebook-products.png)

4. In the **...** menu for **products.csv**, select **Load data** > **Spark**. A new code cell containing the following code should be added to the notebook:

    ```python
    df = spark.read.format("csv").option("header","true").load("Files/products.csv")
    # df now is a Spark DataFrame containing CSV data from "Files/products.csv".
    display(df)
    ```

    > **Tip**: You can hide the pane containing the files on the left by using its **<** icon. Doing so will help you focus on the notebook.

5. Use the **&#9655;** (*Run cell*) button on the left of the cell to run it.

    > **Note**: Since this is the first time you've run any Spark code in this session, the Spark pool must be started. This means that the first run in the session can take a minute or so to complete. Subsequent runs will be quicker.

6. When the cell command has completed, review the output below the cell, which should look similar to this:

    | Index | ProductID | ProductName | Category | ListPrice |
    | -- | -- | -- | -- | -- |
    | 1 | 771 | Mountain-100 Silver, 38 | Mountain Bikes | 3399.9900 |
    | 2 | 772 | Mountain-100 Silver, 42 | Mountain Bikes | 3399.9900 |
    | 3 | 773 | Mountain-100 Silver, 44 | Mountain Bikes | 3399.9900 |
    | ... | ... | ... | ... | ... |

## Save the data in delta format

Delta lake uses the *delta* file format to save data. The delta format is based on *Parquet*, with additional files used to record transactional metadata.

1. Under the results returned by the first code cell, use the **+ Code** button to add a new code cell. Then enter the following code in the new cell and run it:

    ```Python
    delta_table_path = "Files/products-delta/"
    df.write.format("delta").save(delta_table_path)
    ```

2. In the pane on the left, in the **...** menu for **Files**, select **Refresh** and note that a new folder named **products-delta** has been created. Select this folder to see the parquet format file(s) containing the data and the **_delta_log** folder containing transactional metadata.

3. In the notebook, add another new code cell. Then, in the new cell, add the following code and run it:

    ```Python
    from delta.tables import *
    from pyspark.sql.functions import *

    # Create a deltaTable object
    deltaTable = DeltaTable.forPath(spark, delta_table_path)

    # Update the table (reduce price of product 771 by 10%)
    deltaTable.update(
        condition = "ProductID == 771",
        set = { "ListPrice": "ListPrice * 0.9" })

    # View the updated data as a dataframe
    display(deltaTable.toDF()
    ```

    The data is loaded from the delta format files into a **DeltaTable** object and updated. You can see the update reflected in the query results.

4. Add another new code cell with the following code and run it:

    ```Python
    new_df = spark.read.format("delta").load(delta_table_path)
    display(new_df)
    ```

    The code loads the delta table data into a data frame from its location in the data lake, verifying that the change you made via a **DeltaTable** object has been persisted.

5. Modify the code you just ran as follows, specifying the option to use the *time travel* feature of delta lake to view a previous version of the data.

    ```Python
    new_df = spark.read.format("delta").option("versionAsOf", 0).load(delta_table_path)
    display(new_df)
    ```

    When you run the modified code, the results show the original version of the data.

6. Add another new code cell with the following code and run it:

    ```Python
    deltaTable.history(10).show(20, False, True)
    ```

    The history of the last 20 changes to the table is shown - there should be two (the original creation, and the update you made.)

## Create catalog tables

So far you've worked with delta lake by loading data from the folder containing the delta format files on which the table is based. You can define *catalog tables* that encapsulate the data and provide a named table entity that you can reference in SQL code. Spark supports two kinds of catalog tables for delta lake:

- *External* tables that store data in a file location that you specify. The metadata for the table is managed separately from the files (so dropping the table does not delete the files).
- *Managed* tables that store data in the file location for the Hive metastore used by the Spark pool. The metadata for the table is tightly-coupled to the data files (so dropping the table deletes the files).

### Create an external table

1. In a new code cell, add and run the following code:

    ```Python
    spark.sql("CREATE TABLE ProductsExternal USING DELTA LOCATION '{0}'".format(delta_table_path))
    spark.sql("DESCRIBE EXTENDED ProductsExternal").show(truncate=False)
    ```

    This code creates an external tabled named **ProductsExternal** based on the path to the delta files you defined previously. It then displays a description of the table's properties. Note that the **Location** property is the path you specified.

2. Add a new code cell, and then enter and run the following code:

    ```sql
    %%sql

    SELECT * FROM ProductsExternal;
    ```

    The code queries the **ProductsExternal** table, which returns a resultset containing the products data in the Delta Lake table.

### Create a managed table

1. In a new code cell, add and run the following code:

    ```Python
    df.write.format("delta").saveAsTable("ProductsManaged")
    spark.sql("DESCRIBE EXTENDED ProductsManaged").show(truncate=False)
    ```

    This code creates a managed tabled named **ProductsManaged** based on the DataFrame you originally loaded from the **products.csv** file (before you updated the price of product 771). You do not specify a path for the parquet files used by the table - this is managed for you in the Hive metastore.

2. In the pane on the left, on the **Lake view** tab, in the **...** menu for the **Tables** node, select **Refresh**. Then note that a folder named **productsmanaged** has been created for the data files on which the table is based.

3. Add a new code cell, and then enter and run the following code:

    ```sql
    %%sql

    SELECT * FROM ProductsManaged;
    ```

    The code uses SQL to query the **ProductsManaged** table.

### Compare external and managed tables

1. In a new code cell, add and run the following code:

    ```sql
    %%sql

    SHOW TABLES;
    ```

    This code lists the tables in the lakehouse database.

2. In the pane on the left, select the **Table view** tab and verify that both tables are listed there.

3. Add a new code cell to the notebook, add use it to run the following code:

    ```sql
    %%sql

    DROP TABLE IF EXISTS ProductsExternal;
    DROP TABLE IF EXISTS ProductsManaged;
    ```

    This code drops the tables from the metastore.

4. On the **Table view** tab, in the **...** menu for the **Tables** node, select **Refresh**; and verify that no tables are now listed.
5. Return to the **Lake view** tab and refresh the **Tables** node. Note that the folder for the managed table has been deleted.
6. Refresh the  **Files** node. Dropping the external table has removed the table from the metastore, but left the data files intact.

### Create a table using SQL

1. Add a new code cell, and then enter and run the following code:

    ```sql
    %%sql

    CREATE TABLE Products
    USING DELTA
    LOCATION 'Files/products-delta';
    ```

2. Add a new code cell, and then enter and run the following code:

    ```sql
    %%sql

    SELECT * FROM Products;
    ```

    Observe that the new catalog table was created for the existing Delta Lake table folder, which reflects the changes that were made previously.

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

In this exercise, you've learned how to work with Delta Lake by creating delta format data files and catalog tables.