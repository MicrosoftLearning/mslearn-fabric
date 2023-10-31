---
lab:
    title: 'Generate and save batch predictions'
    module: 'Generate batch predictions using a deployed model in Microsoft Fabric'
---

# Generate and save batch predictions

In this lab, you'll use a machine learning model to predict a quantitative measure of diabetes. You'll use the PREDICT function in Fabric to generate the predictions with a registered model.

By completing this lab, you'll gain hands-on experience in generating predictions and visualizing the results.

This lab will take approximately **20** minutes to complete.

> **Note**: You'll need a Microsoft Fabric license to complete this exercise. See [Getting started with Fabric](https://learn.microsoft.com/fabric/get-started/fabric-trial) for details of how to enable a free Fabric trial license. You will need a Microsoft *school* or *work* account to do this. If you don't have one, you can [sign up for a trial of Microsoft Office 365 E3 or higher](https://www.microsoft.com/microsoft-365/business/compare-more-office-365-for-business-plans).

## Create a workspace

Before working with models in Fabric, create a workspace with the Fabric trial enabled.

1. Sign into [Microsoft Fabric](https://app.fabric.microsoft.com) at `https://app.fabric.microsoft.com` and select **Synapse Data Science**.
2. In the menu bar on the left, select **Workspaces** (the icon looks similar to &#128455;).
3. Create a new workspace with a name of your choice, selecting a licensing mode that includes Fabric capacity (*Trial*, *Premium*, or *Fabric*).
4. When your new workspace opens, it should be empty, as shown here:

    ![Screenshot of an empty workspace in Power BI.](./Images/new-workspace.png)

## Upload the notebook

To ingest data, train, and register a model, you'll run the cells in a notebook. You can upload the notebook to your workspace.

1. In a new browser tab navigate to `https://github.com/MicrosoftLearning/mslearn-fabric/blob/main/Allfiles/Labs/08/Generate-Predictions.ipynb`
1. Select the **Download raw file** icon to download the `Generate-Predictions` notebook to a folder of your choice.
1. Return to the Fabric tab and navigate to the **Home** page.
1. Select **Import notebook**.

    You'll get a notification when the notebook is imported successfully.

1. Navigate to the imported notebook named `Generate-Predictions`.
1. Read the instructions in the notebook carefully and run each cell individually.

## Clean up resources

In this exercise, you have used a model to generate batch predictions.

If you've finished exploring the notebook, you can delete the workspace that you created for this exercise.

1. In the bar on the left, select the icon for your workspace to view all of the items it contains.
2. In the **...** menu on the toolbar, select **Workspace settings**.
3. In the **Other** section, select **Remove this workspace** .
