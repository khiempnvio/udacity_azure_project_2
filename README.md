# Azure Infrastructure Operations Project: Building a CI/CD Pipeline

## Introduction
For this project, you will build a Github repository from scratch and create a scaffolding that will assist you in performing both Continuous Integration and Continuous Delivery.

## Getting Started
1. Clone this repository
2. Update this README to reflect how someone would use your code.

## Dependencies
1. Create an [Github Account](https://github.com)
2. Create an [Azure Account](https://portal.azure.com)

## Trello board
https://trello.com/b/4Gusjl7K/project-management-template-udacity-azure

## Spreadsheet
https://docs.google.com/spreadsheets/d/1p8G7eR6ItVUcQuKBGDGGwJW6gfh8JcfsDvpT5bLv3L4/edit?usp=sharing

## Demonstration

https://docs.google.com/spreadsheets/d/1p8G7eR6ItVUcQuKBGDGGwJW6gfh8JcfsDvpT5bLv3L4/edit?usp=sharing

## Instructions

### 1. CI: Set Up Azure Cloud Shell

#### Create the Cloud-Based Development Environment
1. Create a GitHub Repo
    ![Define prefix variable](https://github.com/khiempnvio/udacity_azure_project_2/blob/main/Images/repo.png?raw=true)

2. Log in Azure Portal and access Azure Cloud Shell

3. Launch an Azure Cloud Shell environment and create ssh-keys. Upload these keys to your GitHub account.
    - Generate SSH Key
    ```bash
    ssh-keygen -t rsa
    ```
    ![Define prefix variable](/Images/ssh-keygen.png?raw=true)

    - Copy the public key to Github Account -> Settings -> SSH and GPG keys (https://github.com/settings/keys)

    ![Define prefix variable](/Images/git_hub_ssh_key.png)

#### Create Project Scaffolding
1. Create a Makefile
    ```
        install:
            pip install --upgrade pip &&\
                pip install -r requirements.txt
        test:
            python -m pytest -vv test_hello.py

        lint:
            pylint --disable=R,C hello.py

        all: install lint test
    ```
2. Create a requirements.txt
    ```
        pylint
        pytest
    ```
3. Create a hello.py
    ```
        def toyou(x):
            return "hi %s" % x


        def add(x):
            return x + 1


        def subtract(x):
            return x - 1
    ```
4. Create a test_hello.py
    ```
        from hello import toyou, add, subtract

        def setup_function(function):
            print("Running Setup: %s" % function.__name__)
            function.x = 10


        def teardown_function(function):
            print("Running Teardown: %s" % function.__name__)
            del function.x


        ### Run to see failed test
        #def test_hello_add():
        #    assert add(test_hello_add.x) == 12

        def test_hello_subtract():
            assert subtract(test_hello_subtract.x) == 9
    ```
#### Local Test
1. Git clone using SSH 
    ```
        git@github.com:khiempnvio/udacity_azure_project_2.git
    ```
    ![Define prefix variable](/Images/git_hub_clone_from_azure_cli.png)
2. Create the Python Virtual Environment
    ```
        python3 -m venv ~/.appvenv
        source ~/.appvenv/bin/activate
    ```
    ![Define prefix variable](/Images/git_hub_python_environtment.png)
3. Install all dependencies, verify code by Lint and run Tests by using make
    ```
        make all
    ```
    ![Define prefix variable](/Images/git_hub_make_all_step_1.png)
    ![Define prefix variable](/Images/git_hub_make_all_step_2.png)

### 2. CI: Configure GitHub Actions
GitHub Actions is a powerful feature provided by GitHub, enabling the creation of comprehensive CI/CD workflows. Within the context of this project, we harness GitHub Actions to execute our Continuous Integration (CI) processes. These processes encompass building, linting, and testing our code, all of which are performed seamlessly through GitHub Actions.

- Architecture Diagram
    ![Define prefix variable](/Images/ci-diagram_configure_gitHub_actions.png)

To set up GitHub Actions for your repository, follow these steps:
#### 1. Enable Github Actions
Go to https://github.com/khiempnvio/udacity_azure_project_2/settings/actions

![Define prefix variable](/Images/git_hub_enable_git_actions.png)
#### 2. Run Github Actions
1. Go to https://github.com/khiempnvio/udacity_azure_project_2/actions/new and select [set up a workflow yourself](https://github.com/khiempnvio/udacity_azure_project_2/new/main?filename=.github%2Fworkflows%2Fmain.yml&workflow_template=blank)

2. Update file name main.yml to pythonapp.yml and change the code

    Proceed with the workflow setup process. GitHub Actions will configure a workflow that suits the requirements of your code.

    Customization of the actions to align with our specific needs is essential. The default action steps are designed to run Python commands. However, given that we employ the Make utility for building, testing, and linting our code, we will need to adjust the GitHub Actions configuration accordingly.

    ```
        name: Python application test with Github Actions

        on: [push]

        jobs:
        build:

            runs-on: ubuntu-latest

            steps:
            - uses: actions/checkout@v2
            - name: Set up Python 3.10.14
            uses: actions/setup-python@v1
            with:
                python-version: 3.10.14
            - name: Install dependencies
            run: |
                make install
            - name: Lint with pylint
            run: |
                make lint
            - name: Test with pytest
            run: |
                make test
    ```
    ![Define prefix variable](/Images/git_hub_pythonapp_yml.png)

3. Git Actions results after push code
    
    Manually trigger the GitHub Action. Upon a successful launch, the results will be displayed below.

    Go to https://github.com/khiempnvio/udacity_azure_project_2/actions
    ![Define prefix variable](/Images/git_hub_actions_success.png)

    Once the workflow is configured, our application will undergo continuous building with each push or pull request made to our main branch.
### 3.Continuous Delivery on Azure
Azure DevOps offers a comprehensive set of developer services, enabling teams to efficiently plan work, collaborate on code development, and build and deploy applications. This platform fosters a collaborative environment and establishes a cohesive set of processes that unite developers, project managers, and contributors in software development endeavors. With Azure DevOps, organizations can accelerate product development compared to traditional software development approaches.

- Architecture Diagram
    ![Define prefix variable](/Images/cd_diagram.png)

To Set Up Azure Pipelines for Continuous Delivery, follow these steps:
1. Setting up Azure DevOps

- If you don't already have an Azure DevOps account, go to 
[dev.azure.com](https://dev.azure.com)  to sign up for a free account.

- Create an Azure DevOps project:
    - Create a new project and establish a connection to Azure. The screenshots below illustrate the process:
    ![Define prefix variable](/Images/dev_az_create_project.png)
    ![Define prefix variable](/Images/dev_az_create_project_step_2.png)
    
    - After creating the project, navigate to Project settings from the 
    left navigation. On the Project Settings page, select Pipelines > Service connections, and then click on New service connection:
    ![Define prefix variable](/Images/dev_az_create_service_connection.png)
    - In the New Service Connections dialog, select Azure Resource Manager from the dropdown and click Next:
    ![Define prefix variable](/Images/dev_az_create_service_connection_step_2.png)
    - Select Service principal (manual) for Udacity Account, if you use personal account can select Service principal (automatic)
    ![Define prefix variable](/Images/dev_az_create_service_connection_step_3.png)
    - Choose the Resource Group of the Azure Web App deployed.
    - Input a valid Service Connection Name.
    - Check the box for Grant Access Permissions to all pipelines.
    - Click Save.
    ![Define prefix variable](/Images/dev_az_create_service_connection_step_4.png)
    ![Define prefix variable](/Images/dev_az_create_service_connection_step_5.png)

2. Setting up Azure Pipeline
- First time you need to create a Personal Access Tokens by go to 
    - Enter token Name
    - Select Full access
    - Click Create 
    - After create you have a token to connect VM and agent
- Create an Agent Pool to run jobs for Pipelines, and here we are using Linux to configure the Agent Pool. In Project Setting > Agent Pools > Add pool
    - Go to https://dev.azure.com/odluser260175/_settings/agentpools
    ![Define prefix variable](/Images/dev_az_create_agent_pool.png)
    - Enter Pool type, Name and Description (optional)
    - Click Create
    ![Define prefix variable](/Images/dev_az_create_agent_pool_2.png)
    ![Define prefix variable](/Images/dev_az_create_agent_pool_3.png)
    - After create an agent pool, you have to creat a vitrual machine and add add it to Agent Pool you already create above
        - Create new VM by go to [portal.azure.com](https://portal.azure.com) 
        ![Define prefix variable](/Images/dev_az_create_vm_step_1.png)
        ![Define prefix variable](/Images/dev_az_create_vm_step_2.png)
        - Connect VM with Agent Pool by Azure CLI
            - Login to VM
            ```
                ssh devopsagent@20.42.94.212
            ```
            ![Define prefix variable](/Images/dev_az_new_agent_step_3.png)
            - After login you have to install docker
            ```
                sudo snap install docker
            ```
            ![Define prefix variable](/Images/dev_az_new_agent_step_4.png)
            - You also configure the devopsagent user to run Docker
            ```
                sudo groupadd docker
                sudo usermod -aG docker $USER
                exit
            ```
            ![Define prefix variable](/Images/dev_az_new_agent_step_5.png)
            - You also RESTART VM to apply changes

            - Go to Agent Pool Detail and create New Agent
            ![Define prefix variable](/Images/dev_az_new_agent_step_1.png)
            ![Define prefix variable](/Images/dev_az_new_agent_step_2.png)
            - Click copy button on Download the Agent, and run commands on Azure CLI

            ```
                curl -O https://vstsagentpackage.azureedge.net/agent/3.240.1/vsts-agent-linux-x64-3.240.1.tar.gz
                mkdir myagent && cd myagent
                tar zxvf ../vsts-agent-linux-x64-3.240.1.tar.gz
                ./config.sh
            ``` 
            ![Define prefix variable](/Images/dev_az_new_agent_step_6.png)
            ![Define prefix variable](/Images/dev_az_new_agent_step_7.png)
            ![Define prefix variable](/Images/dev_az_new_agent_step_8.png)
            
            - Run two commands to complete config
            ```
                sudo ./svc.sh install
                sudo ./svc.sh start
            ```
            ![Define prefix variable](/Images/dev_az_new_agent_step_9.png)
            ![Define prefix variable](/Images/dev_az_new_agent_step_10.png)
- Install Integrate Azure Pipelines on Github account
    - Go to [https://github.com/marketplace/azure-pipelines](https://github.com/marketplace/azure-pipelines) to install
    ![Define prefix variable](/Images/dev_az_azure_pipeline_github.png)
- Update files and Clone the Repo, Create a Web Service
    - Update files
        1. Update Makefile
            ```
                setup:
                    python3 -m venv ~/.flask-ml-azure
                    #source ~/.flask-ml-azure/bin/activate
                    
                install:
                    pip install --upgrade pip &&\
                        pip install -r requirements.txt

                test:
                    #python -m pytest -vv --cov=myrepolib tests/*.py
                    #python -m pytest --nbval notebook.ipynb


                lint:
                    #hadolint Dockerfile #uncomment to explore linting Dockerfiles
                    pylint --disable=R,C,W1203,bare-except --fail-under=6 app.py

                all: install lint test
            ```

        2. Update requirements.txt
            ```
                Werkzeug==2.2.2
                Flask==2.2.2
                pandas==1.3.5
                scikit-learn==1.1.3
                joblib==1.2.0
                pylint==2.13.7
                pytest==7.1.2
                locust
            ```
        3. Create a make_predict.sh file
            ```
            #!/usr/bin/env bash

                PORT=5000
                echo "Port: $PORT"

                # POST method predict
                curl -d '{  
                "CHAS":{  
                    "0":0
                },
                "RM":{  
                    "0":6.575
                },
                "TAX":{  
                    "0":296.0
                },
                "PTRATIO":{  
                    "0":15.3
                },
                "B":{  
                    "0":396.9
                },
                "LSTAT":{  
                    "0":4.98
                }
                }'\
                    -H "Content-Type: application/json" \
                    -X POST http://localhost:$PORT/predict
            ```
        4. Create a make_predict_azure_app.sh file
            ```
                #!/usr/bin/env bash

                PORT=443
                echo "Port: $PORT"

                # POST method predict
                curl -d '{
                "CHAS":{
                    "0":0
                },
                "RM":{
                    "0":6.575
                },
                "TAX":{
                    "0":296.0
                },
                "PTRATIO":{
                    "0":15.3
                },
                "B":{
                    "0":396.9
                },
                "LSTAT":{
                    "0":4.98
                }
                }'\
                    -H "Content-Type: application/json" \
                    -X POST http://khiempnudacity-flask-app-1.azurewebsites.net:$PORT/predict
            ```
        5. Create a locustfile.py file
            ```
            import time
            from locust import HttpUser, task, between

            class LoadTestPrediction(HttpUser):
                wait_time = between(1, 3)

                @task
                def predict(self):
                    self.client.post("/predict",json={
                        "CHAS":{
                            "0":0
                        },
                        "RM":{
                            "0":6.575
                        },
                        "TAX":{
                            "0":296.0
                        },
                        "PTRATIO":{
                            "0":15.3
                        },
                        "B":{
                            "0":396.9
                        },
                        "LSTAT":{
                            "0":4.98
                        }
                    })
            ```
        6. Create an app.py file
            ```
                from flask import Flask, request, jsonify
                from flask.logging import create_logger
                import logging

                import pandas as pd
                import joblib
                from sklearn.preprocessing import StandardScaler

                app = Flask(__name__)
                LOG = create_logger(app)
                LOG.setLevel(logging.INFO)

                def scale(payload):
                    """Scales Payload"""

                    LOG.info("Scaling Payload: %s payload")
                    scaler = StandardScaler().fit(payload)
                    scaled_adhoc_predict = scaler.transform(payload)
                    return scaled_adhoc_predict

                @app.route("/")
                def home():
                    html = "<h3>Sklearn Prediction Home</h3>"
                    return html.format(format)

                # TO DO:  Log out the prediction value
                @app.route("/predict", methods=['POST'])
                def predict():
                    # Performs an sklearn prediction
                    try:
                        # Load pretrained model as clf. Try any one model. 
                        # clf = joblib.load("./Housing_price_model/LinearRegression.joblib")
                        # clf = joblib.load("./Housing_price_model/StochasticGradientDescent.joblib")
                        clf = joblib.load("./Housing_price_model/GradientBoostingRegressor.joblib")
                    except:
                        LOG.info("JSON payload: %s json_payload")
                        return "Model not loaded"

                    json_payload = request.json
                    LOG.info("JSON payload: %s json_payload")
                    inference_payload = pd.DataFrame(json_payload)
                    LOG.info("inference payload DataFrame: %s inference_payload")
                    scaled_payload = scale(inference_payload)
                    prediction = list(clf.predict(scaled_payload))
                    return jsonify({'prediction': prediction})

                if __name__ == "__main__":
                    app.run(host='0.0.0.0', port=5000, debug=True)
            ```
        7. Create a command.sh file
            ```
                az webapp up -n khiempnudacity-flask-app-1 --resource-group Azuredevops --sku FREE
            ```
    Back to Azure CLI to create a service
    1. Set up virtualenv
    ```
        python3 -m venv ~/.flask-ml-azure
        source ~/.flask-ml-azure/bin/activate
    ```
    2. Clone Repo from github or pull if your repo is exists
    ```
        git clone git@github.com:khiempnvio/udacity_azure_project_2.git
    ```
    3. Run Make command to install dependences 
    ```
        make all
    ```
    4. Run web service by command
    ```
        export FLASK_APP=app.py
        flask run
    ```
    ![Define prefix variable](/Images/dev_az_run_web_local.png)
    ![Define prefix variable](/Images/dev_az_run_web_local_1.png)
    5. Run make_prediction.sh
        ```
            chmod +x make_prediction.sh
            ./make_prediction.sh
        ```
    6. Run web service by command
    ```
        chmod +x command.sh
        ./command.sh
    ```
    ![Define prefix variable](/Images/dev_az_run_command.png)
    ![Define prefix variable](/Images/dev_az_run_command_1.png)
    6. Run make_predict_azure_app.sh
    ```
        chmod +x make_predict_azure_app.sh
        ./make_predict_azure_app.sh
    ```
    7. Run locust from Azure CLI
    ```
        locust
    ```
    ![Define prefix variable](/Images/dev_az_locust.png)
    You can view track locust by command
    ```
        locust --headless --users 10 --spawn-rate 1 -H https://khiempnudacity-flask-app-1.azurewebsites.net
    ```
    ![Define prefix variable](/Images/dev_az_locust_2.png)
    8. Logs of Azure Webapp
    - Azure App Service offers the capability to access and view application logs. You can access these logs using Azure CLI commands: 
        ```
            az webapp log tail --name khiempnudacity-flask-app-1 --resource-group azuredevops
        ```
    ![Define prefix variable](/Images/dev_az_logs.png)

- Setup Pipeline
    - Select Pipeline from slide bar
    ![Define prefix variable](/Images/dev_az_create_pipeline.png)
    - Select Github(maybe it required login)
    ![Define prefix variable](/Images/dev_az_create_pipeline_2.png)
    - Select your Repo
    ![Define prefix variable](/Images/dev_az_create_pipeline_3.png)
    - Config file azure-pipelines.yml and click Run or Commit code to run
    ```
        # Starter pipeline    
        # Start with a minimal pipeline that you can customize to build and deploy your code.
        # Add steps that build, run tests, deploy, and more:
        # https://aka.ms/yaml
        trigger:
        - main

        # TODO: Replace the agent pool name
        pool: myAgentPool

        variables:
        # TODO: Replace the service connection name as used in the DevOps project settings
        azureServiceConnectionId: 'myServiceConnection'
        
        # TODO: Specify the value of the existing Web App name
        webAppName: 'khiempnudacity-flask-app-1'

        # Environment name
        environmentName: 'flask-ml-service'

        # Project root folder. Point to the folder containing manage.py file.
        projectRoot: $(System.DefaultWorkingDirectory)

        stages:
        - stage: Build
        displayName: Build stage
        jobs:
        - job: BuildJob
            pool: myAgentPool
            steps:    
            - script: |
                python -m venv appenv
                source antenv/bin/activate
                sudo apt-get update
                sudo apt-get -y install make
                sudo apt-get -y install zip
                sudo apt-get -y install python3-pip
                make install
                # export PATH=$HOME/.local/bin:$PATH
                # python3.9 -m pip install -r requirements.txt
            workingDirectory: $(projectRoot)
            displayName: 'Step install dependences'

            - script: |
                make lint
                export FLASK_APP=app.py
            workingDirectory: $(projectRoot)
            displayName: 'Step verify code and export FLASK_APP'

            - task: ArchiveFiles@2
            displayName: 'Step archive files'
            inputs:
                rootFolderOrFile: '$(projectRoot)'
                includeRootFolder: false
                archiveType: zip
                archiveFile: $(Build.ArtifactStagingDirectory)/$(Build.BuildId).zip
                replaceExistingArchive: true

            - upload: $(Build.ArtifactStagingDirectory)/$(Build.BuildId).zip
            displayName: 'Step upload package'
            artifact: drop

        - stage: Deploy
        displayName: 'Deploy Web App'
        dependsOn: Build
        condition: succeeded()
        jobs:
        - deployment: DeploymentJob
            pool: myAgentPool
            environment: $(environmentName)
            strategy:
            runOnce:
                deploy:
                steps:
                - task: AzureWebApp@1
                    displayName: 'Deploy Azure Web App : flask-ml-service'
                    inputs:
                    azureSubscription: $(azureServiceConnectionId)
                    appName: $(webAppName)
                    package: $(Pipeline.Workspace)/drop/$(Build.BuildId).zip
    ```
    ![Define prefix variable](/Images/dev_az_create_pipeline_4.png)

    You need to Permit for jobs, if your account have no permision
    ![Define prefix variable](/Images/dev_az_create_pipeline_5.png)

    After you Permit jobs will be run
    - Stage BuildJob should be success
    ![Define prefix variable](/Images/dev_az_create_pipeline_6.png)
    - Stage DeploymentJob should be success
    ![Define prefix variable](/Images/dev_az_create_pipeline_7.png)
    ![Define prefix variable](/Images/dev_az_create_pipeline_8.png)
    Anytime you change something, the pipeline will be auto run and deploy web service
## Resource Cleanup
To prevent incurring Azure resource charges related to this project, it is advisable to remove the resource group housing the App Service and the App Service Plan.