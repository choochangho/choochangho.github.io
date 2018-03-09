---
layout: post
title:  "AMI Snapshot 에 이름 부여하기"
categories: amazon AMI Snapshot
date:   2018-03-09 09:33
permalink: /archivers/amazon-ami-snapshot
---

# 현재 상태

1. AutoScaling 을 사용하여 웹서비스를 운영
2. 웹 어플리케이션 소스 코드가 배포될때 마다 운영중인 인스턴스를 이미지로 저장
3. 저장된 이미지를 Launch Configuration 의 AMI 로 등록하여 Scale Up 이 될 경우 해당 인스턴스로 사용하도록 함.
4. 1 ~ 3 의 전 과정을 Python3 + boto3 의 조합으로 자동화 하여 사용중

# 확인 및 문제점

1. 새로운 이미지가 생성될때 마다 스냅샷(Management Console > EC2 > ELASTIC BLOCK STORE > Snapshots)이 추가됨을 확인 할수 있음.
![생성된 AMI]({{ base }}/assets/images/2018-03-09/created_amis.png)
*생성된 AMI(Management Console > EC2 > IMAGES > AMIs)*
2. 1에서 새롭게 생성된 스냅샷은 이름(Name)이 없어 어떤 AMI의 스냅샷인지 구분하기 어려움.
![이름 없는 스냅샷]({{ base }}/assets/images/2018-03-09/none_name_snapshots.png)
*이름 없는 스냅샷(Management Console > EC2 > ELASTIC BLOCK STORE > Snapshots)*

# boto3의 API 확인

http://boto3.readthedocs.io/en/latest/reference/services/ec2.html#EC2.Client.create_tags
```python
response = client.create_tags(
    DryRun=True|False,
    Resources=[
        'string',
    ],
    Tags=[
        {
            'Key': 'string',
            'Value': 'string'
        },
    ]
)
```
해당 API의 Parameters 를 읽어보면 Resources에 ID(예: ami-1a2b3c4d)를 넣을수 있는것을 알수 있다.

하지만, Snapshot id 를 넣을 수 있는지는 AWS Case Open 을 통해 확인을 했다.

아래는 답변 메일로 받은 내용중 일부이다.
![Case Open Mail]({{ base }}/assets/images/2018-03-09/case_open_mail.png)
*답변 메일중 일부*

즉, 공식 문서의 예제로 나온 ami ID 가 아닌 snapshot ID 를 넣어도 된다는 점이다.

이제, 코드만 작성하면 된다.

# Image 의 정보 확인

```python
import datetime
import boto3
import pprint


# pretty print dict(json)
pp = pprint.PrettyPrinter()

# change the default profile
boto3.setup_default_session(
    profile_name='profile name',
    region_name='ap-northeast-2'
)
client = boto3.client('ec2')

# Get all images
# 0123456789 => ACCOUNT ID
response = client.describe_images(Owners=['0123456789',])
pp.pprint(response)
```

결과를 보자.

```text
{'Images': [{'Architecture': 'x86_64',
             'BlockDeviceMappings': [{'DeviceName': '/dev/xvda',
                                      'Ebs': {'DeleteOnTermination': True,
                                              'Encrypted': False,
                                              'SnapshotId': 'snap-XXXXXXXXXXXXX',
                                              'VolumeSize': 8,
                                              'VolumeType': 'gp2'}}],
             'CreationDate': '2018-03-07T14:12:07.000Z',
             'EnaSupport': True,
             'Hypervisor': 'xen',
             'ImageId': 'ami-XXXXXXXX',
             'ImageLocation': '0XXXXXXXXXXX/front-web-20180307231207',
             'ImageType': 'machine',
             'Name': 'front-web-20180307231207',
             'OwnerId': '0XXXXXXXXXXXXX',
             'Public': False,
             'RootDeviceName': '/dev/xvda',
             'RootDeviceType': 'ebs',
             'SriovNetSupport': 'simple',
             'State': 'available',
             'VirtualizationType': 'hvm'}],
```
대략 위와 같은 형태로 리턴이 되든것을 알 수 있다. 여기서 BlockDeviceMappings -> Ebs -> SnapshotId
가 우리가 원하는 SnapshotId 이다.

# create_tags 함수

위에서 SnapshotId 와 ImageId 그리고 이미지의 Name 까지 확인이 되었으니 스냅샷에 이름을 부여할 수 있게 되었다.

다만, 스냅샷은 이미지 하나당 N개가 리턴되므로 스냅샷의 이름을 시퀜셜하게 부여하면 되겠다.

# 전체 코드

위에서 살펴본 내용으로 내가 만든 모든 이미지와 그 이미지의 스냅샷에 이름을 부여하는 코드를 만들면 아래와 같을 것이다.

```python
import datetime
import boto3
import pprint


# pretty print dict(json)
pp = pprint.PrettyPrinter()

# change the default profile
boto3.setup_default_session(
    profile_name='my_profile_name',
    region_name='ap-northeast-2'
)
client = boto3.client('ec2')

# Get all images with my ACCOUNT ID
response = client.describe_images(Owners=['0XXXXXXXXXXX',])
ec2 = boto3.resource('ec2')
for image in response['Images']:
    ImageId = image['ImageId']
    Name = image['Name']
    BlockDeviceMappings = image['BlockDeviceMappings']
    i = 1
    for BlockDeviceMapping in BlockDeviceMappings:
        snapshot_seq = '(' + str(i).zfill(3) + ')'
        SnapshotId = BlockDeviceMapping['Ebs']['SnapshotId']
        snapshot_name = Name + snapshot_seq
        ec2.create_tags(
            DryRun=False,
            Resources=[SnapshotId,],
            Tags=[
                {
                    'Key': 'Name',
                    'Value': snapshot_name
                },
            ]
        )
        i += 1
```

# 결과 확인

위의 전체코드를 파일로 저장후 실행시키면 Management Console 의 결과는 아래와 같을 것이다.

![스냅샷 이름이 부여된 결과]({{ base }}/assets/images/2018-03-09/result.png)
*스냅샷 이름이 부여된 결과*


# 참고
- [BOTO3 create_tags](http://boto3.readthedocs.io/en/latest/reference/services/ec2.html#EC2.Client.create_tags)
