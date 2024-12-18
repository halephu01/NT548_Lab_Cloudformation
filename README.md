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
2. LAB2: 
   1. **AWS CodeCommit**:
      1. Tạo repository:
         ```
         aws codecommit create-repository --repository-name NT548_Lab2_Cloudformation
         ```
      2. Clone repository:
         ```
         pip install git-remote-codecommit
         git clone codecommit::ap-southeast-2://NT548_Lab2_Cloudformation
         ```
      3. Thêm các file và commit:
         ```
         git add .
         git commit -m "Nhom6 Lab2"
         git push -u origin master
         ```
   2. **AWS CodeBuild**:
      1. Tạo project: 
         ```
         Nhập tên project: Lab2
         ```
      2. Chọn Source:
         ```
         Source Provider: AWS CodeCommit
         Repository: NT548_Lab2_Cloudformation
         Branch: master
         ```
      3. Environment:
         ```
         Environment Type: Ubuntu
         Runtime: Standard
         Image: aws/codebuild/standard:5.0
         Service Role: 
            - Nếu chưa có thì tạo mới bằng cách nhập ở Role Name: Nhom6-Lab2-CodeBuild-Role
            - Nếu đã có thì chọn Existing service role và chọn Role ARN tương ứng
         ```
      4. Buildspec:
         ```
         Nhập vào file buildspec.yaml
         ```
      5. Các thông tin khác như:
         ```
         Batch configuration, Artifacts, Logs chọn như mặc định hoặc chọn theo ý muốn và chọn Create build project
         ```
      6. Chọn Start build

   3. **AWS CodePipeline**:
      1. Choose creation option: 
         ```
         Chọn Build custom pipeline
         ```
      2. Choose pipeline settings:
         ```
         Pipeline name: Lab2
         Pipeline type: Queued (Pipeline type V2 required)
         Service role: 
            - Nếu chưa có thì tạo mới bằng cách nhập ở Role Name: Nhom6-Lab2-CodePipeline-Role
            - Nếu đã có thì chọn Existing service role và chọn Role ARN tương ứng
         ```
      3. Add source stage:
         ```
         Source provider: AWS CodeCommit
         Repository name: NT548_Lab2_Cloudformation
         Branch: master
         Change detection options: AWS CodePipeline
         Output artifact format: CodePipeline default
         ```
      4. Add build stage:
         ```
         Build provider: AWS CodeBuild
         Project name: Lab2
         Build type: Single build
         Region: ap-southeast-2
         Input artifact: SourceArtifact
         ```
      5. Add deploy stage:
         ```
         Deploy provider: AWS CloudFormation
         Region: ap-southeast-2
         Input artifact: BuildArtifact
         Action mode: Create or update a stack
         Stack name: Nhom6
         Template:   
            ```
            Artifact file name: BuildArtifact
            File name: main.yaml
            ```
         Role name: Chọn role AwsCloudFormation đã tạo
      6. Review: 
         ```
         Kiểm tra lại các thông tin và chọn Create pipeline
         ```
