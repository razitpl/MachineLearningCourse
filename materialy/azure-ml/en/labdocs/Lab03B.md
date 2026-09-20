# Lab 3B: Training and Registering Models

Machine Learning is primarily about training models that you can use to provide predictive services to applications; so now it's time to see how you can use Azure Machine Learning jobs to run training scripts, and how to register the resulting trained models.

## Before You Start

Before you start this lab, ensure that you have completed [Lab 1A](Lab01A.md) and [Lab 1B](Lab01B.md), which include tasks to create the Azure Machine Learning workspace and other resources used in this lab.

## Task 1: Use the Azure Machine Learning SDK to Train and Register Models

In this task, you'll use code in a notebook to run training scripts as Azure Machine Learning jobs, using `mlflow.sklearn.autolog()` to automatically log parameters, metrics, and the trained model, and then register the resulting model as `diabetes_model`.

1. In [Azure Machine Learning studio](https://ml.azure.com), view the **Compute** page for your workspace; and on the **Compute Instances** tab, ensure your compute instance (`mymachine`) is running. If not, start it.
2. When the compute instance is running, click the **JupyterLab** link to open JupyterLab in a new browser tab.
3. In the JupyterLab file browser, open the `MachineLearningCourse/materialy/azure-ml/en` folder you cloned in [Lab 1B](Lab01B.md) (`~/cloudfiles/code/Users/<your-user-name>/MachineLearningCourse/materialy/azure-ml/en`), then open the **03B - Training Models.ipynb** notebook. Confirm that it uses the **Python 3.10 - SDK v2** kernel, then read the notes in the notebook, running each code cell in turn.

> **Note**: If you intend to continue straight to the [next exercise](Lab04A.md), leave your compute instance running. If you're taking a break, you might want to close all JupyterLab tabs and **Stop** your compute instance to avoid incurring unnecessary costs.
