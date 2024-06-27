***
# AWS 실습 보충자료

## AWS 클라우드 학습 사이트
 - [ ] [AWS Workshop](https://workshops.aws/)
    - 분야별 수준별로 실제 클라우드 시나리오를  
      AWS 실습 환경에서 직접 체험해 볼 수 있도록 다양한 실습 가이드를 제공합니다
  
 - [ ] [AWS Free tier 소개](https://aws.amazon.com/ko/free/) 
    - AWS에서는 3가지 유형으로 무료로 서비스를 체험해볼수 있는 기회를 제공하고 있습니다
        - 12개월 무료: 이 프리 티어 오퍼는 AWS 신규 고객에게만 제공되며 AWS 가입일로부터 12개월 동안 유효합니다. 
        - 언제나 무료: 이 프리 티어 오퍼는 12개월의 AWS 프리 티어 기간이 끝나도 자동으로 만료되지 않으며, 기존 및 신규 AWS 고객 모두에게 무기한으로 제공됩니다.
        - 평가판: 이 프리 티어 오퍼는 처음 사용을 시작한 시점부터 사용 가능한 단기 평가판입니다. 평가판 기간이 만료된 경우에는 사용량에 따라 표준 서비스 요금을 지불하면 됩니다(전체 요금 내역은 해당 서비스 페이지 참조).

***
***

## 실습과제 

### MAIN LAB 
 - [ ] 클라우드에 고가용성 웹 서비스 구축하기 
   - [Highly Available Web Application Workshop](https://catalog.us-east-1.prod.workshops.aws/workshops/3de93ad5-ebbe-4258-b977-b45cdfe661f1/en-US)
      - 홈페이지, 블로그, 쇼핑몰 등 다양하게 제작이 가능한 오픈소스 서버  
   '워드프레스(Word press)'를 활용하여 간단한 웹서비스를 구축해보는 실습입니다
      - AWS 기본 서비스인 VPC, RDS, EC2, EFS 를 활용해 구축해 봅니다
      
  - [ ] LAB 변경사항
    - VPC 생성방법 : 수동생성 -> 자동생성 (교재 참조)
      - VPC Name 지정 : wordpress-workshop
      - Subnet 변경 : Public subnet 2, Private subnet 4 (총 6개 Subnet 지정) 
      - CIDR 대역 변경 
        - VPC : 10.2.0.0/16
        - PUB A: 10.2.0.0/24
        - PUB B: 10.2.1.0/24
        - App A: 10.2.2.0/24
        - App B: 10.2.3.0/24
        - DATA A: 10.2.4.0/24
        - DATA B: 10.2.5.0/24
    - 보안그룹 변경 : 리소스별 SG 생성 -> deault로 변경 (교재 참조)
      - 실습과정에서 리소스별 Security Group은 모두 default SG로 변경
        - default Security Group은 별로 in/out bount rule 설정이 필요없음
      - 예외 SG : ALB security group "WP Load Balancer"는 추가 생성이 필요하며 ALB 에 추가 적용
        - ALB SG : "default" +  WP Load Balancer"


### LAB START 
 - [ ] AWS 루트 사용자(계정관리자) 로그인
    - https://aws.amazon.com/ko/  이동
    - "콘솔에 로그인" > "루트 사용자" 선택 > ID/PW 입력 >"로그인"


 - [ ] AWS 루트계정 설정
   - [MFA 2차인증 설정](/AWS%20Start/MFA.md)
   - [IAM 사용자 및 권한 설정](/AWS%20Start/IAM.md)
      - 정책 생성 및 EC2 서버 권한 설정
      - 일반(계정) 사용자 생성
      

 - [ ] 웹서버(EC2) 구성/접근
   - [ASG 사용할 템플릿 구성: EC2 Launch Template 생성](/EC2%20Access/Launch%20Template.md)
   - [접근방법1: Session Manager로 EC2 접근](/EC2%20Access/Session%20Manager.md)
   - [접근방법2: ID/PW로 EC2 접근](/EC2%20Access/IDPW.md)

### AWS 리소스 정리
 - [ ] [리소스 정리(삭제)](/Delete/Delete%20resource.md) 
   - AWS 계정 해지시에도 비용이 청구될수 있습니다
   - 계정을 혜지하기 전에 AWS Console에 루트계정으로 접속하여 리소스가 정상적으로 삭제되었는지 다시한번 확인합니다
   - 실습과정에 사용된 리소는 IAM, VPC, EC2, RDS, EFS 이 5가지 Console 화면을 벗어나지 않습니다  
   - IAM은 계정관리 서비스로 해당 Console에서 비용발생하는 리소스는 없습니다

***
***

## SIDE LAB  
 - [Auto Scaling LAB](/Side%20LAB/LAB.md) 
   - 웹서버의 부하 증가시 자동으로 서버가 증설되는 LAB  


## 유용한 도구들
 - [ ] [테스트 도구](/Tools/tools.md)

## IaC 란
 - [ ] [코드로 인프라 관리하기](/IaC/IaC.md)




***
***

