# Azure Infrastructure Operations Project: Building a CI/CD Pipeline

### Introduction
For this project, you will build a Github repository from scratch and create a scaffolding that will assist you in performing both Continuous Integration and Continuous Delivery.

### Getting Started
1. Clone this repository
2. Update this README to reflect how someone would use your code.

### Dependencies
1. Create an [Github Account](https://github.com)
1. Create an [Azure Account](https://portal.azure.com)
3. Install GNU for run Make Command on Window OS [GNUWIN32](https://sourceforge.net/projects/gnuwin32/)
4. Install [Python](https://www.python.org/) 

### Trello board
https://trello.com/b/4Gusjl7K/project-management-template-udacity-azure

### Spreadsheet
https://docs.google.com/spreadsheets/d/1p8G7eR6ItVUcQuKBGDGGwJW6gfh8JcfsDvpT5bLv3L4/edit?usp=sharing

### Instructions

**1. Create the Cloud-Based Development Environment**
- Create a repo from github
- Launch an Azure Cloud Shell environment and create ssh-keys. Upload these keys to your GitHub account.
- Take a screenshot showing the project cloned into Azure Cloud Shell
- Create scaffolding for project
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
**2. Configure GitHub Actions**
- This diagram for GitHub Actions
    ![Define prefix variable](/images/ci-diagram.png.png)
- Create a file  [pythonapp.yml](https://github.com/khiempnvio/udacity_azure_project_2/actions/new) for workfollow
    
    ```
        name: Python application test with Github Actions

        on: [push]

        jobs:
        build:

            runs-on: ubuntu-latest

            steps:
            - uses: actions/checkout@v2
            - name: Set up Python 3.5
            uses: actions/setup-python@v1
            with:
                python-version: 3.5
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
- Go to Git Actions to see result.
- Run make lint, make test to verify everything is passed.
    > make lint

    > make test
### Output

**1. Create the Cloud-Based Development Environment**
- Create a repo from github
    ![Define prefix variable](https://github.com/khiempnvio/udacity_azure_project_2/blob/main/Images/repo.png?raw=true)
- Launch an Azure Cloud Shell environment and create ssh-keys. Upload these keys to your GitHub account.
    ![Define prefix variable](/images/ssh-keygen.png)
- Take a screenshot showing the project cloned into Azure Cloud Shell.
    ![Define prefix variable](/images/git-clone-from-azure-cloud-shell.png)

**2. Configure GitHub Actions**
- Go to Git Actions to see result.
    ![Define prefix variable](/images/git_action_success.png)