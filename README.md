# Azure Infrastructure Operations Project: Building a CI/CD Pipeline

## Introduction
For this project, you will build a Github repository from scratch and create a scaffolding that will assist you in performing both Continuous Integration and Continuous Delivery.

## Getting Started
1. Clone this repository
2. Update this README to reflect how someone would use your code.

## Dependencies
1. Create an [Github Account](https://github.com)
1. Create an [Azure Account](https://portal.azure.com)

## Trello board
https://trello.com/b/4Gusjl7K/project-management-template-udacity-azure

## Spreadsheet
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
- Architecture Diagram
    ![Define prefix variable](/Images/ci-diagram_configure_gitHub_actions.png)
#### 1. Enable Github Actions
Go to https://github.com/khiempnvio/udacity_azure_project_2/settings/actions

![Define prefix variable](/Images/git_hub_enable_git_actions.png)
#### 2. Run Githu Actions
1. Go to https://github.com/khiempnvio/udacity_azure_project_2/actions/new and select [set up a workflow yourself](https://github.com/khiempnvio/udacity_azure_project_2/new/main?filename=.github%2Fworkflows%2Fmain.yml&workflow_template=blank)

2. Update file name main.yml to pythonapp.yml and change the code
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
3. Git Actions results after push code
    