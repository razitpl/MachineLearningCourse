# Lab 1A: Creating an Azure Machine Learning Workspace

In this lab, you will create the Azure Machine Learning workspace that you will use throughout the rest of this course.

## Before You Start

Azure Machine Learning (Azure ML) is a Microsoft Azure-based service for running data science and machine learning workloads at scale in the cloud. To use Azure Machine Learning, you will need an Azure subscription. Please use your student's subscription.

## Task 1: Create an Azure ML Workspace

As its name suggests, a workspace is a centralized place to manage all of the Azure ML assets you need to work on a machine learning project.

1. Sign into the [Azure portal](https://portal.azure.com) and create a new **Machine Learning** resource, specifying a unique workspace name and creating a new resource group in the **North Central US** or **UK South** region. Select the **Enterprise** workspace edition.

2. When the workspace and its associated resources have been created, view the workspace in the portal.

## Task 2: Explore the Azure ML Studio Interface

You can manage workspace assets in the Azure portal, but for data scientists, this tool contains lots of irrelevant information and links that relate to managing general Azure resources. An alternative, Azure ML-specific web interface for managing workspaces is available.

> **Note**: The web-based interface for Azure ML is named *Azure Machine Learning studio*, which you may find confusing as there is also a free *Azure Machine Learning Studio* product for creating machine learning models using a visual designer. 

1. In the Azure portal blade for your Azure Machine Learning workspace, click the link to launch **Azure Machine Learning studio**; or alternatively, in a new browser tab, open [https://ml.azure.com](https://ml.azure.com). If prompted, sign in using the Microsoft account you used in the previous task and select your Azure subscription and workspace.
2. View the Azure Machine Learning studio interface for your workspace - you can manage all of the assets in your workspace from here.

## Task 3: Create Compute Resources

One of the benefits of Azure Machine Learning is the ability to create cloud-based compute on which you can run experiments and training scripts at scale.

1. In the Azure Machine Learning studio web interface for your workspace, view the **Compute** page. This is where you'll manage all the compute targets for your data science activities.
2. On the **Compute instances** tab, add a new compute instance with the following settings. You'll use this instance to run JupyterLab and the Azure ML SDK v2 notebooks in the next lab.
    * **Compute name**: mymachine
    * **Virtual Machine size**: Standard_D2as_v4
    * **Enable idle shutdown**: 30 minutes (or less)
3. While the compute instance is being created, switch to the **Compute clusters** tab, and add a new compute cluster with the following settings:
    * **Compute name**: aml-cluster
    * **Virtual Machine size**: Standard_D2as_v4
    * **Minimum number of nodes**: 0
    * **Maximum number of nodes**: Depending on your quota (2-3 nodes is usually sufficient)
    * **Idle seconds before scale down**: 300

## Task 4: Create Data Resources

Now that you have some compute resources that you can use to process data, you'll need a way to store and ingest the data to be processed.

1. In the *Workspace* interface, view the **Data** page. Your Azure ML workspace already includes two datastores based on the Azure Storage account that was created along with the workspace. These are used to store notebooks, configuration files, and data.

   > **Note**: In a real-world environment, you'd likely add custom datastores that reference your business data stores - for example, Azure blob containers, Azure Data Lakes, Azure SQL Databases, and so on. You'll explore this later in the course.

2. In the *Data* interface, view the **Data assets** page. Datasets represent specific data files or tables that you plan to work with in Azure ML.
3. Download the [diabetes.csv](https://raw.githubusercontent.com/razitpl/MachineLearningCourse/master/materialy/azure-ml/en/data/diabetes.csv) file to your computer - you'll upload it in the next step.
4. Create a new dataset from local files, using the following settings:
    * **Name**: diabetes_dataset (*be careful to match the case and spacing*)
    * **Dataset type**: Tabular
    * **Description**: Diabetes data
    * **Destination storage type**: Azure Blob Storage (probably workspaceblobstore)
    * **Settings and preview**: Review the automatically detected settings.
    * **Schema**: Review the default column selections and data types.
5. After the dataset has been created, open it and view the **Explore** page to see a sample of the data. This data represents details from patients who have been tested for diabetes, and you will use it in many of the subsequent labs in this course.
    > **Note:** You can optionally generate a *profile* of the dataset to get a quick overview of its structure and quality. The profile can show information such as the number of rows and columns, detected data types, missing values, unique values, and basic statistics for numerical columns.
    >
    > Generating a profile does not modify the dataset or train a model. It simply helps you identify potential data-quality issues before you start working with the data. You will explore datasets and data profiling in more detail later in the course.
