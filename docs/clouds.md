# Cloud Usage Examples

## AWS Topics
### [aws] manager aws resource using awscli

```
$ pip install awscli
$ aws configure
$ aws ec2 run-instances --region=us-west-2 --image-id=ami-9fa3xxx --key-name xilixxx --instance-type c5.2xlarge  --security-group-ids sg-xxx --subnet-id subnet-b4xxx --associate-public-ip-address --profile default --placement {\"AvailabilityZone\":\"us-west-2c\"}
$ aws ec2 describe-instances --instance-ids i-xxxxxxxx
$ aws ec2 create-tags --resources i-xxxxxxx --tags Key=Name,Value=xxxxxxx
$ aws ec2 describe-instance-types --instance-types m5zn.6xlarge --output json|jq '.[\"InstanceTypes\"][][\"VCpuInfo\"]'
$ aws ec2 get-console-output --output text --latest --instance-id i-xxxxxx
$ aws ec2 stop-instances --instance-ids i-xxxxxxx --force
$ aws ec2 describe-instances --instance-ids i-0fda3024xxxxxx
$ aws ec2 describe-instances --filters \"Name=instance-type,Values=c5.4xlarge\"
$ aws ec2 create-volume --size 80 --region ap-northeast-1 --availability-zone ap-northeast-1a --volume-type gp2
$ aws ec2 attach-volume --volume-id vol-xxxxxxxx --instance-id i-xxxxxxx --device /dev/xvdb
$ aws ec2 describe-images --image-id ami-xxxxxx --query 'Images[].EnaSupport'
$ aws ec2 describe-images --query 'Images[*].{ID:ImageId,Name:Name,Public:Public}' --filters 'Name=name,Values=*JBEAP-7.2.0*'  --region us-west-1
$ aws ec2 describe-images --filters Name=name,Values=RHEL-8.[1,2] --region us-west-2  --output text --query 'Images[*].[ImageId, Name, Public]'
$ aws ec2 describe-images --owners xxxxxx|self --query 'sort_by(Images, &CreationDate)", "out": ""},
$ aws ec2 describe-images --owners xxxxxx|self --query 'sort_by(Images, &CreationDate)[*].[CreationDate,Name,ImageId]' --filters \"Name=name,Values=RHEL-7.?*GA*\" --region us-east-1 --output table
// https://signin.aws.amazon.com/switchrole?roleName=xxxx&account=xxxx (switch role from web)
$ aws sts assume-role --profile redhat-xxxx --role-arn \"arn:aws:iam::xxxx:role/xxxx\" --role-session-name session-xiliang (siwtch role from cli)
$ aws sts get-caller-identity (get current user)

```      
### [aws] register AMI by uploading local image - [rhel_doc](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html-single/deploying_red_hat_enterprise_linux_7_on_public_cloud_platforms/index?#deploying-a-virtual-machine-on-aws_cloud-content)
```
// optional if you have raw file already
$ qemu-img convert -f qcow2 -O raw rhel-server-7.7-1-x86_64-kvm.qcow2 rhel-server-7.7-1-x86_64-kvm.raw
$ aws s3 cp rhel-server-7.7.1-x86_64-kvm.raw s3://s3-bucket-name
// import-image directly, not recommended
$ aws ec2 import-image --platform Linux --license-type BYOL --no-encrypted --description 'xiliang_7.9gadoc' --architecture x86_64 --disk-containers Format=Raw,UserBucket=\"{S3Bucket=virtqes1,S3Key=rhel-server-ec2-7.9-30.x86_64.raw}\" --region us-east-1
$ aws ec2 describe-import-image-tasks --import-task-ids import-ami-xxxxxxxxx
// import as snapshot and then do register
$ aws ec2 import-snapshot --disk-container file://containers_9.json --profile xxxxxx --region us-east-1"
$ cat containers_9.json
{
    "Description": "xiliang_rhel-ec2-9.4-xxx.raw",
    "Format": "raw",
    "UserBucket": {
        "S3Bucket": "xilxx_xxxx",
        "S3Key": "rhel-ec2-9.4-xxx.raw"
    }
}
$ aws ec2 describe-import-snapshot-tasks --import-task-ids import-snap-xxxxxx --profile xxxxxx --region us-east-1
$ aws ec2 register-image --name 'xiliang_RHEL-9.0.0-20210602.1' --description 'xiliang_RHEL-9.0.0-20210602.1' --architecture x86_64  --virtualization-type hvm --root-device-name '/dev/sda1' --block-device-mappings '{\"DeviceName\": \"/dev/sda1\",\"Ebs\": {\"SnapshotId\": \"snap-0890c56xxxxxx\"}}' --ena-support --profile redhat-test --region us-east-1
```

### [aws] share ami to another account
```
$ aws ec2 modify-image-attribute --profile xxx --region us-east-1 --launch-permission "Add=[{UserId=<account>}]" --image-id ami-0xxxx
$ aws --profile xxx --region us-east-1 ec2 modify-snapshot-attribute --snapshot-id "snap-066xxxxxx" --create-volume-permission "{
    \"Add\": [
        {
            \"UserId\": \"<account>\"
        }
    ]
    }"
```
note: share snapshot to allow the other account to copy the ami

### [aws] ec2_allow_console_serial policy

grant permission to allow account access serial console 
```
{
	"Version": "2012-10-17",
	"Statement": [
		{
			"Effect": "Allow",
			"Action": [
				"ec2:GetSerialConsoleAccessStatus",
				"ec2:EnableSerialConsoleAccess"
			],
			"Resource": [
				"*"
			]
		},
		{
			"Sid": "AllowDescribeInstances",
			"Effect": "Allow",
			"Action": [
				"ec2:DescribeInstances",
				"ec2:DescribeInstanceTypes",
				"ec2:GetSerialConsoleAccessStatus"
			],
			"Resource": "*"
		},
		{
			"Sid": "AllowinstanceBasedSerialConsoleAccess",
			"Effect": "Allow",
			"Action": [
				"ec2-instance-connect:SendSerialConsoleSSHPublicKey",
				"ec2-instance-connect:SendSSHPublicKey",
				"ec2-instance-connect:OpenTunnel"
			],
			"Resource": [
				"*"
			]
		}
	]
}
```

### [aws] allow_image_import policy

grant permission to allow account upload images 
```
{
	"Version": "2012-10-17",
	"Statement": [
		{
			"Effect": "Allow",
			"Action": [
				"s3:GetBucketLocation",
				"s3:GetObject",
				"s3:PutObject"
			],
			"Resource": [
				"arn:aws:s3:::xxx",
				"arn:aws:s3:::xxx/*"
			]
		},
		{
			"Effect": "Allow",
			"Action": [
				"ec2:CancelConversionTask",
				"ec2:CancelExportTask",
				"ec2:CreateImage",
				"ec2:CreateInstanceExportTask",
				"ec2:CreateTags",
				"ec2:DescribeConversionTasks",
				"ec2:DescribeExportTasks",
				"ec2:DescribeExportImageTasks",
				"ec2:DescribeImages",
				"ec2:DescribeInstanceStatus",
				"ec2:DescribeInstances",
				"ec2:DescribeSnapshots",
				"ec2:DescribeTags",
				"ec2:ExportImage",
				"ec2:ImportInstance",
				"ec2:ImportVolume",
				"ec2:StartInstances",
				"ec2:StopInstances",
				"ec2:TerminateInstances",
				"ec2:ImportImage",
				"ec2:ImportSnapshot",
				"ec2:DescribeImportImageTasks",
				"ec2:DescribeImportSnapshotTasks",
				"ec2:CancelImportTask"
			],
			"Resource": "*"
		}
	]
}
```