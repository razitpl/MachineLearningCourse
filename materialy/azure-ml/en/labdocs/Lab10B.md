# Lab 10B: Monitoring Data Drift

Changing trends in data over time can reduce the accuracy of the predictions made by a model. Monitoring for this *data drift* and retraining as necessary is an important way to ensure your machine learning solution continues to predict accurately.

> **How data drift monitoring works**: **Azure Machine Learning model monitoring** compares your model's real production traffic - captured automatically by the *data collector* on a deployed online endpoint - against a reference dataset, on a recurring schedule. That means it needs a model already deployed to a managed online endpoint with data collection enabled, some scored production requests, and a monitoring schedule.

## Before You Start

Before you start this lab, ensure that you have completed [Lab 1A](Lab01A.md) and [Lab 1B](Lab01B.md), which include tasks to create the Azure Machine Learning workspace and other resources used in this lab. You must also have completed:

- [Lab 7A](Lab07A.md), which deploys the `diabetes_model` model to the `diabetes-endpoint` managed online endpoint (deployment `blue`).
- [Lab 10A](Lab10A.md), which enables Application Insights diagnostics and **data collection** on that same `blue` deployment - data collection is what supplies the production data this lab's monitor analyzes.

## Task 1: Configure Model Monitoring for Data Drift

In this task, you'll simulate some scored requests against the deployed model, then create a data drift monitoring schedule that compares those requests against the `diabetes_mltable` training data.

1. In [Azure Machine Learning studio](https://ml.azure.com), view the **Compute** page for your workspace; and on the **Compute Instances** tab, ensure your compute instance is running. If not, start it.
2. When the compute instance is running, click the **JupyterLab** link to open JupyterLab in a new browser tab.
3. In JupyterLab, open the **10B - Monitoring Data Drift.ipynb** notebook. Then read the notes in the notebook, running each code cell in turn.
4. Confirm that the notebook uses the **Python 3.10 - SDK v2** kernel, or another current Azure ML SDK v2 kernel.

## Task 2: View Monitoring Results

1. In [Azure Machine Learning studio](https://ml.azure.com), select **Manage** > **Monitoring**.
2. Select the **diabetes-model-monitor** schedule you created in the notebook.
3. After the schedule has run at least once, review the **data drift** signal - the overall drift score and the per-feature contribution.

   > **Note**: The monitor runs on the daily schedule defined in the notebook, so results won't be available immediately. You can trigger an on-demand run from the schedule's page in Studio if you don't want to wait.

> **Note**: If you have finished with the labs in this course, you might want to close all Jupyter tabs and **Stop** your compute instance to avoid incurring unnecessary costs. If you don't intend to work with your Azure Machine Learning workspace again, delete the resource group in which it is defined in your Azure subscription.
