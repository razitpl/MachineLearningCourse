# Lab 6B: Publishing a Pipeline

After you've created a pipeline, you can deploy it behind a batch endpoint through which the pipeline can be initiated. This enables you to run the pipeline on-demand or at scheduled times.

## Before You Start

Before you start this lab, ensure that you have completed [Lab 1A](Lab01A.md) and [Lab 1B](Lab01B.md), which include tasks to create the Azure Machine Learning workspace and other resources used in this lab; and [Lab 6A](Lab06A.md) in which you create the pipeline you will deploy in this lab.

## Task 1: Deploy a Pipeline as a Batch Endpoint

In this task, you'll deploy the pipeline you created in the previous lab behind a batch endpoint.

1. In [Azure Machine Learning studio](https://ml.azure.com), view the **Compute** page for your workspace; and on the **Compute instances** tab, ensure your compute instance is running. If not, start it.
2. When the compute instance is running, select the **JupyterLab** link for the compute instance to open JupyterLab in a new browser tab.
3. In the JupyterLab file browser, open the **MachineLearningCourse/materialy/azure-ml/en** folder (the repository you cloned in [Lab 1B](Lab01B.md)) and open the **06B - Publishing a Pipeline.ipynb** notebook. Confirm that it's using the **Python 3.10 - SDK v2** kernel, then read the notes in the notebook, running each code cell in turn.

> **Note**: If you intend to continue straight to the [next exercise](Lab07A.md), leave your compute instance running. If you're taking a break, you might want to close all JupyterLab tabs and **Stop** your compute instance to avoid incurring unnecessary costs.
