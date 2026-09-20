# Lab 5B: Working with Compute Targets

While you can run experiments on your local compute, in many cases you'll want to leverage cloud compute for increased scalability.

## Before You Start

Before you start this lab, ensure that you have completed [Lab 1A](Lab01A.md) and [Lab 1B](Lab01B.md), which include tasks to create the Azure Machine Learning workspace and other resources used in this lab.

## Task 1: Work with Compute Targets

In this task, you'll run a training job on a cloud-based compute target, using the Azure Machine Learning SDK v2.

1. In [Azure Machine Learning studio](https://ml.azure.com), view the **Compute** page for your workspace; and on the **Compute instances** tab, ensure your compute instance is running. If not, start it.
2. When the compute instance is running, select its **JupyterLab** link to open the JupyterLab interface in a new browser tab.
3. In JupyterLab, open the **MachineLearningCourse/materialy/azure-ml/en** folder (clone or pull the repository first if you haven't already - see [Lab 1B](Lab01B.md)) and open the **05B - Working with Compute Targets.ipynb** notebook. Confirm that the notebook is using the **Python 3.10 - SDK v2** kernel, then read the notes and run each code cell in turn.

> **Note**: If you intend to continue straight to the [next exercise](Lab06A.md), leave your compute instance running. If you're taking a break, you might want to close all Jupyter tabs and **Stop** your compute instance to avoid incurring unnecessary costs.
