# Lab 5A: Working with Environments

All Python code runs in the context of an environment, which determines the Python packages available. When you run a script as an experiment in Azure Machine Learning, you can configure the environment in which it runs.

## Before You Start

Before you start this lab, ensure that you have completed [Lab 1A](Lab01A.md) and [Lab 1B](Lab01B.md), which include tasks to create the Azure Machine Learning workspace and other resources used in this lab.

## Task 1: Work with Environments

In this task, you'll use code in a notebook to work with environments for Azure Machine Learning jobs, using the Azure Machine Learning SDK v2.

1. In [Azure Machine Learning studio](https://ml.azure.com), view the **Compute** page for your workspace; and on the **Compute instances** tab, ensure your compute instance is running. If not, start it.
2. When the compute instance is running, select its **JupyterLab** link to open the JupyterLab interface in a new browser tab.
3. In JupyterLab, open the **MachineLearningCourse/materialy/azure-ml/en** folder (clone or pull the repository first if you haven't already - see [Lab 1B](Lab01B.md)) and open the **05A - Working with Environments.ipynb** notebook. Confirm that the notebook is using the **Python 3.10 - SDK v2** kernel, then read the notes and run each code cell in turn.

> **Note**: If you intend to continue straight to the [next exercise](Lab05B.md), leave your compute instance running. If you're taking a break, you might want to close all Jupyter tabs and **Stop** your compute instance to avoid incurring unnecessary costs.
