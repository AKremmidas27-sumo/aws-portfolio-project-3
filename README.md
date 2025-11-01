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

[IMG_2933.pdf](https://github.com/user-[IMG_2935.pdf](https://github.com/user-attachments/files/23281710/IMG_2935.pdf)

  [IMG_2935.pdf](https://github.com/user-attachments/files/23281739/IMG_2935.pdf)
    
  [IMG_2938.pdf](https://github.com/user-attachments/files/23281756/IMG_2938.pdf)

  [IMG_2941.pdf](https://github.com/user-attachments/files/23281762/IMG_2941.pdf)

    [IMG_2942.pdf](https://github.com/user-attachments/files/23281763/IMG_2942.pdf)
    
    [IMG_2943.pdf](https://github.com/user-attachments/files/23281764/IMG_2943.pdf)

    [IMG_2944.pdf](https://github.com/user-attachments/files/23281765/IMG_2944.pdf)

    [IMG_2946.pdf](https://github.com/user-attachments/files/23281768/IMG_2946.pdf)
    
    [IMG_2948.pdf](https://github.com/user-attachments/files/23281769/IMG_2948.pdf)

    [IMG_2949.pdf](https://github.com/user-attachments/files/23281771/IMG_2949.pdf)

    [IMG_2950.pdf](https://github.com/user-attachments/files/23281772/IMG_2950.pdf)

    [IMG_2952.pdf](https://github.com/user-attachments/files/23281774/IMG_2952.pdf)

    [IMG_2957.pdf](https://github.com/user-attachments/files/23281775/IMG_2957.pdf)
    
    [IMG_2970.pdf](https://github.com/user-attachments/files/23281788/IMG_2970.pdf)

    [IMG_2972.pdf](https://github.com/user-attachments/files/23281789/IMG_2972.pdf)
    
    [IMG_2981.pdf](https://github.com/user-attachments/files/23281791/IMG_2981.pdf)
    
    [IMG_2993.pdf](https://github.com/user-attachments/files/23281793/IMG_2993.pdf)

    [IMG_2998.pdf](https://github.com/user-attachments/files/23281801/IMG_2998.pdf)
    
    [IMG_2999.pdf](https://github.com/user-attachments/files/23281802/IMG_2999.pdf)

    

    
