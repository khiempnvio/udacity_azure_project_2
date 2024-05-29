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

**3. Continuous Delivery on Azure**
1. Update Makefile
    ```
    setup:
        python3 -m venv ~/.udacity-devops

    install:
        pip install --upgrade pip &&\
            pip install -r requirements.txt

    test:
        #python -m pytest -vv --cov=myrepolib tests/*.py
        #python -m pytest --nbval notebook.ipynb


    lint:
        #hadolint Dockerfile #uncomment to explore linting Dockerfiles
        pylint --disable=R,C,W1203 app.py

    all: install lint test
    ```

2. Update requirements.txt
    ```
    Flask== 2.2.2
    pandas==1.3.5
    scikit-learn==1.0.2
    joblib==1.2.0
    pylint==2.13.7
    pytest==7.1.2
    ```
3. Create a app.py file
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
4. Create a make_predict_azure_app file
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
            -X POST https://<yourappname>.azurewebsites.net:$PORT/predict 
            #your application name <yourappname>goes here
    ```
5. Create a make_prediction.sh file
    ```
        #!/usr/bin/env bash
        PORT=8000
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
6. Create a locustfile.py file
    ```
    ```
7. Create a commands.sh file
    ```
        az webapp up -n <your-appservice>
    ```
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