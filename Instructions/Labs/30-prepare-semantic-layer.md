---
lab:
    title: 'Prepare the semantic layer for AI in Microsoft Fabric'
    module: 'Prepare the semantic layer for AI in Microsoft Fabric'
    description: 'Configure Prep for AI features in Power BI Desktop, add synonyms via linguistic modeling, publish to a Fabric workspace, test with Copilot and HCAAT diagnostics, and mark the model as Approved for Copilot.'
    duration: 30 minutes
    level: 300
    islab: true
    primarytopics:
        - Power BI
        - Semantic models
        - Copilot
        - Prep for AI
    categories:
        - Semantic modeling
    courses:
        - DP-600
---

# Prepare the semantic layer for AI

Copilot in Power BI relies on semantic model metadata to generate accurate responses. Without preparation, Copilot might misinterpret business terminology, use the wrong measures, or return inconsistent answers. Preparing the semantic layer ensures that Copilot understands your data the way your business users do.

You learn how to:

- Simplify the AI data schema to focus Copilot on business-relevant fields.
- Write AI instructions that provide business context and terminology.
- Create verified answers that return predefined visuals for common questions.
- Add synonyms via Q&A linguistic modeling to improve natural language interpretation.
- Test your AI preparation using the Copilot pane and HCAAT diagnostics.
- Mark the semantic model as Approved for Copilot in the Power BI service.

This lab takes approximately **30** minutes to complete.

## Set up the environment

You need a paid Fabric capacity (F2 or higher) to complete this exercise. Copilot isn't supported on trial capacities. For more information, see [Fabric licenses](https://learn.microsoft.com/fabric/enterprise/licenses).

You need [Power BI Desktop](https://www.microsoft.com/download/details.aspx?id=58494) (November 2025 or newer) installed to complete this exercise. *Note: UI elements may vary slightly depending on your version.*

1. On the Fabric home page, select **Power BI**.

1. In the left navigation bar, select **Workspaces**, and then select **New workspace**.

1. Name the new workspace, such as **dp_fabric**, select a licensing mode that includes Fabric capacity (paid F2 or higher), and select **Apply**.

1. Open a web browser and enter the following URL to download the [30-prepare-data-ai zip folder](https://github.com/MicrosoftLearning/mslearn-fabric/raw/refs/heads/main/Allfiles/Labs/30/30-prepare-data-ai.zip):

    `https://github.com/MicrosoftLearning/mslearn-fabric/raw/refs/heads/main/Allfiles/Labs/30/30-prepare-data-ai.zip`

1. Save the file in **Downloads** and extract the zip file to the **30-prepare-data-ai** folder.

1. Open the **30-Starter-Sales Analysis.pbix** file from the folder you extracted.

    > Ignore and close any warnings asking to apply changes. Don't select *Discard changes*.

1. On the **Home** ribbon, select the **Copilot** button. When prompted to select a workspace, choose the workspace you created (for example, **dp_fabric**) and select **Connect**. This links Power BI Desktop to your Fabric capacity for Copilot features.

1. On the **Home** ribbon, locate the **Prep data for AI** button. If you don't see it, confirm that you have a current version of Power BI Desktop.

    > **Note**: *Prep for AI is a preview feature. If the tabs in the Prep for AI dialog appear disabled, navigate to **Modeling** > **Q&A setup** and enable Q&A for the model. Close the dialog and try again.*

> **Tip**: If you are in a lab VM and have any problems entering the text, you can download the [30-snippets.txt](https://github.com/MicrosoftLearning/mslearn-fabric/raw/refs/heads/main/Allfiles/Labs/30/30-snippets.txt) file from `https://github.com/MicrosoftLearning/mslearn-fabric/raw/refs/heads/main/Allfiles/Labs/30/30-snippets.txt`, saving it on the VM. The file contains all the text snippets and trigger phrases used in this lab.

## Simplify the data schema

Hiding technical fields helps Copilot focus on business-relevant data and produce more accurate responses. In this section, you use the Prep for AI dialog to deselect surrogate keys, sort columns, and ETL metadata from the AI data schema.

1. On the **Home** ribbon, select **Prep data for AI**.

1. In the dialog that opens, select **Simplify the data schema** from the left navigation or by selecting the card.

1. Review the list of tables and their fields. By default, all fields are visible to AI. Deselect fields that aren't relevant for business users:

    - From the **Date** table, deselect **Day (Sort Order)** and **Month (Sort Order)**. These are internal sort helper columns.
    - From the **Product** table, deselect **ProductKey**. This is a surrogate key used for relationships.
    - From the **Customer** table, deselect **CustomerKey**. This is a surrogate key used for relationships.
    - From the **Sales** table, deselect **LoadDate** and **SourceSystem**. These are ETL metadata columns. Also deselect **SalesOrderLineNumber**, which is an internal identifier.

1. Confirm that business-relevant fields remain selected: ProductName, Category, CustomerName, City, Year, Month, Total Sales, Profit, and Profit Margin.

1. Select **Apply**.

**Verify**: The Simplify data schema tab shows deselected fields for surrogate keys, sort columns, and ETL metadata. All business-relevant fields remain selected.

## Add AI instructions

Table names and field descriptions don't always capture your organization's business rules and terminology. In this section, you write AI instructions that provide Copilot with business context, including key terminology, analysis preferences, and data scope.

1. In the Prep for AI dialog, select **Add AI instructions** from the left navigation.

1. In the instructions text box, enter the following business context:

    ```text
    This model contains retail sales data for an outdoor recreation products company.

    Key business terminology:
    - "Revenue" and "sales" refer to the Total Sales measure.
    - Products are organized by Category and Subcategory.

    Analysis guidance:
    - When users ask about "top products," rank by Total Sales descending.
    - When users ask about profitability, use the Profit Margin measure (percentage), not the Profit measure (absolute amount).
    - Customers are identified by CustomerName, not CustomerKey.

    Data scope: This model covers retail sales from January 2024 through December 2025.
    ```

1. Select **Apply**.

1. Select **Close** to close the Prep for AI dialog and return to the **Report** view.

**Verify**: The AI instructions tab contains business context that clarifies terminology, analysis preferences, and data scope.

## Create verified answers

Predefined responses ensure that common business questions always return the same accurate visual. In this section, you create verified answers for two report visuals and assign trigger phrases that map to frequently asked questions.

1. Navigate to the **Sales Overview** report page.

1. Select the card visual that shows **Total Sales**.

1. Select the **...** menu on the visual, then select **Set verified answer**.

1. In the verified answer dialog, add the following trigger phrases. Press **Enter** after each one:

    - What were total sales?
    - Show me total revenue
    - How much did we sell?

1. Select **Apply**.

1. Next, select the bar chart visual that shows **Sales by Category**.

1. Select the **...** menu, then select **Set verified answer**.

1. Add the following trigger phrases:

    - Show me sales by category
    - Which product categories have the most sales?
    - Break down revenue by product category

1. Select **Apply**.

**Verify**: You created verified answers for two visuals with trigger phrases that describe common business questions.

## Add synonyms with linguistic modeling

Synonyms help Copilot and Q&A interpret natural language variations for field and measure names. In this section, you add synonyms to key fields using the Q&A linguistic modeling setup.

1. On the **Modeling** ribbon, select **Q&A setup**.

1. In the Q&A setup dialog, select the **Synonyms** tab.

1. Locate the **Total Sales** measure. Add the following synonyms, pressing **Enter** after each one:

    - revenue
    - income
    - total revenue

1. Locate the **Profit Margin** measure. Add the following synonyms:

    - margin
    - profitability
    - profit percentage

1. Locate **CustomerName** in the **Customer** table. Add the following synonyms:

    - customer
    - buyer
    - client

1. Locate **Category** in the **Product** table. Add the following synonym:

    - product category

1. Select **Close** to close the Q&A setup dialog.

**Verify**: The Synonyms tab shows the synonyms you added for Total Sales, Profit Margin, CustomerName, and Category.

## Publish to the Fabric workspace

Publishing the report makes the semantic model available in the Power BI service, where you can test with Copilot and mark the model as approved. In this section, you publish the report to the Fabric workspace you created during setup.

1. In Power BI Desktop, select **File** > **Save** to save your changes.

1. On the **Home** ribbon, select **Publish**.

1. In the **Publish to Power BI** dialog, select the workspace you created earlier (for example, **dp_fabric**), and then select **Select**.

1. Wait for publishing to complete. When the success message appears, select the link to open the report in the Power BI service, or navigate to your workspace in a browser.

**Verify**: The report and semantic model appear in your Fabric workspace.

## Test with Copilot

Validating your AI preparation confirms that Copilot uses the correct fields, measures, and verified answers. In this section, you test the published model with Copilot in the Power BI service, use HCAAT diagnostics to inspect Copilot's reasoning, and mark the model as Approved for Copilot.

1. In the Power BI service, open the published report from your workspace.

1. On the report toolbar, select the **Copilot** button to open the Copilot pane.

1. In the Copilot pane, select the **skill picker** (the icon at the bottom of the pane). Select **Answer questions about the data** to focus Copilot on data queries.

1. Ask a question that matches one of your verified answers:

    > What were total sales?

    Copilot should return the predefined card visual showing total sales, rather than generating a new response.

1. Ask the second verified-answer question:

    > Show me sales by category

    Copilot should return the bar chart visual you configured.

1. Ask a question that tests your synonyms and AI instructions:

    > Show me the top products by profitability

    Based on your AI instructions, Copilot should use the **Profit Margin** measure rather than the **Profit** measure. The synonym "profitability" should map correctly to **Profit Margin**.

1. Select the **How Copilot arrived at this answer** (HCAAT) link below the response. Review which fields, filters, and measures Copilot used. Confirm that Copilot selected the expected measure and didn't use any hidden fields.

1. Ask an open-ended question that doesn't have a verified answer:

    > Which city had the highest sales?

    Select the HCAAT link again and verify that Copilot uses **City** and **Total Sales** without referencing any surrogate keys or ETL columns.

1. If any response is inaccurate, note the question. You can improve accuracy by adjusting AI instructions, adding a verified answer, or refining synonyms.

**Verify**: Verified answers return predefined visuals. Open-ended questions use the correct measures and fields. HCAAT diagnostics confirm Copilot's reasoning matches your AI preparation.

### Mark the model as Approved for Copilot

Approving a semantic model signals to your organization that it's been validated and is ready for AI consumption. In this task, you mark the published semantic model as Approved for Copilot.

1. In your workspace, select the semantic model (not the report) to open the model details page.

1. On the model details page, select **Endorsement**.

1. In the Endorsement dialog, under **Copilot**, select **Approved**.

1. Select **Apply**.

**Verify**: The semantic model shows the **Approved** endorsement badge in the workspace list.

## Clean up resources

In this exercise, you configured Prep for AI features in Power BI Desktop, added synonyms, published the model to a Fabric workspace, tested with Copilot and HCAAT diagnostics, and marked the model as Approved for Copilot.

1. Navigate to your workspace in the Power BI service.
1. In the workspace settings, select **Other** and then select **Remove this workspace**.
