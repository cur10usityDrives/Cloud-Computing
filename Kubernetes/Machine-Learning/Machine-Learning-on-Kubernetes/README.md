# Machine Learning on Kubernetes with Flask API

## Introduction
This project demonstrates the deployment of a machine learning model using Flask API on a Kubernetes cluster. 
The model predicts customer behavior based on input features using logistic regression.

## Technologies Used
- **Google Cloud Platform (GCP)**: Provides the infrastructure for running Kubernetes.
- **Kubernetes**: Manages containerized applications in a clustered environment.
- **Docker**: Containerizes the Flask application and its dependencies.
- **Flask**: A lightweight web framework for building the API.
- **Python**: The programming language used for the model and API implementation.
- **Minikube**: Local Kubernetes cluster setup tool used for development and testing.

## Project Structure
```
.
├── flask_api.py          # Flask application script
├── Dockerfile            # Dockerfile for building the container image
├── logreg.pkl            # Pre-trained logistic regression model
├── requirements.txt      # Python dependencies
└── README.md             # Project documentation
```

## Setup and Installation

### Prerequisites
- Google Cloud Platform (GCP) account
- Google Cloud SDK
- Docker
- Minikube
- kubectl

### Step-by-Step Guide

1. **Start Minikube in Google Cloud Platform:**
   ```sh
   minikube start
   ```

2. **Create a `requirements.txt` File:**
   ```txt
   Flask==1.1.2
   flasgger==0.9.4
   numpy==1.19.5
   pandas==1.1.4
   scikit-learn==0.24.2
   ```

3. **Upload `logreg.pkl` to Your Workspace:**
   - Use the Cloud Shell file upload feature to upload your `logreg.pkl` file to your working directory.

4. **Create the `flask_api.py` File:**
   ```python
   from flask import Flask, request
   import numpy as np
   import pickle
   import pandas as pd
   from flasgger import Swagger

   app = Flask(__name__)
   Swagger(app)

   # Load the logistic regression model
   with open("logreg.pkl", "rb") as pickle_in:
       model = pickle.load(pickle_in)

   @app.route('/')
   def home():
       return "Welcome to the Flask API!"

   @app.route('/predict', methods=["GET"])
   def predict_class():
       """
       Predict if Customer would buy the product or not.
       ---
       parameters:
         - name: age
           in: query
           type: number
           required: true
         - name: new_user
           in: query
           type: number
           required: true
         - name: total_pages_visited
           in: query
           type: number
           required: true
       responses:
         200:
           description: Prediction
       """
       age = int(request.args.get("age"))
       new_user = int(request.args.get("new_user"))
       total_pages_visited = int(request.args.get("total_pages_visited"))
       prediction = model.predict([[age, new_user, total_pages_visited]])
       return "Model prediction is " + str(prediction[0])

   @app.route('/predict_file', methods=["POST"])
   def prediction_test_file():
       """
       Prediction on multiple input test file.
       ---
       parameters:
         - name: file
           in: formData
           type: file
           required: true
       responses:
         200:
           description: Test file Prediction
       """
       df_test = pd.read_csv(request.files.get("file"))
       prediction = model.predict(df_test)
       return str(list(prediction))

   if __name__ == '__main__':
       app.run(debug=True, host='0.0.0.0', port=5000)
   ```

5. **Create a Dockerfile:**
   ```Dockerfile
   # Use the official Python image from the Docker Hub
   FROM python:3.8-slim

   # Set the working directory in the container
   WORKDIR /app

   # Copy the current directory contents into the container at /app
   COPY . /app

   # Install any needed packages specified in requirements.txt
   RUN pip install --no-cache-dir -r requirements.txt

   # Make port 5000 available to the world outside this container
   EXPOSE 5000

   # Define environment variable to prevent Python from writing .pyc files to disk
   ENV PYTHONUNBUFFERED=1

   # Run flask_api.py when the container launches
   CMD ["python", "flask_api.py"]
   ```

6. **Build the Docker Image:**
   ```sh
   sudo docker build -t ml_app_docker .
   ```

7. **Run the Docker Container:**
   ```sh
   docker container run -p 5000:5000 ml_app_docker
   ```

8. **Expose Port 5000 in Cloud Shell:**
   - On the top right side of the screen, click the eye-shaped button.
   - Change the port number if it's not already set to 5000. You should see a welcome message.

9. **Access Swagger-UI:**
   - Add `/apidocs/` at the end of the URL to access the Swagger-UI home page.

## Usage
- **Get Prediction:**
  - Use the `GET` endpoint to get a prediction for a single customer based on query parameters.

- **File Prediction:**
  - Use the `POST` endpoint to get predictions for multiple customers by uploading a CSV file.

## Contributing
Contributions are welcome! Please fork the repository and create a pull request with your changes.

## Author
Natnael Haile
