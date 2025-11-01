### AWS PORTFOLIO 3 PROJECT - SERVERLESS IMAGE UPLOAD FLOW 

Secure Image Uploads with Pre-Signed URLs (AWS SAM)

This project demonstrates a (secure, serverless image upload flow) using AWS API Gateway, Lambda, and S3 — with optional CloudFront CDN + Origin Access Control (OAC) and automatic thumbnail generation.

Features
- Secure uploads via S3 pre-signed PUT URLs (no public bucket, no AWS keys exposed to clients)
- Least-privilege IAM policies
- Optional CloudFront + OAC to serve images/CDN cache privately from S3
- Optional thumbnail Lambda triggered by S3 events to create smaller images automatically
- CORS enabled** for front-end integration
- Infrastructure as Code via AWS SAM

## AWS CLI SETUP

# 0) Variables
REGION=us-east-2
ACCOUNT_ID=$(aws sts get-caller-identity --query Account --output text)
PKG_BUCKET=andrew-sam-artifacts-$ACCOUNT_ID-$REGION

# 1) Initialize SAM project
sam init --name aws-portfolio-project-3 --runtime python3.12 --app-template hello-world

# 2) Update runtime and add Lambda source (src/app.py)

# 3) Build and package
sam build --use-container
sam package --template-file template.yaml \
  --s3-bucket "$PKG_BUCKET" \
  --output-template-file packaged.yaml

# 4) Deploy
sam deploy \
  --template-file packaged.yaml \
  --stack-name image-upload-stack \
  --region $REGION \
  --capabilities CAPABILITY_IAM

API=https://nvdopp01vj.execute-api.us-east-2.amazonaws.com/Prod

# Test Commands
1) Request presigned URL
curl -s "$API/generateUploadUrl?ext=jpg&contentType=image/jpeg" | tee /tmp/url.json

2) Extract values
UPLOAD_URL=$(jq -r .uploadUrl /tmp/url.json)
KEY=$(jq -r .key /tmp/url.json)

3) Upload a test file
echo "hello image" > /tmp/test.jpg
curl -i -X PUT -H "Content-Type: image/jpeg" --data-binary "@/tmp/test.jpg" "$UPLOAD_URL"

4) Verify in S3
aws s3api head-object \
  --region us-east-2 \
  --bucket image-upload-stack-uploadbucket-h5xocqhpmpno \
  --key "$KEY" \
  --query "{Key:Key,Size:ContentLength,ContentType:ContentType,ETag:ETag}"

   


# LAMBDA SUMMARIZATION EFFORTS
import json, os, uuid, boto3
from botocore.config import Config
from urllib.parse import urlparse, urlunparse

BUCKET = os.environ["BUCKET_NAME"]
REGION = os.environ.get("AWS_REGION", "us-east-2")
EXPIRES = 3600

s3 = boto3.client("s3", region_name=REGION)
def lambda_handler(event, context):
    key = f"uploads/{uuid.uuid4()}.jpg"
    url = s3.generate_presigned_url(
        "put_object",
        Params={"Bucket": BUCKET, "Key": key, "ContentType": "image/jpeg"},
        ExpiresIn=EXPIRES,
    )
    # Force regional host to avoid 307 redirects
    p = urlparse(url)
    if p.hostname and p.hostname.endswith("s3.amazonaws.com"):
        bucket = p.hostname.split(".")[0]
        new_host = f"{bucket}.s3.{REGION}.amazonaws.com"
        p = p._replace(netloc=new_host)
        url = urlunparse(p)
    return {
        "statusCode": 200,
        "headers": {"Access-Control-Allow-Origin": "*"},
        "body": json.dumps({"uploadUrl": url, "key": key, "expiresIn": EXPIRES})
    }

  
  ![IMG_2933](https://github.com/user-attachments/assets/2c149e44-dfa8-4133-8c26-495bb08b85b6)

  ![IMG_2935](https://github.com/user-attachments/assets/8ae966eb-23d1-4fe4-861f-616f3d32e2dc)

  ![IMG_2937](https://github.com/user-attachments/assets/63802f20-9c6a-4a68-bd6e-5a195728295e)

  ![IMG_2938](https://github.com/user-attachments/assets/dd4f7088-7b6a-43af-9c10-6addd9376d16)

  ![IMG_2941](https://github.com/user-attachments/assets/54fe60c0-e1b1-4861-beab-e79f2e6a74a7)

  ![IMG_2942](https://github.com/user-attachments/assets/afb63813-36b4-473f-9f85-8790fc3ea024)

  ![IMG_2944](https://github.com/user-attachments/assets/688ea492-c246-419b-8826-ca08fcdbcfca)

  ![IMG_2944](https://github.com/user-attachments/assets/8ba09406-9f99-4bc5-86dd-c874bb25fe2f)

  ![IMG_2945](https://github.com/user-attachments/assets/20802996-4d10-4941-9552-2a3a2bf407fe)

  ![IMG_2946](https://github.com/user-attachments/assets/7d88bee2-9ee0-4676-b7e3-2dc4f9215246)

  ![IMG_2948](https://github.com/user-attachments/assets/2fa4da09-8840-4156-8e89-0cb10f7d68d7)

  ![IMG_2949](https://github.com/user-attachments/assets/d0a05093-d3c8-498e-a636-79a848185797)

  ![IMG_2957](https://github.com/user-attachments/assets/e2f261d1-baad-491e-bdcf-57b5961ad5cc)
  
  ![IMG_2970](https://github.com/user-attachments/assets/6f46eef2-e8ae-4717-9f72-58430ae9194c)

  ![IMG_2972](https://github.com/user-attachments/assets/e9cfedbc-39f3-45fd-90a9-59e3524a8a92)

  ![IMG_2998](https://github.com/user-attachments/assets/bf8331fc-0ef9-4199-83ab-bce298935cfe)

  ![IMG_2999](https://github.com/user-attachments/assets/f2cb6c96-9b32-46e7-bd78-02c5d1767107)


   
