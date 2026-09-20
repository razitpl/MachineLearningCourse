# Lab 9B: Interpreting Models

When you train your own models, you can use *explainers* to determine feature importance.

This lab has two distinct parts:

- A **local, offline** part - using the open-source `interpret-community` package's `TabularExplainer` directly against a model trained in the notebook. This needs no connection to Azure ML at all.
- A part that attaches explanations to a model **registered in your Azure ML workspace**, by building a **Responsible AI (RAI) dashboard**: a pipeline job made up of registered RAI components that runs against an already-registered (MLflow-format) model and produces a dashboard you view in Studio.

## Before You Start

Before you start this lab, ensure that you have completed [Lab 1A](Lab01A.md) and [Lab 1B](Lab01B.md), which include tasks to create the Azure Machine Learning workspace and other resources used in this lab, and [Lab 3B](Lab03B.md), which registers the `diabetes_model` used later in this lab.

## Task 1: Interpret Models Locally

In this task, you'll use the `interpret-community` package to interpret a model trained directly in the notebook, without any connection to Azure Machine Learning.

1. In [Azure Machine Learning studio](https://ml.azure.com), view the **Compute** page for your workspace; and on the **Compute Instances** tab, ensure your compute instance is running. If not, start it.
2. When the compute instance is running, click the **JupyterLab** link to open JupyterLab in a new browser tab.
3. In the JupyterLab file browser, open the **MachineLearningCourse/materialy/azure-ml/en** repository folder, and then open the **09B - Interpreting Models.ipynb** notebook. Then read the notes in the notebook, running each code cell in turn.

## Task 2: Build a Responsible AI Dashboard for a Registered Model

In this task, you'll build a Responsible AI dashboard for the `diabetes_model` that was registered in [Lab 3B](Lab03B.md), using the **wizard in Azure Machine Learning studio** - no code required.

1. Run the notebook cell that checks the model and the data asset are in place; it prints their names and versions.
2. In Studio, go to **Models**, select **diabetes_model**, and on the **Details** tab choose **Create Responsible AI dashboard (preview)**.
3. Work through the wizard:

   | Section | Choice |
   |---|---|
   | Training dataset | `diabetes_mltable` |
   | Test dataset | `diabetes_mltable` |
   | Modeling task | Classification |
   | Dashboard components | Model debugging |
   | Component parameters | Target feature: `Diabetic`, **Generate explanations** on |
   | Experiment configuration | dashboard name, experiment, `aml-cluster` compute |

4. Select **Create**. The job takes some fifteen minutes.
5. When it finishes, go back to **diabetes_model** and open its **Responsible AI** tab. Select the dashboard, view the **Aggregate feature importance** chart, then switch to **Individual feature importance** and pick a data point. Open **Error analysis** to see where the model goes wrong most often.

> **Why the wizard rather than the SDK**: the wizard runs a pipeline built from RAI components published by Microsoft in the `azureml` registry. Those components can also be fetched in code, as the [documentation](https://learn.microsoft.com/azure/machine-learning/how-to-responsible-ai-insights-sdk-cli) describes, but they aren't available in every workspace - `registry_client.components.get()` then fails with `Could not find component with name`. The wizard is unaffected.

> **A common trap**: the RAI components accept data only in `mltable` format and models only in MLflow format. That's why you pick the `diabetes_mltable` asset and the `diabetes_model` registered as MLflow in [Lab 3B](Lab03B.md). A plain CSV file or a `joblib` model won't even appear in the wizard's lists.

> **Note**: If you intend to continue straight to the [next exercise](Lab10A.md), leave your compute instance running. If you're taking a break, you might want to close all Jupyter tabs and **Stop** your compute instance to avoid incurring unnecessary costs.
