---
lab:
    title: 'Use tools to optimize Power BI performance'
    module: 'Optimize enterprise-scale tabular models'
---

# Use tools to optimize Power BI performance

## Overview

**The estimated time to complete the lab is 30 minutes**

In this lab, you will learn how to use two external tools to help you develop, manage, and optimize data models and DAX queries.

In this lab, you learn how to use:

- Best Practice Analyzer (BPA) in Tabular Editor.

- DAX Studio.

## Get started

For this lab you'll install and use Tabular Editor, and DAX Studio to optimize a semantic model.

## Use Best Practice Analyzer

In this exercise, you will install Tabular Editor 2 and load Best Practice Analyzer (BPA) rules. You will review the BPA rules, and then address specific issues found in the data model.

*BPA is a free third-party tool that notifies you of potential modeling missteps or changes that you can make to improve your model design and performance. It includes recommendations for naming, user experience, and common optimizations that you can apply to improve performance. For more information, see [Best practice rules to improve your model's performance](https://powerbi.microsoft.com/blog/best-practice-rules-to-improve-your-models-performance/).*

### Download and Install Tabular Editor 2

Download and install Tabular Editor 2 to enable the creation of calculation groups.

***Important**: If you have already installed Tabular Editor 2 in your VM
environment, continue to the next task.*

*Tabular Editor is an alternative tool for authoring tabular models for Analysis Services and Power BI. Tabular Editor 2 is an open source project that can edit a BIM file without accessing any data in the model.*

1. Ensure Power BI Desktop is closed.

1. In Microsoft Edge, navigate to the Tabular Editor Release page.

    ```https://github.com/TabularEditor/TabularEditor/releases```

1. Scroll down to the **Assets** section and select the **TabularEditor.Installer.msi** file. This will initiate the file install.

1. Upon completion, select **Open file** to run the installer.

1. In the Tabular Editor installer window, select **Next**.

1. At the **License Agreement** step, if you agree, select **I agree**, and then select **Next**.

1. At the **Select Installation Folder** step, select **Next**.

1. At the **Application Shortcuts** step, select **Next**.

1. At the **Confirm Installation** step, select **Next**.

    *If prompted, select **Yes** to allow app to make changes.*

1. When the installation is complete, select **Close**.

    *Tabular Editor is now installed and registered as a Power BI Desktop external tool.*

### Set up Power BI Desktop

In this task, you will open a pre-developed Power BI Desktop solution.

1. Download the [Sales Analysis starter file](https://aka.ms/fabric-optimize-starter) from `https://aka.ms/fabric-optimize-starter` and save it to a location you will remember.

1. Navigate to the downloaded file and open it in Power BI Desktop.

1. Select the **External Tools** ribbon tab.

    ![](Images/use-tools-to-optimize-power-bi-performance-image8.png)

1. Notice that you can launch Tabular Editor from this ribbon tab.

    ![](Images/use-tools-to-optimize-power-bi-performance-image9.png)

    *Later in this exercise, you will use Tabular Editor to work with BPA.*

### Review the data model

In this task, you will review the data model.

1. In Power BI Desktop, at the left, switch to **Model** view.

    ![](Images/use-tools-to-optimize-power-bi-performance-image10.png)

2. Use the model diagram to review the model design.

    ![](Images/use-tools-to-optimize-power-bi-performance-image11.png)

    *The model comprises eight dimension tables and one fact table. The **Sales** fact table stores sales order details. It's a classic star schema design that includes snowflake dimension tables (**Category** > **Subcategory** > **Product**) for the product dimension.*

    *In this exercise, you will use BPA to detect model issues and fix them.*

### Load BPA rules

In this task, you will load BPA rules.

*The BPA rules aren't added during the Tabular Editor installation. You must download and install them.*

1. On the **External Tools** ribbon, select **Tabular Editor**.

    ![](Images/use-tools-to-optimize-power-bi-performance-image12.png)

    *Tabular Editor opens in a new window and connects live to the data model hosted in Power BI Desktop. Changes made to the model in Tabular Editor aren't propagated to Power BI Desktop until you save them.*

2. To load the BPA rules, select the **C# Script** tab.

    *Note: This may be called the Advanced Scripting tab in older versions of Tabular Editor.*

    ![](Images/use-tools-to-optimize-power-bi-performance-image13.png)

3. Paste in the following script.

    ```csharp
    System.Net.WebClient w = new System.Net.WebClient(); 

    string path = System.Environment.GetFolderPath(System.Environment.SpecialFolder.LocalApplicationData);
    string url = "https://raw.githubusercontent.com/microsoft/Analysis-Services/master/BestPracticeRules/BPARules.json";
    string downloadLoc = path+@"\TabularEditor\BPARules.json";
    w.DownloadFile(url, downloadLoc);
    ```

4. To run the script, on the toolbar, select the **Run script** command.

    ![](Images/use-tools-to-optimize-power-bi-performance-image14.png)

    *To use the BPA rules, you must close and then reopen Tabular Editor.*

5. Close Tabular Editor.

6. To reopen Tabular Editor, in Power BI Desktop, on the **External Tools** ribbon, select **Tabular Editor**.

    ![](Images/use-tools-to-optimize-power-bi-performance-image15.png)

### Review the BPA rules

In this task, you will review the BPA rules that you loaded in the previous task.

1. In Tabular Editor, on the menu, select **Tools** > **Manage BPA Rules**.

    ![](Images/use-tools-to-optimize-power-bi-performance-image16.png)

2. In the **Manage Best Practice Rules** window, in the **Rule collections** list, select **Rules for the local user**.

    ![](Images/use-tools-to-optimize-power-bi-performance-image17.png)

3. In the **Rules in collection** list, scroll down the list of rules.

    *Tip: You can drag the bottom right corner to enlarge the window.*

    *Within seconds, Tabular Editor can scan your entire model against each of the rules and provides a report of all the model objects which satisfy the condition in each rule.*

4. Notice that BPA groups the rules into categories.

    *Some rules, like DAX expressions, focus on performance optimization while others, like the formatting rules, are aesthetic-oriented.*

5. Notice the **Severity** column.

    *The higher the number, the more important the rule.*

6. Scroll to the bottom of the list, and then uncheck the **Set IsAvailableInMdx to false on non-attribute columns** rule.

    ![](Images/use-tools-to-optimize-power-bi-performance-image18.png)

    *You can disable individual rules or entire categories of rules. BPA won't check disabled rules against your model. The removal of this specific rule is to show you how to disable a rule.*

7. Select **OK**.

    ![](Images/use-tools-to-optimize-power-bi-performance-image19.png)

### Address BPA issues

In this task, you will open BPA and review the results of the checks.

1. On the menu, select **Tools** > **Best Practice Analyzer** (or press **F10**).

    ![](Images/use-tools-to-optimize-power-bi-performance-image20.png)

2. In the **Best Practice Analyzer** window, if necessary, maximize the window.

3. Notice the list of (possible) issues, grouped by category.

4. In the first category, right-click the **'Product'** table, and then select **Ignore item**.

    ![](Images/use-tools-to-optimize-power-bi-performance-image21.png)

    *When an issue isn't really an issue, you can ignore that item. You can always reveal ignored items by using the **Show ignored** command on the toolbar.*

5. Further down the list, in the **Use the DIVIDE function for division** category, right-click **[Profit Margin]**, and then select **Go to object**.

    ![](Images/use-tools-to-optimize-power-bi-performance-image22.png)

    *This command switches to Tabular Editor and focuses on the object. It makes it easy to apply a fix to the issue.*

6. In the Expression Editor, modify the DAX formula to use the more efficient (and safe) [DIVIDE](https://docs.microsoft.com/dax/divide-function-dax) function, as follows.

    *Tip: All formulas are available to copy and paste from the **D:\fabric\Allfiles\Labs\16\Snippets.txt**.*

    ```dax
    DIVIDE ( [Profit], SUM ( 'Sales'[Sales Amount] ) )
    ```

7. To save the model changes, on the toolbar, select the **Save changes to the connected database** command (or press **Ctrl+S**).

    ![](Images/use-tools-to-optimize-power-bi-performance-image23.png)

    *Saving changes pushes modifications to the Power BI Desktop data model.*

8. Switch back to the (out of focus) **Best Practice Analyzer** window.

9. Notice that BPA no longer lists the issue.

10. Scroll down the list of issues to locate the **Provide format string for "Date" columns** category.

    ![](Images/use-tools-to-optimize-power-bi-performance-image24.png)

11. Right-click the **'Date'[Date]** issue, and then select **Generate fix script**.

    ![](Images/use-tools-to-optimize-power-bi-performance-image25.png)

    *This command generates a C# script and copies it to the clipboard. You can also use the **Apply fix** command to generate and run the script, however it might be safer to review (and modify) the script before you run it.*

12. When notified that BPA has copied the fix script to the clipboard, select **OK**.

13. Switch to Tabular Editor, and select the **C# Script** tab.

    *Note: This may be called the Advanced Scripting tab in older versions of Tabular editor.*

    ![](Images/use-tools-to-optimize-power-bi-performance-image13.png)

14. To paste the fix script, right-click inside the pane, and then press **Ctrl+V**.

    ![](Images/use-tools-to-optimize-power-bi-performance-image27.png)

    *You can choose to make a change to the format string.*

15. To run the script, on the toolbar, select the **Run script** command.

    ![](Images/use-tools-to-optimize-power-bi-performance-image14.png)

16. Save the model changes.

17. To close Tabular Editor, on the menu, select **File** > **Exit**.

18. Save the Power BI Desktop file.

    ![](Images/use-tools-to-optimize-power-bi-performance-image29.png)

    *You must also save the Power BI Desktop file to ensure the Tabular Editor changes are saved.*

    *In the message about pending changes, select **Apply later**.*

## Use DAX Studio

In this exercise, you'll use DAX Studio to optimize DAX queries in the Power BI report file.

*According to its website, DAX Studio is "the ultimate tool for executing and analyzing DAX queries against Microsoft Tabular models." It's a feature-rich tool for DAX authoring, diagnosis, performance tuning, and analysis. Features include object browsing, integrated tracing, query execution breakdowns with detailed statistics, DAX syntax highlighting and formatting.*

### Download DAX Studio

In this task, you will download DAX Studio.

1. In Microsoft Edge, navigate to the DAX Studio downloads page.

    ```https://daxstudio.org/downloads/```

1. Select **DaxStudio_3_X_XX_setup.exe (installer)** - this will initiate the file install.
    *note: The version of DAX studio will change slightly over time. Download the latest release.*

1. Upon completion, select **Open file** to run the installer.

    ![Graphical user interface, application Description automatically generated](Images/use-tools-to-optimize-power-bi-performance-image31b.png)

1. In the DAX Studio installer window, select **Install for all users (recommended)**.

1. In the User Account Control window, select Yes to allow the app to make changes to the device.

    ![Graphical user interface, application Description automatically generated](Images/use-tools-to-optimize-power-bi-performance-image31c.png)

1. At the **License Agreement** step, if you accept the license terms, select **I accept the agreement**, and then select **Next**.

    ![Graphical user interface, application Description automatically
    generated](Images/use-tools-to-optimize-power-bi-performance-image31d.png)

1. Select **Next** to use the default destination location.
1. Select **Next** to select the default components to install.
1. Select **Next** to place the shortcut on the default start menu folder.
1. Select **Create a desktop shortcut** and select next.

    ![Graphical user interface, application Description automatically
    generated](Images/use-tools-to-optimize-power-bi-performance-image31e.png)
1. Select **Install**.

1. Upon completion, with **Launch DAX Studio** selected, select **Finish**. This will open DAX Studio.
    ![Graphical user interface, application Description automatically
    generated](Images/use-tools-to-optimize-power-bi-performance-image31f.png)

1. In the **Connect** window, select the **Power BI / SSDT Model** option.

1. In the corresponding dropdown list, ensure the **Sales Analysis - Use tools to optimize Power BI performance** model is selected.

    ![](Images/use-tools-to-optimize-power-bi-performance-image30.png)

    *If you do not have the **Sales Analysis - Use tools to optimize Power BI performance** starter file open, you will not be able to connect. Be sure the file is open.*

1. Select **Connect**.

    ![](Images/use-tools-to-optimize-power-bi-performance-image31.png)

1. If necessary, maximize the DAX Studio window.

### Use DAX studio to optimize a query

In this task, you will optimize a query by using an improved measure formula.

*Note that it's difficult to optimize a query when the data model volumes are small. This exercise focuses on using DAX Studio rather than optimizing DAX queries.*

1. In a browser window download the [Monthly Profit Growth.dax](https://aka.ms/fabric-optimize-dax) files from `https://aka.ms/fabric-optimize-dax` and save it on your local computer (in any folder).

   ![](https://github.com/MicrosoftLearning/mslearn-fabric/assets/34583336/58254cce-753e-4322-9060-536e12554aa7)

3. Switch to the Dax Studio window and from the **File** menu, select **Browse** to navigate to the **Monthly Profit Growth.dax** file and **Open** the file.

    ![](Images/use-tools-to-optimize-power-bi-performance-image33.png)

6. Read the comments at the top of the file, and then review the query that follows.

    *It's not important to understand the query in its entirety.*

    *The query defines two measures that determine monthly profit growth. Currently, the query only uses the first measure (at line 72). When a measure isn't used, it doesn't impact on the query execution.*

7. To run a server trace to record detailed timing information for performance profiling, on the **Home** ribbon tab, from inside the **Traces** group, select **Server Timings**.

    ![](Images/use-tools-to-optimize-power-bi-performance-image34.png)

8. To run the script, on the **Home** ribbon tab, from inside the **Query** group, select the **Run** icon.

    ![](Images/use-tools-to-optimize-power-bi-performance-image35.png)

9. In the lower pane, review the query result.

    *The last column displays the measure results.*

10. In the lower pane, select the **Server Timings** tab.

    ![](Images/use-tools-to-optimize-power-bi-performance-image36.png)

11. Review the statistics available at the left side.

    ![](Images/use-tools-to-optimize-power-bi-performance-image37.png)

    *From top left to bottom right, the statistics tell you how many milliseconds it took to run the query, and the duration the storage engine (SE) CPU took. In this case (your results will differ), the formula engine (FE) took 73.5% of the time, while the SE took the remaining 26.5% of the time. There were 34 individual SE queries and 21 cache hits.*

12. Run the query again, and notice that all SE queries come from the SE cache.

    *That's because the results were cached for reuse. Sometimes in your testing, you may want to clear the cache. In that case, on the **Home** ribbon tab, by selecting the down arrow for the **Run** command.*

    ![](Images/use-tools-to-optimize-power-bi-performance-image38.png)

    *The second measure definition provides a more efficient result. You will now update the query to use the second measure.*

13. At line 72, replace the word **Bad** with **Better**.

    ![](Images/use-tools-to-optimize-power-bi-performance-image39.png)

14. Run the query, and then review the server timing statistics.

    ![](Images/use-tools-to-optimize-power-bi-performance-image40.png)

15. Run it a second time to result in full cache hits.

    ![](Images/use-tools-to-optimize-power-bi-performance-image41.png)

    *In this case, you can determine that the "better" query, which uses variables and a time intelligence function, performs better-almost a 50% reduction in query execution time.*

### Finish up

To conclude this exercise, close all applications - there's no need to save the files.
