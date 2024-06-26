
## Infrastructure as Code(IaC) 란?

코드형 인프라(IaC)는 수동 프로세스 및 설정 대신 코드를 사용해서  
네트워크 또는 컴퓨팅 인프라를 프로비저닝하고 지원하는 기능입니다

- [ ] IaC를 사용하면
  1. 손쉽게 환경을 복제할수 있습니다
  2. 인프라 구성시 오류가 감소합니다
  3. 최적의 인프라 구성을 반복 사용할수 있습니다
  4. 인프라 구성 변화를 손쉽게 설정하고 추적(이력관리)

- [ ] AWS에서는 CloudFormation이 IaC를 지원합니다
- [ ] Cloud Service Provider(CSP) 에 종속적이지 않은 Terrafor을 활용할수도 있습니다



***
***
## AWS CloudFormation 인프라 구성

### AWS CloudFormation : 1. VPC 구성
리소스 기준으로 작성하며, 하위폴더 및 네이밍 규칙으로 구분한다
![image](https://github.com/llwindy999ll/workshop/assets/170963109/9fbf84ca-b0b0-4c07-91db-b4b9c89f82c8)

```
Description: Introduction to CloudFormation SFID - Virtual Private Cloud (VPC)
Resources:

  # Create a VPC
  MainVPC:
    Type: AWS::EC2::VPC
    Properties:
      CidrBlock: 10.0.0.0/16
      EnableDnsHostnames: 'true'
      EnableDnsSupport: 'true'
      Tags:
      - Key: Name
        Value: VPC for SFID CFN

# Create and attach InternetGateway
  InternetGateway:
    Type: AWS::EC2::InternetGateway
    DependsOn: MainVPC

  AttachIGW:
    Type: AWS::EC2::VPCGatewayAttachment
    Properties:
      VpcId: !Ref MainVPC
      InternetGatewayId: !Ref InternetGateway

# Create First Subnet
  FirstSubnet:
    Type: AWS::EC2::Subnet
    Properties:
      VpcId: !Ref MainVPC
      CidrBlock: 10.0.10.0/24
      AvailabilityZone: "us-east-1a"
      Tags:
      - Key: Name
        Value: Public Subnet A - SFID

# Creating additional subnet
  SecondSubnet:
    Type: AWS::EC2::Subnet
    Properties:
      VpcId: !Ref MainVPC
      CidrBlock: 10.0.20.0/24
      AvailabilityZone: "us-east-1b"
      Tags:
      - Key: Name
        Value: Public Subnet B - SFID

# Create and Set Public Route Table
  PublicRouteTable:
    Type: AWS::EC2::RouteTable
    Properties:
      VpcId:  !Ref MainVPC
      Tags:
      - Key: Name
        Value: Public Route Table

  PublicRoute:
    Type: 'AWS::EC2::Route'
    DependsOn: AttachIGW
    Properties:
      RouteTableId: !Ref PublicRouteTable
      DestinationCidrBlock: 0.0.0.0/0
      GatewayId: !Ref InternetGateway

  # Associate Public Subnets to Public Route Table
  PublicSubnet1RouteTableAssociation:
    Type: 'AWS::EC2::SubnetRouteTableAssociation'
    Properties:
      SubnetId: !Ref FirstSubnet
      RouteTableId: !Ref PublicRouteTable

  PublicSubnet2RouteTableAssociation:
    Type: 'AWS::EC2::SubnetRouteTableAssociation'
    Properties:
      SubnetId: !Ref SecondSubnet
      RouteTableId: !Ref PublicRouteTable
      
  # Create Security Group for the following:
  MainSecurityGroup:
    Type: AWS::EC2::SecurityGroup
    Properties:
      GroupDescription: Security Group for Web Server
      VpcId: !Ref MainVPC
      SecurityGroupIngress:
      - IpProtocol: tcp
        FromPort: 80
        ToPort: 80
  # Replace the IP address below by your Public IP Address:
        CidrIp: 127.0.0.1/32
      Tags:
      - Key: Name
        Value: Web Server Security Group - SFID

Outputs:
  MainSubnet:
    Value: !Ref FirstSubnet
    Description: Public Subnet ID with Direct Internet Route

  MainSecurityGroup:
    Value: !Ref MainSecurityGroup
    Description: Security Group ID for the Web Server



```






### AWS CloudFormation : 2. EC2 인스턴스 설정
리소스 기준으로 작성하며, 하위폴더 및 네이밍 규칙으로 구분한다
![image](https://github.com/llwindy999ll/workshop/assets/170963109/aab6974d-2e82-4f58-b0ee-a154e2855cb5)

```

Description: Introduction to CloudFormation SFID - Elastic Compute Cloud (EC2)

Parameters:
  PublicSubnet:
    Description: Select a Public Subnet created in the "VPC for SFID CFN" Lab (Hint - Search for "SFID")
    Type: 'AWS::EC2::Subnet::Id'
  SecurityGroup:
    Description: Select the Security Group created in the "VPC for SFID CFN" Lab (Hint - Search for "SFID")
    Type: 'AWS::EC2::SecurityGroup::Id'

Resources:
# Create EC2 Linux
  WebServerInstance:
    Type: AWS::EC2::Instance
    Properties:
      ImageId: "ami-07caf09b362be10b8"
      InstanceType: t3a.micro
      Tags:
          - Key: Name
            Value: Web Server for IMD
      UserData: 
        Fn::Base64:
          !Sub |
          #!/bin/sh
          yum -y install httpd
          chkconfig httpd on
          systemctl start httpd
          echo '<html><center><text="#252F3E" style="font-family: Amazon Ember"><h1>AWS CloudFormation is Fun !!!</h1>' > /var/www/html/index.html
          echo '<h3><img src="https://d0.awsstatic.com/logos/powered-by-aws.png"></h3></html>' >> /var/www/html/index.html
      NetworkInterfaces:
        - GroupSet:
            - !Ref SecurityGroup
          AssociatePublicIpAddress: 'true'
          DeviceIndex: '0'
          DeleteOnTermination: 'true'
          SubnetId: !Ref PublicSubnet

Outputs:
  PublicDNS:
    Value: !Join 
      - ''
      - - 'http://'
        - !GetAtt 
          - WebServerInstance
          - PublicDnsName
    Description: Web Host Public URL


```

***
