# aws-portfolio-project-3
Secure Image Uploads with Pre-Signed URLs (AWS SAM)

This project demonstrates a (secure, serverless image upload flow) using AWS API Gateway, Lambda, and S3 — with optional CloudFront CDN + Origin Access Control (OAC) and automatic thumbnail generation.

Features
- Secure uploads** via S3 pre-signed PUT URLs (no public bucket, no AWS keys exposed to clients)
- Least-privilege IAM policies
- Optional CloudFront + OAC to serve images/CDN cache privately from S3
- Optional thumbnail Lambda triggered by S3 events to create smaller images automatically
- CORS enabled** for front-end integration
- Infrastructure as Code via AWS SAM


