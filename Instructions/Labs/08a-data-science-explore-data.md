---
lab:
    title: 'Create and use notebooks for data exploration'
    module: 'Explore data for data science with notebooks in Microsoft Fabric'
---

# Use notebooks to explore data in Microsoft Fabric

In this lab, we'll use notebooks for data exploration. Notebooks are a powerful tool for interactively exploring and analyzing data. During this exercise, we'll learn how to create and use notebooks to explore a dataset, generate summary statistics, and create visualizations to better understand the data. By the end of this lab, you'll have a solid understanding of how to use notebooks for data exploration and analysis.

This lab takes approximately **30** minutes to complete.

> **Note**: You need a Microsoft *school* or *work* account to complete this exercise. If you don't have one, you can [sign up for a trial of Microsoft Office 365 E3 or higher](https://www.microsoft.com/microsoft-365/business/compare-more-office-365-for-business-plans).

## Create a workspace

Before working with data in Fabric, create a workspace with the Fabric trial enabled.

1. Navigate to the [Microsoft Fabric home page](https://app.fabric.microsoft.com) at `https://app.fabric.microsoft.com` in a browser.
1. Select **Synapse Data Science**.
1. In the menu bar on the left, select **Workspaces** (the icon looks similar to &#128455;).
1. Create a new workspace with a name of your choice, selecting a licensing mode that includes Fabric capacity (*Trial*, *Premium*, or *Fabric*).
1. When your new workspace opens, it should be empty.

    ![Screenshot of an empty workspace in Fabric.](./Images/new-workspace.png)

## Create a notebook

To train a model, you can create a *notebook*. Notebooks provide an interactive environment in which you can write and run code (in multiple languages) as *experiments*.

1. In the **Synapse Data Science** home page, create a new **Notebook**.

    After a few seconds, a new notebook containing a single *cell* will open. Notebooks are made up of one or more cells that can contain *code* or *markdown* (formatted text).

1. Select the first cell (which is currently a *code* cell), and then in the dynamic tool bar at its top-right, use the **M&#8595;** button to convert the cell to a *markdown* cell.

    When the cell changes to a markdown cell, the text it contains is rendered.

1. Use the **&#128393;** (Edit) button to switch the cell to editing mode, then delete the content and enter the following text:

    ```text
   # Perform data exploration for data science

   Use the code in this notebook to perform data exploration for data science.
    ``` 

## Load data into a dataframe

Now you're ready to run code to get data. You'll work with the [**diabetes dataset**](https://learn.microsoft.com/azure/open-datasets/dataset-diabetes?tabs=azureml-opendatasets?azure-portal=true) from the Azure Open Datasets. After loading the data, you'll convert the data to a Pandas dataframe, which is a common structure for working with data in rows and columns.

1. In your notebook, use the **+ Code** icon below the latest cell to add a new code cell to the notebook. Enter the following code to load the dataset into a dataframe.

    ```python
    # Azure storage access info for open dataset diabetes
    blob_account_name = "azureopendatastorage"
    blob_container_name = "mlsamples"
    blob_relative_path = "diabetes"
    blob_sas_token = r"" # Blank since container is Anonymous access
    
    # Set Spark config to access  blob storage
    wasbs_path = f"wasbs://%s@%s.blob.core.windows.net/%s" % (blob_container_name, blob_account_name, blob_relative_path)
    spark.conf.set("fs.azure.sas.%s.%s.blob.core.windows.net" % (blob_container_name, blob_account_name), blob_sas_token)
    print("Remote blob path: " + wasbs_path)
    
    # Spark read parquet, note that it won't load any data yet by now
    df = spark.read.parquet(wasbs_path)
    ```

1. Use the **&#9655; Run cell** button on the left of the cell to run it. Alternatively, you can press `SHIFT` + `ENTER` on your keyboard to run a cell.

    > **Note**: Since this is the first time you've run any Spark code in this session, the Spark pool must be started. This means that the first run in the session can take a minute or so to complete. Subsequent runs will be quicker.

1. Use the **+ Code** icon below the cell output to add a new code cell to the notebook, and enter the following code in it:

    ```python
    display(df)
    ```

1. When the cell command has completed, review the output below the cell, which should look similar to this:

    |AGE|SEX|BMI|BP|S1|S2|S3|S4|S5|S6|Y|
    |---|---|---|--|--|--|--|--|--|--|--|
    |59|2|32.1|101.0|157|93.2|38.0|4.0|4.8598|87|151|
    |48|1|21.6|87.0|183|103.2|70.0|3.0|3.8918|69|75|
    |72|2|30.5|93.0|156|93.6|41.0|4.0|4.6728|85|141|
    |24|1|25.3|84.0|198|131.4|40.0|5.0|4.8903|89|206|
    |50|1|23.0|101.0|192|125.4|52.0|4.0|4.2905|80|135|
    | ... | ... | ... | ... | ... | ... | ... | ... | ... | ... | ... |

    The output shows the rows and columns of the diabetes dataset.

1. The data is loaded as a Spark dataframe. Scikit-learn will expect the input dataset to be a Pandas dataframe. Run the code below to convert your dataset to a Pandas dataframe:

    ```python
    df = df.toPandas()
    df.head()
    ```

## Check the shape of the data

Now that you've loaded the data, you can check the structure of the dataset, such as the number of rows and columns, data types, and missing values.

1. Use the **+ Code** icon below the cell output to add a new code cell to the notebook, and enter the following code in it:

    ```python
    # Display the number of rows and columns in the dataset
    print("Number of rows:", df.shape[0])
    print("Number of columns:", df.shape[1])

    # Display the data types of each column
    print("\nData types of columns:")
    print(df.dtypes)
    ```

    The dataset contains **442 rows** and **11 columns**. This means you have 442 samples and 11 features or variables in your dataset. The `SEX` variable likely contains categorical or string data.

## Check for missing data

1. Use the **+ Code** icon below the cell output to add a new code cell to the notebook, and enter the following code in it:

    ```python
    missing_values = df.isnull().sum()
    print("\nMissing values per column:")
    print(missing_values)
    ```

    The code checks for missing values. Observe that there's no missing data in the dataset.

## Generate descriptive statistics for numerical variables

Now, let's generate descriptive statistics to understand the distribution of numerical variables.

1. Use the **+ Code** icon below the cell output to add a new code cell to the notebook, and enter the following code.

    ```python
    df.describe()
    ```

    The average age is approximately 48.5 years, with a standard deviation of 13.1 years. The youngest individual is 19 years old and the oldest is 79 years old. The average `BMI` is approximately 26.4, which falls in the **overweight** category according to [WHO standards](https://www.who.int/health-topics/obesity#tab=tab_1). The minimum `BMI` is 18 and the maximum is 42.2.

## Plot the data distribution

Let's verify the `BMI` feature, and plot its distribution to get a better understanding of its characteristics.

1. Add another code cell to the notebook. Then, enter the following code into this cell and execute it.

    ```python
    import matplotlib.pyplot as plt
    import seaborn as sns
    import numpy as np
    
    # Calculate the mean, median of the BMI variable
    mean = df['BMI'].mean()
    median = df['BMI'].median()
    
    # Histogram of the BMI variable
    plt.figure(figsize=(8, 6))
    plt.hist(df['BMI'], bins=20, color='skyblue', edgecolor='black')
    plt.title('BMI Distribution')
    plt.xlabel('BMI')
    plt.ylabel('Frequency')
    
    # Add lines for the mean and median
    plt.axvline(mean, color='red', linestyle='dashed', linewidth=2, label='Mean')
    plt.axvline(median, color='green', linestyle='dashed', linewidth=2, label='Median')
    
    # Add a legend
    plt.legend()
    plt.show()
    ```

    From this graph, you're able to observe the range and distribution of `BMI` in the dataset. For example, most of `BMI` fall within 23.2 and 29.2, and the data is right skewed.

## Perform multivariate analysis

Let's generate visualizations such as scatter plots and box plots to uncover patterns and relationships within the data.

1. Use the **+ Code** icon below the cell output to add a new code cell to the notebook, and enter the following code.

    ```python
    import matplotlib.pyplot as plt
    import seaborn as sns

    # Scatter plot of Quantity vs. Price
    plt.figure(figsize=(8, 6))
    sns.scatterplot(x='BMI', y='Y', data=df)
    plt.title('BMI vs. Target variable')
    plt.xlabel('BMI')
    plt.ylabel('Target')
    plt.show()
    ```
    
    We can see that as the `BMI` increases, the target variable also increases, indicating a positive linear relationship between these two variables.

1. Add another code cell to the notebook. Then, enter the following code into this cell and execute it.

    ```python
    import seaborn as sns
    import matplotlib.pyplot as plt
    
    fig, ax = plt.subplots(figsize=(7, 5))
    
    # Replace numeric values with labels
    df['SEX'] = df['SEX'].replace({1: 'Male', 2: 'Female'})
    
    sns.boxplot(x='SEX', y='BP', data=df, ax=ax)
    ax.set_title('Blood pressure across Gender')
    plt.tight_layout()
    plt.show()
    ```

    These observations suggest that there are differences in the blood pressure profiles of male and female patients. On average, female patients have a higher blood pressure than male patients.

1. Aggregating the data can make it more manageable for visualization and analysis. Add another code cell to the notebook. Then, enter the following code into this cell and execute it.

    ```python
    import matplotlib.pyplot as plt
    import seaborn as sns
    
    # Calculate average BP and BMI by SEX
    avg_values = df.groupby('SEX')[['BP', 'BMI']].mean()
    
    # Bar chart of the average BP and BMI by SEX
    ax = avg_values.plot(kind='bar', figsize=(15, 6), edgecolor='black')
    
    # Add title and labels
    plt.title('Avg. Blood Pressure and BMI by Gender')
    plt.xlabel('Gender')
    plt.ylabel('Average')
    
    # Display actual numbers on the bar chart
    for p in ax.patches:
        ax.annotate(format(p.get_height(), '.2f'), 
                    (p.get_x() + p.get_width() / 2., p.get_height()), 
                    ha = 'center', va = 'center', 
                    xytext = (0, 10), 
                    textcoords = 'offset points')
    
    plt.show()
    ```

    This graph shows that the average blood pressure is higher in female patients compared to male patients. Additionally, it shows that the average Body Mass Index (BMI) is slightly higher in females than in males.

1. Add another code cell to the notebook. Then, enter the following code into this cell and execute it.

    ```python
    import matplotlib.pyplot as plt
    import seaborn as sns
    
    plt.figure(figsize=(10, 6))
    sns.lineplot(x='AGE', y='BMI', data=df, ci=None)
    plt.title('BMI over Age')
    plt.xlabel('Age')
    plt.ylabel('BMI')
    plt.show()
    ```

    The age group of 19 to 30 years has the lowest average BMI values, while the highest average BMI is found in the age group of 65 to 79 years. Additionally, observe that the average BMI for most age groups falls within the overweight range.

## Correlation analysis

Let's calculate correlations between different features to understand their relationships and dependencies.

1. Use the **+ Code** icon below the cell output to add a new code cell to the notebook, and enter the following code.

    ```python
    df.corr(numeric_only=True)
    ```

1. A heatmap is a useful tool for quickly visualizing the strength and direction of relationships between variable pairs. It can highlight strong positive or negative correlations, and identify pairs that lack any correlation. To create a heatmap, add another code cell to the notebook, and enter the following code.

    ```python
    plt.figure(figsize=(15, 7))
    sns.heatmap(df.corr(numeric_only=True), annot=True, vmin=-1, vmax=1, cmap="Blues")
    ```

    `S1` and `S2` variables have a high positive correlation of **0.89**, indicating that they move in the same direction. When `S1` increases, `S2` also tends to increase, and vice versa. Additionally, `S3` and `S4` have a strong negative correlation of **-0.73**. This means that as `S3` increases, `S4` tends to decrease.

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
