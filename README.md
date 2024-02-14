#Commands Playbook
#Set Custom variables
export PROJECT_ID=gcp-project-sarunraj2023
export REGION=us-east4-a
export BUCKET=gcs-dbt-poc


#Setting up gcloud defaults
#Set your default project:
gcloud config set project $PROJECT_ID
#Configure gcloud for your chosen region:
gcloud config set run/region $REGION
gcloud config set builds/region $REGION#Service Account Creation

#Create a service account to serve as the service identity:
gcloud iam service-accounts create cr-identity
#Grant the service account access to the Cloud Storage bucket:
gcloud projects add-iam-policy-binding $PROJECT_ID --member "serviceAccount:cr-identity@$PROJECT_ID.iam.gserviceaccount.com" --role "roles/storage.objectAdmin"

#Creating the Cloud Run Service
#Ensure you are in the root of this directory
#This will create a Cloud Run service with name cloudrun-static-demo that will be accessible from anywhere

gcloud builds submit --tag gcr.io/gcp-project-sarunraj2023/website-fuse-image
gcloud beta run deploy --image gcr.io/gcp-project-sarunraj2023/website-fuse-image --execution-environment gen2 --region us-east4 --allow-unauthenticated --service-account cr-identity --set-env-vars BUCKET=gcs-dbt-poc --port 80
