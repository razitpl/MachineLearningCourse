# Lab 1B: Working with Azure Machine Learning Tools

In this lab, you will explore the tools available for working with an Azure Machine Learning workspace.

You will use Azure Machine Learning studio, JupyterLab on a compute instance, GitHub, and the Azure Machine Learning SDK v2 for Python.

## Before You Start

Before starting this lab, you must have created an Azure Machine Learning workspace by following the instructions in the [previous lab](Lab01A.md).

You also need:

- An Azure subscription with permission to access the Azure Machine Learning workspace.
- A running Azure Machine Learning compute instance.
- Access to the course repository.

## Task 1: Use Azure ML SDK v2 in a Compute Instance

You can manage many Azure Machine Learning resources through the Studio interface. However, the Azure Machine Learning SDK v2 enables you to automate repeatable tasks and manage Azure ML resources from Python code.

1. Open [Azure Machine Learning studio](https://ml.azure.com).

2. Select your Azure Machine Learning workspace.

3. In the left menu, select **Compute**.

4. Open the **Compute instances** tab.

5. Start the compute instance created in the previous lab if it is not already running.

6. Wait until the compute instance status changes to **Running**.

7. Select the **JupyterLab** link for the compute instance.

8. In JupyterLab, create a new notebook.

9. In the upper-right corner of the notebook, select a current Azure Machine Learning SDK v2 kernel. The kernel name may be similar to:

    ```text
    Python 3.10 - SDK v2
    ```

10. Run the following code cell to verify that the Azure ML SDK v2 is available:

    ```python
    import sys
    from azure.ai.ml import MLClient
    from azure.identity import DefaultAzureCredential

    print(f"Python executable: {sys.executable}")
    print("Azure Machine Learning SDK v2 is ready.")
    ```

11. If the imports fail with a `ModuleNotFoundError`, open a new terminal in JupyterLab and install the required packages:

    ```bash
    python -m pip install --upgrade azure-ai-ml azure-identity
    ```

12. Restart the notebook kernel after installing packages, and run the verification cell again.

> **More Information:** For current Azure Machine Learning SDK v2 documentation, see the [Azure ML SDK for Python documentation](https://learn.microsoft.com/python/api/overview/azure/ai-ml-readme?view=azure-python).

## Task 2: Clone the Course Repository

You will use notebooks and supporting files from the course repository.

1. In JupyterLab, select **File** > **New** > **Terminal**.

2. Change to your Azure Machine Learning user directory, replacing `<your-user-name>` with the folder name shown under **Users** in the JupyterLab file browser (this is based on your Azure AD identity, not your Linux username):

    ```bash
    cd ~/cloudfiles/code/Users/<your-user-name>
    ```

3. Clone the course repository:

    ```bash
    git clone https://github.com/razitpl/MachineLearningCourse.git
    ```

4. If you have already cloned the repository, update it instead:

    ```bash
    cd ~/cloudfiles/code/Users/<your-user-name>/MachineLearningCourse/materialy/azure-ml/en
    git pull
    ```

5. Close the terminal tab.

6. In the JupyterLab file browser, refresh the page if necessary.

7. Open the following folder:

    ```text
    MachineLearningCourse/materialy/azure-ml/en
    ```

8. Locate and open the **01B - Intro to the Azure ML SDK.ipynb** notebook for this lab.

9. Confirm that the notebook uses the **Python 3.10 - SDK v2** kernel, or another current Azure ML SDK v2 kernel.

10. Run the notebook cells one at a time and read the explanations.

## Task 3: Connect to an Azure ML Workspace

The Azure Machine Learning SDK v2 uses an `MLClient` object to connect to and manage Azure Machine Learning workspaces.

You can connect by supplying your subscription ID, resource group name, and workspace name in code. Alternatively, you can download a `config.json` file from the Azure portal and use it to create the client.

### Download the workspace configuration

1. Open the [Azure portal](https://portal.azure.com) in a new browser tab.

2. Find and open the Azure Machine Learning workspace created in the previous lab.

3. On the workspace **Overview** page, select **Download config.json**.

4. Save the downloaded file.

5. In JupyterLab, upload `config.json` to the root folder of the `MachineLearningCourse/materialy/azure-ml/en` repository.

6. Ensure that `config.json` is not committed to a public GitHub repository.

7. If the repository does not already contain a `.gitignore` file, create one.

8. Add the following entry to `.gitignore`:

    ```gitignore
    config.json
    ```

### Connect with the Azure ML SDK v2

1. Open a notebook that uses the **Python 3.10 - SDK v2** kernel.

2. Run the following cell:

    ```python
    from azure.ai.ml import MLClient
    from azure.identity import DefaultAzureCredential

    credential = DefaultAzureCredential()

    ml_client = MLClient.from_config(
        credential=credential
    )

    print(f"Connected to workspace: {ml_client.workspace_name}")
    ```

3. If authentication fails, open a JupyterLab terminal and sign in to Azure:

    ```bash
    az login
    ```

4. Complete the browser-based sign-in process.

5. Return to the notebook and run the connection cell again.

6. When the connection succeeds, verify that the correct Azure Machine Learning workspace name is displayed.

> **Note:** `DefaultAzureCredential` can use multiple authentication methods. During interactive development, an Azure CLI sign-in is often the simplest method.

## Task 4: Explore Azure ML Resources with SDK v2

After connecting to the workspace, you can use `MLClient` to list and manage Azure Machine Learning resources.

1. Run the following code to list the available compute resources:

    ```python
    for compute in ml_client.compute.list():
        print(f"{compute.name}: {compute.type}")
    ```

2. Run the following code to list registered data assets:

    ```python
    for data_asset in ml_client.data.list():
        print(
            f"Name: {data_asset.name}, "
            f"Version: {data_asset.version}, "
            f"Type: {data_asset.type}"
        )
    ```

3. Run the following code to list registered models:

    ```python
    for model in ml_client.models.list():
        print(
            f"Name: {model.name}, "
            f"Version: {model.version}, "
            f"Type: {model.type}"
        )
    ```

4. Run the following code to list recent Azure Machine Learning jobs:

    ```python
    for job in ml_client.jobs.list():
        print(
            f"Name: {job.name}, "
            f"Status: {job.status}, "
            f"Display name: {job.display_name}"
        )
    ```

5. Compare the resources returned by the SDK with the resources displayed in Azure Machine Learning studio.

> **Note:** The SDK v2 lets you manage data assets, environments, compute resources, jobs, models, and endpoints programmatically. You will use these capabilities in later labs.

## Task 5: Optional Development with GitHub Codespaces

You can also work with the repository outside Azure Machine Learning Studio by using GitHub Codespaces.

GitHub Codespaces provides a browser-based Visual Studio Code development environment.

1. Open the [MachineLearningCourse/materialy/azure-ml/en repository](https://github.com/razitpl/MachineLearningCourse).

2. Select **Code**.

3. Select the **Codespaces** tab.

4. Select **Create codespace on main**.

5. Wait for the Codespace to start.

6. Open the integrated terminal in Visual Studio Code.

7. Create a Python virtual environment:

    ```bash
    python -m venv .venv
    ```

8. Activate the virtual environment:

    ```bash
    source .venv/bin/activate
    ```

9. Upgrade `pip` and install the Azure ML SDK v2:

    ```bash
    python -m pip install --upgrade pip
    python -m pip install azure-ai-ml azure-identity ipykernel
    ```

10. Sign in to Azure:

    ```bash
    az login
    ```

11. In Visual Studio Code, select the Python interpreter from the `.venv` virtual environment.

12. Open the lab notebook and select the `.venv` Python kernel if prompted.

> **Note:** GitHub Codespaces is optional. You can complete all labs by using JupyterLab on an Azure Machine Learning compute instance.

## Clean Up

When you have finished working, stop your compute instance if you do not plan to use it immediately.

1. Return to [Azure Machine Learning studio](https://ml.azure.com).

2. Select **Compute**.

3. Open the **Compute instances** tab.

4. Select your compute instance.

5. Select **Stop**.

> **Note:** Stopping a compute instance prevents further compute charges. You can start it again when you continue working on the labs.
