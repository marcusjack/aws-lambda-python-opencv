# Python OpenCV module for AWS Lambda

This is a simple script that build a deployement package including OpenCV compatible with the AWS Lambda Python runtime. The dynamic library is compiled with all extended instruction sets supported by Lambda CPU and binaries are stripped to save space. You simply need to add your code inside *lambda_funcion.py* and possibly your haar cascades files or additional Python modules. You can download directly a [pre-built archive](https://github.com/Plastic-1/aws-lambda-python-opencv/archive/prebuilt.zip).

- Build duration: ~20 min on T2.micro / ~15 min on C4.2xlarge
- Package size without haar cascades included: 26MB
- OpenCV 3.1 by default but may works with newer

**Need to be built on an Amazon Linux instance.**

## Module building
### Option 1: with an existing instance
- Download the repo `wget https://github.com/Plastic-1/aws-lambda-python-opencv/archive/master.zip`
- Unzip the archive `unzip master.zip`
- Launch the script `cd aws-lambda-python-opencv-master && ./build.sh`

### Option 2: without an existing instance
On the EC2 console, launch a new instance with:
- Amazon Linux AMI
- Role with S3 write permission
- Shutdown behavior: *Terminate*
- Paste the script below in the user data text field
```sh
#!/bin/bash
yum update -y
yum install -y git cmake gcc-c++ gcc python-devel chrpath

cd /tmp
wget https://github.com/Plastic-1/aws-lambda-python-opencv/archive/master.zip
unzip master.zip
chmod 777 aws-lambda-python-opencv-master
cd aws-lambda-python-opencv-master
su -c './build.sh' ec2-user

aws s3 cp lambda-package.zip s3://<my-target-bucket>
shutdown -h now
```
- Replace *my-target-bucket* by a bucket or your choice
- Less than 30 min later the instance will be terminated and the archive will be available on your bucket
