# Sử dụng CloudFormation để quản lý cơ sở hạ tầng trên AWS

## Giới thiệu

Sử dụng CloudFormation để triển khai VPC - Virtual Private Cloud trên cơ sở hạ tầng AWS, bao gồm các EC2 instances trong public và private subnet, NAT gateway, Internet Gateway cũng như các rule, security groups cần thiết để đảm bảo tính bảo mật cho các thành phần trong VPC.

## Cấu trúc file

```plaintext
.
├── main.yaml
├── vpc.yaml
├── subnets.yaml
├── gateways.yaml
├── route-tables.yaml
├── security-groups.yaml
├── ec2.yaml
```

## Hướng dẫn cài đặt
1. LAB1: 
   1. **Cấu hình AWS Credentials**: Cài đặt AWS CLI và sử dụng lệnh ```aws configure ``` để cấu hình cho phép Terraform tương tác với các tài nguyên của AWS
   2. **Clone Github Project và chạy code CloudFormation**:
   ```
   git clone https://github.com/halephu01/NT548_Lab1_Cloudformation
   cd NT548_Lab1_Cloudformation   
   ```
   3. **Chạy code CloudFormation**:
      1. Tạo Key Pair cho các EC2 instances bằng lệnh:
         ```
         aws ec2 create-key-pair --key-name nhom6 --query 'KeyMaterial' --output text > nhom6.pem
         ```
      2. Tạo S3 Bucket để lưu trữ các file template của CloudFormation:
         ```
         aws s3api create-bucket --bucket nhom6-lab1 --region ap-southeast-2 --create-bucket-configuration LocationConstraint=ap-southeast-2
         ```
      3. Upload toàn bộ file template lên S3 Bucket:
         ```
         aws s3 sync . s3://nhom6-lab1
         ```
      4. Tạo stack CloudFormation với các tham số cần thiết:
         ```
         aws cloudformation create-stack --stack-name nhom6 --template-body file://main.yaml
         ```
      5. Kiểm tra stack CloudFormation đã được tạo bằng lệnh:
         ```
         aws cloudformation describe-stacks --stack-name nhom6
         ```
      6. Dọn dẹp tài nguyên:
         ```
         aws cloudformation delete-stack --stack-name nhom6
         ```
##LAB2: 