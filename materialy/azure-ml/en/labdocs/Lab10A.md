# Lab 10A: Monitoring a Model

When you deploy a model as a service, it's useful to be able to track information about the requests it processes and how the deployment is performing.

Azure Machine Learning gives you two complementary ways to do this for a managed online endpoint:

- **Application Insights integration** - a deployment-level setting (`app_insights_enabled=True`) that sends built-in request and latency telemetry, plus anything your scoring script prints to STDOUT, to the Application Insights resource linked to your workspace.
- **Azure Machine Learning model monitoring** - a richer capability built on top of the endpoint's *data collection* feature, which tracks data drift, prediction drift, and data quality over time. Model monitoring is covered in [Lab 10B](Lab10B.md); this lab enables the data collection it depends on.

## Before You Start

Before you start this lab, ensure that you have completed [Lab 1A](Lab01A.md) and [Lab 1B](Lab01B.md), which include tasks to create the Azure Machine Learning workspace and other resources used in this lab. Ideally, you should also have completed [Lab 7A](Lab07A.md), which creates the `diabetes-endpoint` managed online endpoint that this lab reuses - but the notebook creates the endpoint for you if it doesn't already exist.

## Task 1: Monitor a Model with Application Insights and Data Collection

In this task, you'll deploy the diabetes classification model to a managed online endpoint with Application Insights diagnostics and data collection enabled, then inspect the telemetry it produces.

1. In [Azure Machine Learning studio](https://ml.azure.com), view the **Compute** page for your workspace; and on the **Compute Instances** tab, ensure your compute instance is running. If not, start it.
2. When the compute instance is running, click the **JupyterLab** link to open JupyterLab in a new browser tab.
3. In JupyterLab, open the **10A - Monitoring a Model.ipynb** notebook. Then read the notes in the notebook, running each code cell in turn.
4. Confirm that the notebook uses the **Python 3.10 - SDK v2** kernel, or another current Azure ML SDK v2 kernel.

> **Note**: If you intend to continue straight to the [next exercise](Lab10B.md), leave your compute instance running. If you're taking a break, you might want to close all Jupyter tabs and **Stop** your compute instance to avoid incurring unnecessary costs.
