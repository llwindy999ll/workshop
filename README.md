# AWS 고가용성 웹 어플리케이션 구축 실습

## Session Manager를 사용하여 리눅스 인스턴스에 접근하기
- [ ] 참고 URL : https://catalog.workshops.aws/general-immersionday/ko-KR/basic-modules/10-ec2/ec2-linux/3-ec2-1
- [ ] Session Manager는 대화형 원클릭 브라우저 기반 셸 또는 AWS CLI를 통해 Amazon EC2 인스턴스를 관리할 수 있는 AWS Systems Manager의 기능입니다. Session Manager를 사용하여 계정의 인스턴스에 세션을 시작할 수 있습니다. 세션이 시작된 후, 다른 연결 유형을 통해 bash 명령을 실행할 수 있습니다.

***
## 도입배경

- [ ] AWS 인프라를 코드로 관리하기 위한 IaC 도구
- [ ] 이력관리 및 표준화를 통해 인프라 파악 및 관리가 쉽도록 도입
    - 담당자 변경시에도 쉽고 빠르게 업무 파악
    - RCS/MSGHUB 서비스간 동일한 품질의 인프라 관리

***

### AWS CloudFormation VPC 구성
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
![image](https://github.com/llwindy999ll/workshop/assets/170963109/d0ee29c4-342e-49a8-9672-84cba9d7e4e8)

