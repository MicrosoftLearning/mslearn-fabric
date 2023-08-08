---
lab:
    title: 'Create and use notebooks for data exploration'
    module: 'Explore data for data science with notebooks in Microsoft Fabric'
---

# Use notebooks to explore data in Microsoft Fabric

In this lab, we will use notebooks for data exploration. Notebooks are a powerful tool for interactively exploring and analyzing data. During this exercise, we will learn how to create and use notebooks to explore a dataset, generate summary statistics, and create visualizations to better understand the data. By the end of this lab, you will have a solid understanding of how to use notebooks for data exploration and analysis.

This lab will take approximately **45** minutes to complete.

> **Note**: You'll need a Microsoft Fabric license to complete this exercise. See [Getting started with Fabric](https://learn.microsoft.com/fabric/get-started/fabric-trial) for details of how to enable a free Fabric trial license. You will need a Microsoft *school* or *work* account to do this. If you don't have one, you can [sign up for a trial of Microsoft Office 365 E3 or higher](https://www.microsoft.com/microsoft-365/business/compare-more-office-365-for-business-plans).

## Create a workspace

Before working with data in Fabric, create a workspace with the Fabric trial enabled.

1. Sign into [Microsoft Fabric](https://app.fabric.microsoft.com) at `https://app.fabric.microsoft.com` and select **Power BI**.
2. In the menu bar on the left, select **Workspaces** (the icon looks similar to &#128455;).
3. Create a new workspace with a name of your choice, selecting a licensing mode that includes Fabric capacity (*Trial*, *Premium*, or *Fabric*).
4. When your new workspace opens, it should be empty, as shown here:

    ![Screenshot of an empty workspace in Power BI.](./Images/new-workspace.png)

## Create a lakehouse and upload files

Now that you have a workspace, it's time to switch to the *Data science* experience in the portal and create a data lakehouse for the data files you're going to analyze.

1. At the bottom left of the Power BI portal, select the **Power BI** icon and switch to the **Data Engineering** experience.
1. In the **Data engineering** home page, create a new **Lakehouse** with a name of your choice.

    After a minute or so, a new lakehouse with no **Tables** or **Files** will be created. You need to ingest some data into the data lakehouse for analysis. There are multiple ways to do this, but in this exercise you'll simply download and extract a folder of text files your local computer (or lab VM if applicable) and then upload them to your lakehouse.

1. TODO: Download and save the `dominicks_OJ.csv` CSV file for this exercise from [https://raw.githubusercontent.com/MicrosoftLearning/dp-data/main/XXXXX.csv](https://raw.githubusercontent.com/MicrosoftLearning/dp-data/main/XXXXX.csv).


1. Return to the web browser tab containing your lakehouse, and in the **...** menu for the **Files** node in the **Lake view** pane, select **Upload** and **Upload files**, and then upload the **dominicks_OJ.csv** file from your local computer (or lab VM if applicable) to the lakehouse.
6. After the files have been uploaded, expand **Files** and verify that the CSV file have been uploaded.

## Create a notebook

To train a model, you can create a *notebook*. Notebooks provide an interactive environment in which you can write and run code (in multiple languages) as *experiments*.

1. At the bottom left of the Power BI portal, select the **Data engineering** icon and switch to the **Data science** experience.

1. In the **Data science** home page, create a new **Notebook**.

    After a few seconds, a new notebook containing a single *cell* will open. Notebooks are made up of one or more cells that can contain *code* or *markdown* (formatted text).

1. Select the first cell (which is currently a *code* cell), and then in the dynamic tool bar at its top-right, use the **M&#8595;** button to convert the cell to a *markdown* cell.

    When the cell changes to a markdown cell, the text it contains is rendered.

1. Use the **&#128393;** (Edit) button to switch the cell to editing mode, then delete the content and enter the following text:

    ```text
   # Train a machine learning model and track with MLflow

   Use the code in this notebook to train and track models.
    ``` 

## Load data into a dataframe

Now you're ready to run code to prepare data and train a model. To work with data, you'll use *dataframes*. Dataframes in Spark are similar to Pandas dataframes in Python, and provide a common structure for working with data in rows and columns.

1. In the **Add lakehouse** pane, select **Add** to add a lakehouse.
1. Select **Existing lakehouse** and select **Add**.
1. Select the lakehouse you created in a previous section.
1. Expand the **Files** folder so that the CSV file is listed next to the notebook editor.
1. In the **...** menu for **churn.csv**, select **Load data** > **Pandas**. A new code cell containing the following code should be added to the notebook:

    ```python
    import pandas as pd
    df = pd.read_csv("/lakehouse/default/" + "Files/dominicks_OJ.csv") 
    display(df.head(5))
    ```

    ```python
    import os
    df = spark.read.format('csv').options(header="true", inferSchema="true").load("file:///lakehouse/default/Files/dominicks_OJ.csv")
    df.show(5)
    ```

    > **Tip**: You can hide the pane containing the files on the left by using its **<<** icon. Doing so will help you focus on the notebook.

1. Use the **&#9655; Run cell** button on the left of the cell to run it.

    > **Note**: Since this is the first time you've run any Spark code in this session, the Spark pool must be started. This means that the first run in the session can take a minute or so to complete. Subsequent runs will be quicker.

    The output shows the rows and columns of customer data from the `dominicks_OJ.csv` file.

## Check the shape of the data

Now that you've loaded the data, you can use it to check the structure of the dataset, such as the number of rows and columns, data types, and missing values.

1. Use the **+ Code** icon below the cell output to add a new code cell to the notebook, and enter the following code in it:

    ```python
    import pandas as pd

    # Load the Dominicks OJ dataset (assuming it's in a CSV file named 'dominicks_oj_dataset.csv')
    df = pd.read_csv('dominicks_oj_dataset.csv')
    
    # Display the number of rows and columns in the dataset
    print("Number of rows:", df.shape[0])
    print("Number of columns:", df.shape[1])

    # Display the data types of each column
    print("\nData types of columns:")
    print(df.dtypes)
    ```

    ```python
    # Display the number of rows and columns in the dataset
    print("Number of rows:", df.count())
    print("Number of columns:", len(df.columns))

    # Display the data types of each column
    print("\nData types of columns:")
    df.printSchema()
    ```

    The code uses pandas to load the dataset into a dataframe, displays the number of rows and columns in the dataset, and then displays the data types of each feature.

## Check for missing data

1. Use the **+ Code** icon below the cell output to add a new code cell to the notebook, and enter the following code in it:

    ```python
    # Check for missing values in the dataset
    missing_values = df.isnull().sum()
    print("\nMissing values per column:")
    print(missing_values)
    ```

    ```python 
    from pyspark.sql.functions import col, sum

    # Check for missing values in the dataset
    missing_values = df.select([col(c).isNull().cast("int").alias(c) for c in df.columns])
    missing_values_count = missing_values.agg(*[sum(col(c)).alias(c) for c in missing_values.columns])
    print("\nMissing values per column:")
    missing_values_count.show()
    ```

    The code checks for missing values in the dataframe. We can observe there's no missing data in the dataset.

1. TODO: Consider adding a date gap analysis for date column.

## Generate descriptive statistics for numerical variables

Now, let's generate descriptive statistics to understand the distribution of numerical variables.

1. Use the **+ Code** icon below the cell output to add a new code cell to the notebook, and enter the following code in it:

    ```python
    desc_stats = df.describe()
    print(desc_stats)
    ```

    ```python
    import pandas as pd

    desc_stats = df.describe()
    desc_stats_pd = desc_stats.toPandas()
    # Used the set_index and transpose methods to transpose the resulting DataFrame, so that the summary statistics are displayed as columns instead of rows to improve readability.
    desc_stats_pd = desc_stats_pd.set_index('summary').transpose()
    print(desc_stats_pd)
    ```

    The average price is 2.28, with a standard deviation of 0.648. This indicates that there is some variation in the price across different observations. Also, the minimum and maximum values for the quantity column are 64 and 716416, respectively, indicating a large range of values.

## Plot the data distribution

Analyze the individual features of the dataset to identify trends and outliers that may affect the analysis.

1. Add another new code cell to the notebook, enter the following code in it, and run it:

    ```python
    import matplotlib.pyplot as plt
    import seaborn as sns
    import numpy as np
    
    # Histogram of the Price variable
    # Calculate the mean, median of the Price variable
    mean = df['Price'].mean()
    median = df['Price'].median()
    
    # Histogram of the Price variable
    plt.figure(figsize=(8, 6))
    plt.hist(df['Price'], bins=20, color='skyblue', edgecolor='black')
    plt.title('Price Distribution')
    plt.xlabel('Price')
    plt.ylabel('Frequency')
    
    # Add lines for the mean, median, and mode
    plt.axvline(mean, color='red', linestyle='dashed', linewidth=2, label='Mean')
    plt.axvline(median, color='green', linestyle='dashed', linewidth=2, label='Median')
    
    # Add a legend
    plt.legend()
    plt.show()
    ```

    ```python
    from pyspark.sql.functions import mean, approx_count_distinct
    from matplotlib import pyplot as plt
    
    # Calculate the mean and median of the Price variable
    mean = df.select(mean('Price')).collect()[0][0]
    median = df.approxQuantile('Price', [0.5], 0.25)[0]
    
    # Convert the 'Price' column to a Pandas series and plot the histogram
    price_pd = df.select('Price').toPandas()['Price']
    plt.figure(figsize=(8, 6))
    plt.hist(price_pd, bins=20, color='skyblue', edgecolor='black')
    plt.title('Price Distribution')
    plt.xlabel('Price')
    plt.ylabel('Frequency')
    
    # Add lines for the mean and median
    plt.axvline(mean, color='red', linestyle='dashed', linewidth=2, label='Mean')
    plt.axvline(median, color='green', linestyle='dashed', linewidth=2, label='Median')
    
    # Add a legend
    plt.legend()
    plt.show()

    ```

    From this graph, you're able to observe the range and distribution of prices in the dataset. For example, you can see that most prices fall within 1.79 and 2.73, and that this data is right skewed.

## Run multivariate analysis

Create various visualizations such as scatter plots, box plots, etc., to gain insights into the data's patterns and relationships. By visualizing the relationships between multiple variables, you can gain insights into the data and generate hypotheses for further analysis.

1. Use the **+ Code** icon below the cell output to add a new code cell to the notebook, and enter the following code in it:

    ```python
    import matplotlib.pyplot as plt
    import seaborn as sns

    # Scatter plot of Quantity vs. Price
    plt.figure(figsize=(8, 6))
    sns.scatterplot(x='Price', y='Quantity', data=df)
    plt.title('Quantity vs. Price')
    plt.xlabel('Price')
    plt.ylabel('Quantity')
    plt.show()
    ```

    ```python
    from matplotlib import pyplot as plt
    import seaborn as sns
    
    # Convert the 'Price' and 'Quantity' columns to a Pandas DataFrame and plot the scatter plot
    df_pd = df.select('Price', 'Quantity').toPandas()
    plt.figure(figsize=(8, 6))
    sns.scatterplot(x='Price', y='Quantity', data=df_pd)
    plt.title('Quantity vs. Price')
    plt.xlabel('Price')
    plt.ylabel('Quantity')
    plt.show()
    ```
    
    This code uses the `matplotlib` and `seaborn` libraries to create the graphs. You can further customize these graphs by changing the plot settings and adding additional features such as titles, axis labels, and legends. From this graph, you're able to observe the relationship between the quantity sold and the price of the product. For example, we can see that as the price increases, the quantity sold decreases, indicating a negative relationship between these two variables.

1. Add another new code cell to the notebook, enter the following code in it, and run it:

    ```python
    import matplotlib.pyplot as plt
    import seaborn as sns

    # Box plot of Quantity by Brand
    plt.figure(figsize=(15, 5))
    sns.boxplot(x='Quantity', y='Brand', data=df, palette='pastel')
    plt.title('Quantity distribution by Brand')
    plt.xlabel('Brand')
    plt.ylabel('Quantity')
    plt.show()
    ```

    ```python
    from matplotlib import pyplot as plt
    import seaborn as sns
    
    # Convert the 'Brand' and 'Quantity' columns to a Pandas DataFrame and plot the box plot
    df_pd = df.select('Brand', 'Quantity').toPandas()
    plt.figure(figsize=(15, 5))
    sns.boxplot(x='Quantity', y='Brand', data=df_pd, palette='pastel')
    plt.title('Quantity distribution by Brand')
    plt.xlabel('Brand')
    plt.ylabel('Quantity')
    plt.show()
    ```

    From this graph, you're able to compare the quantities sold by brand. For example, you can see that the **dominicks** brand has a higher median quantity sold than the other brands, suggesting that it is more popular among customers.

1. Aggregating the data can help to reduce the dimensionality of the data, making it easier to visualize and analyze. Add another new code cell to the notebook, enter the following code in it, and run it:

    ```python
    import matplotlib.pyplot as plt
    import seaborn as sns

    # 4. Bar chart of the average Quantity by Store
    plt.figure(figsize=(15, 6))
    df.groupby('Store')['Quantity'].mean().plot(kind='bar', color='skyblue', edgecolor='black')
    plt.title('Average Quantity by Store')
    plt.xlabel('Store')
    plt.ylabel('Average Quantity')
    plt.show()
    ```

    ```python
    from matplotlib import pyplot as plt
    from pyspark.sql.functions import mean
    
    # Calculate the average Quantity by Store
    store_quantity = df.groupBy('Store').agg(mean('Quantity').alias('AverageQuantity'))
    
    # Convert the result to a Pandas DataFrame and plot the bar chart
    store_quantity_pd = store_quantity.toPandas().set_index('Store')
    plt.figure(figsize=(15, 6))
    store_quantity_pd['AverageQuantity'].plot(kind='bar', color='skyblue', edgecolor='black')
    plt.title('Average Quantity by Store')
    plt.xlabel('Store')
    plt.ylabel('Average Quantity')
    plt.show()

    ```

    From this graph, you're able to compare the average quantities sold by store. For example, you can see that the **111** store has a higher average quantity sold than all the other stores, indicating that it is more successful at selling products.

1. Add another new code cell to the notebook, enter the following code in it, and run it:

    ```python
    import matplotlib.pyplot as plt
    
    # Convert the WeekStarting column to a datetime data type
    df['WeekStarting'] = pd.to_datetime(df['WeekStarting'])
    
    # Line chart of the average Quantity by WeekStarting
    plt.figure(figsize=(8, 6))
    df.groupby('WeekStarting')['Quantity'].mean().plot(kind='line', color='blue')
    plt.title('Average Quantity by WeekStarting')
    plt.xlabel('WeekStarting')
    plt.ylabel('Average Quantity')
    plt.show()
    ```

    From this graph, you're able to observe how the average quantity sold changes over time. For example, you can see that the average quantity sold increases during certain months and decreases during others, suggesting seasonal trends or changes in customer demand.

## Correlation analysis

Let's calculate correlations between different features to understand their relationships and dependencies.

>[IMPORTANT]
> Correlation doesn't imply causation. 

1. Use the **+ Code** icon below the cell output to add a new code cell to the notebook, and enter the following code in it:

    ```python
    df.corr(numeric_only=True)
    ```

    ```python
    import pandas as pd
    from pyspark.ml.feature import VectorAssembler
    from pyspark.ml.stat import Correlation
    
    # Filter out only numeric variables
    numeric_cols = [col for col, dtype in df.dtypes if dtype in ('int', 'double', 'float')]
    assembler = VectorAssembler(inputCols=numeric_cols, outputCol="features")
    df_vector = assembler.transform(df).select("features")
    
    pearsonCorr = Correlation.corr(df_vector, 'features').collect()[0][0]
    
    # Convert the correlation matrix to a Pandas DataFrame
    corr_df = pd.DataFrame(pearsonCorr.toArray())
    
    # Set the column and index names to the names of the numeric columns
    corr_df.columns = numeric_cols
    corr_df.index = numeric_cols
    
    corr_df

    ```

1. Alternatively, a heatmap can help you quickly identify which pairs of variables have strong positive or negative relationships, and which pairs have no relationship. Add another new code cell to the notebook, enter the following code in it, and run it:

    ```python
    # Plotting correlation of all numeric variables
    plt.figure(figsize=(15, 7))
    sns.heatmap(df.corr(numeric_only=True), annot=True, vmin=-1, vmax=1, cmap="Blues")
    ```

    ```python
    # Plotting correlation of all numeric variables
    plt.figure(figsize=(15, 7))
    sns.heatmap(corr_df, annot=True, vmin=-1, vmax=1, cmap="Blues")
    ```

    From this graph, you can see a value close to 1 or -1, this suggests that there is a positive or negative strong linear relationship between the corresponding pair of variables. For example, the correlation value between **Hincome150** and **COLLEGE** suggests there's a strong positive linear relationship between these variables. However, **Age60** and **WorkingWoman** indicates there's a strong negative linear relationship between these variables.

## Save the notebook and end the Spark session

Now that you've finished exploring the data, you can save the notebook with a meaningful name and end the Spark session.

1. In the notebook menu bar, use the ⚙️ **Settings** icon to view the notebook settings.
2. Set the **Name** of the notebook to **Explore data for data science**, and then close the settings pane.
3. On the notebook menu, select **Stop session** to end the Spark session.

## Clean up resources

In this exercise, you've created and used notebooks for data exploration. You've also executed code to calculate summary statistics, and create visualizations to better understand the patterns and relationships in the data.

If you've finished exploring your model and experiments, you can delete the workspace you created for this exercise.

1. In the bar on the left, select the icon for your workspace to view all of the items it contains.
2. In the **...** menu on the toolbar, select **Workspace settings**.
3. In the **Other** section, select **Remove this workspace**.
