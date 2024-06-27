## AWS IAM
### AWS IAM User 생성 
 - 루트사용자는 계정의 관리자입니다
 - IAM User는 해당계정의 사용자로 처음 계정을 만들면 사용자가 존재하지 않습니다
 - MAIN LAB 을 수행할 사용자를 생성합니다
    - IAM User Name : workshop-user

### User Policy(정책) 생성 
 - IAM User 에서 사용할 정책을 사용합니다 
 - IAM > 정책 > "정책생성" 버튼 
   ![alt text](image-7.png)
 - "서비스선택 : IAM" 선택 > "작업 허용됨" : "작업 필터링" 입력창에 "ListInstanceProfiles" 입력(디폴트 "허용")) > 다음
   ![alt text](image-8.png)
   ![alt text](image-6.png) 
 - 정책이름 : "IAMUser-ListInstanceProfiles"
   ![alt text](image-9.png)


 - IAM User(workshop-user) 권한 추가
    - AmazonEC2FullAccess
    - AmazonElasticFileSystemFullAccess
    - AmazonRDSFullAccess
    - AmazonSSMFullAccess
    - IAMUser-ListInstanceProfiles
   ![alt text](image-10.png)

### EC2 Role 생성

- IAM > 역할 > 역할 만들기
- 사용사례 EC2 선택 > 다음
![alt text](image-1.png)

- EC2 접속을 위한 SSM 정책 부여
![alt text](image-5.png)

- IAM Role 이름 설정
  ![alt text](image-4.png)

### IAM role 사용
#### Launch template EC2 Role 지정 
 - 해당 Role(역할)은 향후 EC2 생성시 사용합니다
 - Launch template 생성 >  EC2 IAM Role > "RoleforSSM"(생성한 Role) 로 지정
    ![alt text](image.png)

***