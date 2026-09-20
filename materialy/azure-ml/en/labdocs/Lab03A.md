# Lab 3A: Running Experiments

Jobs are at the core of a data scientist's work. In Azure Machine Learning SDK v2, you submit and track *jobs* that run a script, and use MLflow tracking - which is built into Azure Machine Learning - to record metrics and outputs.

## Before You Start

Before you start this lab, ensure that you have completed [Lab 1A](Lab01A.md) and [Lab 1B](Lab01B.md), which include tasks to create the Azure Machine Learning workspace and other resources used in this lab.

## Task 1: Run Experiments in a Compute Instance

An Azure Machine Learning Compute Instance provides a useful environment for running experiment code, right in your workspace.

1. In [Azure Machine Learning studio](https://ml.azure.com), view the **Compute** page for your workspace; and on the **Compute Instances** tab, ensure your compute instance (`mymachine`) is running. If not, start it.
2. When the compute instance is running, click the **JupyterLab** link to open JupyterLab in a new browser tab.
3. In the JupyterLab file browser, open the `MachineLearningCourse/materialy/azure-ml/en` folder you cloned in [Lab 1B](Lab01B.md) (`~/cloudfiles/code/Users/<your-user-name>/MachineLearningCourse/materialy/azure-ml/en`), then open the **03A - Running Experiments.ipynb** notebook. Confirm that it uses the **Python 3.10 - SDK v2** kernel, then read the notes in the notebook, running each code cell in turn.

> **Note**: If you intend to continue straight to the [next exercise](Lab03B.md), leave your compute instance running. If you're taking a break, you might want to close all JupyterLab tabs and **Stop** your compute instance to avoid incurring unnecessary costs.
