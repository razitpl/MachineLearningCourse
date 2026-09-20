# Lab 9A: Reviewing Automated Machine Learning Explanations

When you use automated machine learning to train a model, you can set `enable_model_explainability=True` on the job so that Azure Machine Learning automatically computes **feature importance** explanations for the best model.

> **Note**: Explanations are surfaced on the **Explanations (preview)** tab in Azure Machine Learning studio. There's no SDK method for pulling those insights back into a notebook, so in this lab you review them in Studio rather than print them in Python.
>
> The full **Responsible AI dashboard** - with error analysis, fairness and causal analysis - is **not available for AutoML models**; Studio shows "Responsible AI dashboard is currently not supported for AutoML models" there. You'll build such a dashboard in [Lab 9B](Lab09B.md), for a model trained with an ordinary script.

## Before You Start

Before you start this lab, ensure that you have completed [Lab 1A](Lab01A.md) and [Lab 1B](Lab01B.md), which include tasks to create the Azure Machine Learning workspace and other resources used in this lab.

## Task 1: Review Feature Importance for Automated Machine Learning Models

In this task, you'll run an automated machine learning job with model explainability enabled, and explore the feature importance information it generates.

1. In [Azure Machine Learning studio](https://ml.azure.com), view the **Compute** page for your workspace; and on the **Compute Instances** tab, ensure your compute instance is running. If not, start it.
2. When the compute instance is running, click the **JupyterLab** link to open JupyterLab in a new browser tab.
3. In the JupyterLab file browser, open the **MachineLearningCourse/materialy/azure-ml/en** repository folder, and then open the **09A - Reviewing Automated Machine Learning Explanations.ipynb** notebook. Then read the notes in the notebook, running each code cell in turn.
4. When the AutoML job has completed, use the Studio link printed by the notebook to open the job, select the best model, and view its **Explanations (preview)** tab to see the feature importance for the raw and engineered features.

> **Note**: If you intend to continue straight to the [next exercise](Lab09B.md), leave your compute instance running. If you're taking a break, you might want to close all Jupyter tabs and **Stop** your compute instance to avoid incurring unnecessary costs.
