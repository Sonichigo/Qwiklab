# TASK 1:Launching an AI Platform Notebook
- In the Cloud Console, in the Search bar, type in Notebook.
- Select Notebook for AI Platform.
- On the Notebook instances page, click **New Instance.**
- In the Customize instance menu, select the **latest version of TensorFlow without GPUs.**
- In the New notebook instance dialog, accept the default options and click **Create.**
- After a few minutes, the AI Platform console will display your instance name, followed by Open Jupyterlab .Click **Open JupyterLab**. Your notebook is now set up.

# TASK 2:Downloading and exploring a sample dataset
- In your notebook, click the terminal.
- Clone the repo:
            ``` 
            git clone https://github.com/GoogleCloudPlatform/training-data-analyst
            ```
- Go to the enclosing folder: `` training-data-analyst/quests/dei ``
- Open the notebook file `` what-if-tool-challenge.ipynb.``
- Download and import the dataset ``hmda_2017_ny_all-records_labels`` by running the first to the eighth cells (the **Get the Train & Test Data** section).

# TASK 3:Building and training two different TensorFlow models
- In the second cell of the Train your first model on the complete dataset section, add the following lines to create the model.
  ```
  model = Sequential()
  model.add(layers.Dense(8, input_dim=input_size))
  model.add(layers.Dense(1, activation='sigmoid'))
  model.compile(optimizer='sgd', loss='mse')
  model.fit(train_data, train_labels, batch_size=32, epochs=10)
  
  ```
- Copy the code for training the second model. Modify model to limited_model as well as ** train_data, train_labels to limited_train_data, limited_train_labels**. The code for the second model should look like the following.
  ```
  limited_model = Sequential()
  limited_model.add(layers.Dense(8, input_dim=input_size))
  limited_model.add(layers.Dense(1, activation='sigmoid'))
  limited_model.compile(optimizer='sgd', loss='mse')
  limited_model.fit(limited_train_data, limited_train_labels, batch_size=32, epochs=10)
  ```
- Run the cells in this section and wait for the finish of model training.

## TASK 3:Deploying models to the Cloud AI Platform
- Moving on to the Deploy your models to the AI Platform section in the notebook.
- Replace the values of GCP_PROJECT and MODEL_BUCKET with your project ID and an unique bucket name.Change the __REGION__ to __us-west1.__
- Add the following codes to the notebook cells for your COMPLETE model.
  ```
  !gcloud ai-platform models create $MODEL_NAME --regions $REGION
  ```
  
  ```
  !gcloud ai-platform versions create $VERSION_NAME \
  --model=$MODEL_NAME \
  --framework='TensorFlow' \
  --runtime-version=2.1 \
  --origin=$MODEL_BUCKET/saved_model/my_model \
  --staging-bucket=$MODEL_BUCKET \
  --python-version=3.7 \
  --project=$GCP_PROJECT
  ```
- Add the following codes to the notebook cells for your LIMITED model.
  ```
  !gcloud ai-platform models create $LIM_MODEL_NAME --regions $REGION
  ```
  
  ```
  !gcloud ai-platform versions create $VERSION_NAME \
  --model=$LIM_MODEL_NAME \
  --framework='TensorFlow' \
  --runtime-version=2.1 \
  --origin=$MODEL_BUCKET/saved_limited_model/my_limited_model \
  --staging-bucket=$MODEL_BUCKET \
  --python-version=3.7 \
  --project=$GCP_PROJECT
  ```
## TASK 4:Using the What-If Tool to compare the models
- Run the last cell in the notebook to activate **What-If Tool.**
