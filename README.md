FACIAL RECOGNITION INTEGRATED WITH AWS INFRASTRUCTURE
CREATING PROJECT By: Mihir Parekh, Cristian Lopez, Vishal Patel, Doulos Htet
CLI Commands
Create the VPC variable
$VpcId="$(aws ec2 describe-vpcs --filter "Name=isDefault, Values=true" --query "Vpcs[0].VpcId"
--output text)"
Set up the subnet for the VPC
$SubnetId="$(aws ec2 describe-subnets --filters "Name=vpc-id,Values=$VpcId" --query
"Subnets[0].SubnetId" --output text)"
Creates stack from Cloudformation and launches EC2 Instance to create a VM based on our
virtualmachine.yaml file (template).
Purpose is to make the environment get the files from our github repo
aws cloudformation create-stack --template-body file://virtualmachine.yaml --stack-name team4
--parameters ParameterKey=VPC,ParameterValue=$VpcId
ParameterKey=Subnet,ParameterValue=$SubnetId
Gets the log file for installing dependency
sudo cat /var/log/cloud-init-output.log
Sets the AWS Access ID Key, AWS Secret Access Key, Region
aws configure set aws_access_key_id ‘AKIAZA3ZDHAEG3N4YY62’ && aws configure set
aws_secret_access_key ‘cXYuR2nkkpMSDME0gCrPCSeDdULxMoNIaOptpuws’ && aws
configure set region ‘us-east-1’ && aws configure set output ‘text’
Grab Python files from our Github Repo GitHub - douloswynnh/team4
sudo git clone https://github.com/douloswynnh/team4
Grabs images from the internet and puts it directly into our dictionary
wget -r -A .jpeg .jpg <url_link>
Add the person's face and name to S3 Bucket along with name added as a metadata. This will
then trigger a lambda function (facerecognition) to move items from S3 to DynamoDB table
python putUsers.py image1 name
FACIAL RECOGNITION INTEGRATED WITH AWS INFRASTRUCTURE
Checks if the first image on the stack (data structure) matches any image from our database.
Will print if the person is recognized or not using the Facial Recognition API.
python testFacialRec.py
CLEAN UP
This will delete stack along with EC2 instance
aws cloudformation delete-stack --stack-name team4
This deletes the DynamoDB Table
aws dynamodb delete-table --table-name facerecognition
This deletes the S3 Bucket
aws s3api delete-bucket --bucket serverless-team-4-s3uploadbucket-8qgj3onpd8i7--region
us-east-1
Deletes the Lambda function
aws lambda delete-function \
--function-name facerecognition