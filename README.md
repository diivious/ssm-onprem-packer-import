# ssm-onprem-packer-import

This packer template will allow you to convert the Cisco Smart Software Manager On-Prem ISO to an AWS AMI.

## Prerequisites

- Install VirtualBox (https://www.virtualbox.org/wiki/Downloads)
- Install Packer (https://learn.hashicorp.com/tutorials/packer/getting-started-install)

## AWS Set-up

1. Create a Service Role for VM Import
```
 {
   "Version": "2012-10-17",
   "Statement": [
      {
         "Effect": "Allow",
         "Principal": { "Service": "vmie.amazonaws.com" },
         "Action": "sts:AssumeRole",
         "Condition": {
            "StringEquals":{
               "sts:Externalid": "vmimport"
            }
         }
      }
   ]
}
```
2. Create a Role/Policy for Import
```
{
   "Version":"2012-10-17",
   "Statement":[
      {
         "Effect":"Allow",
         "Action":[
            "s3:GetBucketLocation",
            "s3:GetObject",
            "s3:ListBucket"
         ],
         "Resource":[
            "arn:aws:s3:::disk-image-file-bucket",
            "arn:aws:s3:::disk-image-file-bucket/*"
         ]
      },
      {
         "Effect":"Allow",
         "Action":[
            "ec2:ModifySnapshotAttribute",
            "ec2:CopySnapshot",
            "ec2:RegisterImage",
            "ec2:Describe*"
         ],
         "Resource":"*"
      }
   ]
}
```

### Build Set-up

1. Download your desired SSM On-Prem Version (https://software.cisco.com/download/home/286285506/type)
2. Save the ISO to your desired location and update the "iso_url" with the path to the ISO
3. Copy the md5 checksum and update in "iso_checksum"
4. Update AWS Region
5. Update AWS S3 Bucket Name

### Run Packer Build

```
packer build \
    -var 'aws_access_key=your_access_key' \
    -var 'aws_secret_key=your_secret_key' \
    onprem-import.json
 ```
 
 

