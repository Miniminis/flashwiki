# AWS CodePipeline 으로 배포 자동화하기 (github)

## 1. Source Stage &#x20;

### 권한오류&#x20;

<figure><img src="../../.gitbook/assets/image (30).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (26).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (12).png" alt=""><figcaption></figcaption></figure>

[https://docs.aws.amazon.com/codepipeline/latest/userguide/troubleshooting.html#troubleshooting-S3-access-denied-list](https://docs.aws.amazon.com/codepipeline/latest/userguide/troubleshooting.html#troubleshooting-S3-access-denied-list)

```json
{
    "Version": "2012-10-17",
    "Id": "SSEAndSSLPolicy",
    "Statement": [
        {
            "Effect": "Allow",
            "Principal": "*",
            "Action": "s3:ListBucket",
            "Resource": "arn:aws:s3:::codepipeline-us-east-2-1234567890",
            "Condition": {
                "StringLike": {
                    "aws:userid": "AROAEXAMPLEID:*"
                }
            }
        },
        {
            "Sid": "DenyUnEncryptedObjectUploads",
            "Effect": "Deny",
            "Principal": "*",
            "Action": "s3:PutObject",
            "Resource": "arn:aws:s3:::codepipeline-us-east-2-1234567890/*",
            "Condition": {
                "StringNotEquals": {
                    "s3:x-amz-server-side-encryption": "aws:kms"
                }
            }
        },
        {
            "Sid": "DenyInsecureConnections",
            "Effect": "Deny",
            "Principal": "*",
            "Action": "s3:*",
            "Resource": "arn:aws:s3:::codepipeline-us-east-2-1234567890/*",
            "Condition": {
                "Bool": {
                    "aws:SecureTransport": false
                }
            }
        }
    ]
}
```

<figure><img src="../../.gitbook/assets/image (16).png" alt=""><figcaption></figcaption></figure>

## 2. Build Stage

<figure><img src="../../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

buildspec.yml 파일을 추가해준다.&#x20;

<figure><img src="../../.gitbook/assets/image (22).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (25).png" alt=""><figcaption></figcaption></figure>

## 3. Deploy Stage

<figure><img src="../../.gitbook/assets/image (10).png" alt=""><figcaption></figcaption></figure>

### aws codedeploy agent 실행 중 access denied error&#x20;

<figure><img src="../../.gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (24).png" alt=""><figcaption></figcaption></figure>

* [https://stackoverflow.com/questions/49945703/aws-codedeploy-access-denied-error](https://stackoverflow.com/questions/49945703/aws-codedeploy-access-denied-error)
* [https://github.com/aws/aws-codedeploy-agent/issues/301](https://github.com/aws/aws-codedeploy-agent/issues/301)



code-agent version check&#x20;

* sudo dpkg -s codedeploy-agent



* [https://velog.io/@inssg/CI-CD-AWS-Code-Deploy-Code-Pipeline-%ED%99%9C%EC%9A%A9](https://velog.io/@inssg/CI-CD-AWS-Code-Deploy-Code-Pipeline-%ED%99%9C%EC%9A%A9)
* [https://prohannah.tistory.com/201](https://prohannah.tistory.com/201)



<figure><img src="../../.gitbook/assets/image (28).png" alt=""><figcaption></figcaption></figure>

1. 배포 그룹에서 SSM 자동설치 해제한 설정으로 codedeploy group 재생성해보기&#x20;
   1. 결과 : 여전히 실패&#x20;
   2.

       <figure><img src="../../.gitbook/assets/image (29).png" alt=""><figcaption></figcaption></figure>



codeagent AccessDenied 문제 언급&#x20;

* [https://blog.leedoing.com/83](https://blog.leedoing.com/83)



EC2 재설치&#x20;

* GONGSAMO-DEV-PUBLIC-2a : vi /var/log/aws/codedeploy-agent/codedeploy-agent.log
* codedeploy 설치하지도 않았는데 깔려있음...?&#x20;

<figure><img src="../../.gitbook/assets/image (27).png" alt=""><figcaption></figcaption></figure>
