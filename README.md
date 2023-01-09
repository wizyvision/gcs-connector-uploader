# Google Cloud Storage Connector

> This repo contains the **Uploader** app that is deployed in Cloud Run. To run the whole Storage connector, you will also need the [**Trigger**](https://github.com/sephdiza/gcs-connector-trigger-function) function to be deployed in Cloud Function.


GCS Connector uploads files on WizyVision, on every files that will be uploaded in the source GCS bucket.


![gcs connector](https://user-images.githubusercontent.com/4800851/211000369-70e9be5f-36a6-4e60-8232-f6b73d892d8b.png)


## Installation
[![Run on Google Cloud](https://deploy.cloud.run/button.svg)](https://deploy.cloud.run)

### Adding Permissions
After successful deployment (applies for both using Cloud Run Button and Manual Installation), navigate to *GCP IAM & Admin* and find the default compute service account created.

<img width="978" alt="Screen Shot 2022-12-21 at 9 39 25 AM" src="https://user-images.githubusercontent.com/35460203/208800600-bcd461e5-63ee-4678-82d1-be8a2006249c.png">


Add these roles to the default service account:
- Storage Object Viewer
- Cloud Run Developer
- Pub/Sub Publisher


### Manual installation

1. Clone this repository.

```
git clone https://github.com/sephdiza/gcs-connector.git
```
2. Install [docker](https://docs.docker.com/get-docker).
3. Create a file named `.env` at the root of this folder. Copy the contents of `placeholder.env` and set the values.
```
# GCP
GOOGLE_CLOUD_PROJECT="<INPUT_YOUR_PROJECT_NAME_HERE>"

# Path where you put the service account file
GOOGLE_APPLICATION_CREDENTIALS_PATH="./secrets/service-account.json"

# WizyVision
# The application API key
WV_BEARER_API_KEY="<INPUT_WIZYVISION_APP_BEARER_API_KEY_HERE>"

# Public API url of your account
WV_PUBLIC_API_URL="https://mywizyvisiondomain.eu.wizyvision.app/api/v1/public/files"

# Comma-separated list of Tag IDs to set for the uploaded files
TAG_IDS=""

# Privacy to set for the uploaded files, Standard if this is left empty
PRIVACY_ID=""

# Header and token to be authenticated to call the Uploader service
UPLOADER_SERVICE_AUTH_HEADER="Wizdam-Dev-Csc-Token"
UPLOADER_SERVICE_AUTH_TOKEN=""

# Slack used for notification when errors are encountered. Can be left blank
SLACK_BEARER_TOKEN=""
SLACK_CHANNEL_ID=""
```
4. Build the image.
```
docker build -t IMAGE_NAME .
```
5. Push the image to the container registry.
```
docker tag IMAGE_NAME gcr.io/PROJECT_NAME_HERE/IMAGE_NAME
docker push gcr.io/PROJECT_NAME_HERE/IMAGE_NAME 
```
6. Deploy to cloud run.
```
gcloud run deploy IMAGE_NAME \
  --project PROJECT_NAME \
  --image IMAGE_URL \
  --region REGION \
  --allow-unauthenticated \
  --memory=256Mi \
  --max-instances=10
```
7. Take note of the URL of the service after successfully deploying. This will be needed in the [Trigger service](https://github.com/sephdiza/gcs-connector-trigger-function) deployment.
<img width="920" alt="Screen Shot 2023-01-06 at 9 57 03 AM" src="https://user-images.githubusercontent.com/35460203/210914793-204acff1-a8ec-4328-b022-0b4bf33b9277.png">

## Testing the Connector
> Note: The [**Trigger**](https://github.com/sephdiza/gcs-connector-trigger-function) function should already be deployed
1. Upload files on the GCS bucket
2. In WizyVision webapp, you should see the file uploaded there with the correct tags and privacy


