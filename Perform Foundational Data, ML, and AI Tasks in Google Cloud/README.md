# Task 1: Run a simple Dataflow job
## Created a BigQuery dataset called lab
- In the Cloud Console, click on Navigation Menu > BigQuery.
- Select your project in the left pane.
- Click CREATE DATASET.
- Enter lab in the Dataset ID, then click Create dataset.
- Run gsutil cp gs://cloud-training/gsp323/lab.schema . in the Cloud Shell to download the schema file.
- Go back to the Cloud Console, select the new dataset lab and click Create Table.
- In the Create table dialog, select Google Cloud Storage from the dropdown in the Source section.
- Copy gs://cloud-training/gsp323/lab.csv to Select file from GCS bucket.
- Enter customers to “Table name” in the Destination section.
- Enable Edit as text and copy the JSON data from the lab.schema file to the textarea in the Schema section.
- Click Create table.

## Create a Cloud Storage bucket
- In the Cloud Console, click on Navigation Menu > Storage.
- Click CREATE BUCKET.
- Copy your GCP Project ID to Name your bucket.
- Click CREATE.

## Create a Dataflow job
- In the Cloud Console, click on Navigation Menu > Dataflow.
- Click CREATE JOB FROM TEMPLATE.
- In Create job from template, give an arbitrary job name.
- From the dropdown under Dataflow template, select Text Files on Cloud Storage to BigQuery under “Process Data in Bulk (batch)”. (DO NOT select the item under “Process Data Continuously (stream)”).
  ![Step5](https://chriskyfung.github.io/images/posts/qwiklabs/qwiklab-gsp323-task1-data-flow-required-parameter.webp)
- RUN JOB

# Task 2: Run a simple Dataproc job
## Create a Dataproc cluster
- In the Cloud Console, click on Navigation Menu > Dataproc > Clusters.
- Click CREATE CLUSTER.
- Make sure the cluster is going to create in the region us-central1.
- Click Create.
- After the cluster has been created, clik the SSH button in the row of the master instance.
### In the SSH console, run the following command:
        hdfs dfs -cp gs://cloud-training/gsp323/data.txt /data.txt
- Close the SSH window and go back to the Cloud Console.
- Click SUBMIT JOB in the cluster details page.
- Select Spark from the dropdown of “Job type”.
- Copy org.apache.spark.examples.SparkPageRank to “Main class or jar”.
- Copy file:///usr/lib/spark/examples/jars/spark-examples.jar to “Jar files”.
- Enter /data.txt to “Arguments”.
- Create

# Task 3: Run a simple Dataprep job
## Import runs.csv to Dataprep
- In the Cloud Console, click on Navigation menu > Dataprep.
- After enter the home page of Cloud Dataprep, click the Import Data button.
- In the Import Data page, select GCS in the left pane.
- Click on the pencil icon under Choose a file or folder.
- Copy gs://cloud-training/gsp323/runs.csv to the textbox, and click the Go button next to it.
- After showing the preview of runs.csv in the right pane, click on the Import & Wrangle button.
- Transform data in Dataprep
- After launching the Dataperop Transformer, scroll right to the end and select column10.
- In the Details pane, click FAILURE under Unique Values to show context menu.
- Select Delete rows with selected values to Remove all rows with the state of “FAILURE”.
- Click the downward arrow next to column9, choose Filter rows > On column value > Contains.
- In the Filter rows pane, enter the regex pattern /(^0$|^0\.0$)/ to “Pattern to match”.
- Select Delete matching rows under the Action section, then click the Add button.
- ## Rename the columns to be:
      runid
      userid
      labid
      lab_title
      start
      end
      time
      score
      state
- Run job
# Task 4: AI
## Use Google Cloud Speech API to analyze the audio file
- In the Cloud Console, click on Navigation menu > APIs & Services > Credentials.
- In the Credentials page, click on + CREATE CREDENTIALS > API key.
- Copy the API key to clipboard, then click RESTRICT KEY.
- Open the Cloud Shell, store the API key as an environment variable by running the following command:
            export API_KEY=<YOUR-API-KEY>
- In the Cloud Shell, create a JSON file called gsc-request.json.
- ### Save the following codes to the file.
             {
      "config": {
        "encoding":"FLAC",
        "languageCode": "en-US"
      },"audio":  {
            "uri":"gs://cloud-training/gsp323/task4.flac"   } }

- curl -s -X POST -H "Content-Type: application/json" --data-binary @gsc-request.json \ "https://speech.googleapis.com/v1/speech:recognize?key=${API_KEY}" > task4-gcs.result 

- Upload the resulted file to Cloud Storage by running:
           gsutil cp task4-gcs.result gs://<YOUR-PROJECT_ID>-marking/task4-gcs.result
Replace <YOUR-PROJECT_ID> with your project ID.

## Use the Cloud Natural Language API to analyze the sentence
- ## In the Cloud Shell, run the following command to use the Cloud Natural Language API to analyze the given sentence.
          gcloud ml language analyze-entities --content="Old Norse texts portray Odin as one-eyed and long-bearded, frequently wielding a spear named Gungnir and wearing a cloak and a broad hat." > task4-cnl.result
         
## Upload the resulted file to Cloud Storage by running:
gsutil cp task4-cnl.result gs://<YOUR-PROJECT_ID>-marking/task4-cnl.result

Replace <YOUR-PROJECT_ID> with your project ID.         

## Use Google Video Intelligence and detect all text on the video
- In the Cloud Shell, create a JSON file called gvi-request.json.
- ### Save the following codes to the file.
      {
        "inputUri":"gs://spls/gsp154/video/train.mp4",
        "features": [
              "LABEL_DETECTION" ] }
              
- Go back to the Cloud Console, click on Navigation menu > APIs & Services > Credentials.
- Click the service account named with “Qwiklabs User Service Account” to view the details.
- Click ADD KEY > Create new key.
- Choose JSON and click CREATE to download the Private key file to your computer.
- Upload the file to the Cloud Shell environment.
- Rename the uploaded file to key.json.
- ### Run the following commands to create a token.
        gcloud auth activate-service-account --key-file key.json
        export TOKEN=$(gcloud auth print-access-token)
- ### Run the following command to use theGoogle Video Intelligence and detect all text on the video.
        curl -s -H 'Content-Type: application/json' \
        -H 'Authorization: Bearer '$(gcloud auth print-access-token)'' \
        'https://videointelligence.googleapis.com/v1/videos:annotate' \
        -d @gvi-request.json > task4-gvi.result

## Upload the resulted file to Cloud Storage by running:
      gsutil cp task4-gvi.result gs://<YOUR-PROJECT_ID>-marking/task4-gvi.result
Replace <YOUR-PROJECT_ID> with your project ID.
