# Lab 6A: Creating a Pipeline

You can use the Azure Machine Learning SDK v2 to perform all of the tasks required to create and operate a machine learning solution in Azure. Rather than perform these tasks individually, you can use *pipelines* to orchestrate the components required to prepare data, run training scripts, register models, and other tasks.

## Before You Start

Before you start this lab, ensure that you have completed [Lab 1A](Lab01A.md) and [Lab 1B](Lab01B.md), which include tasks to create the Azure Machine Learning workspace and other resources used in this lab.

## Task 1: Create a Pipeline

In this task, you'll create a pipeline to train and register a model.

1. In [Azure Machine Learning studio](https://ml.azure.com), view the **Compute** page for your workspace; and on the **Compute instances** tab, ensure your compute instance is running. If not, start it.
2. When the compute instance is running, select the **JupyterLab** link for the compute instance to open JupyterLab in a new browser tab.
3. In the JupyterLab file browser, open the **MachineLearningCourse/materialy/azure-ml/en** folder (the repository you cloned in [Lab 1B](Lab01B.md)) and open the **06A - Creating a Pipeline.ipynb** notebook. Confirm that it's using the **Python 3.10 - SDK v2** kernel, then read the notes in the notebook, running each code cell in turn.

> **Note**: If you intend to continue straight to the [next exercise](Lab06B.md), leave your compute instance running. If you're taking a break, you might want to close all JupyterLab tabs and **Stop** your compute instance to avoid incurring unnecessary costs.
