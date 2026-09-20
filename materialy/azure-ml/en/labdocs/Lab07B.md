# Lab 7B: Creating a Batch Inferencing Service

In many scenarios, inferencing is performed as a batch process that uses a predictive model to score a large number of cases. To implement this kind of inferencing solution in Azure Machine Learning, you can create a batch endpoint.

## Before You Start

Before you start this lab, ensure that you have completed [Lab 1A](Lab01A.md) and [Lab 1B](Lab01B.md), which include tasks to create the Azure Machine Learning workspace and other resources used in this lab.

## Task 1: Create a Batch Inferencing Service

In this task, you'll deploy a model to a batch endpoint for batch inferencing.

1. In [Azure Machine Learning studio](https://ml.azure.com), view the **Compute** page for your workspace; and on the **Compute Instances** tab, ensure your compute instance is running. If not, start it.
2. When the compute instance is running, click the **JupyterLab** link to open JupyterLab in a new browser tab.
3. In the JupyterLab file browser, open the **MachineLearningCourse/materialy/azure-ml/en** folder you cloned in [Lab 1B](Lab01B.md), then open the **07B - Creating a Batch Inferencing Service.ipynb** notebook. Confirm that it uses the **Python 3.10 - SDK v2** kernel, then read the notes in the notebook, running each code cell in turn.

> **Note**: If you intend to continue straight to the [next exercise](Lab08A.md), leave your compute instance running. If you're taking a break, you might want to close all JupyterLab tabs and **Stop** your compute instance to avoid incurring unnecessary costs.
