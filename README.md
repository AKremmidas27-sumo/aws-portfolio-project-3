### aws-portfolio-project-3
Secure Image Uploads with Pre-Signed URLs (AWS SAM)

This project demonstrates a (secure, serverless image upload flow) using AWS API Gateway, Lambda, and S3 — with optional CloudFront CDN + Origin Access Control (OAC) and automatic thumbnail generation.

Features
- Secure uploads via S3 pre-signed PUT URLs (no public bucket, no AWS keys exposed to clients)
- Least-privilege IAM policies
- Optional CloudFront + OAC to serve images/CDN cache privately from S3
- Optional thumbnail Lambda triggered by S3 events to create smaller images automatically
- CORS enabled** for front-end integration
- Infrastructure as Code via AWS SAM

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

