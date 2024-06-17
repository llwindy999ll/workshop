## AWS IAM
### AWS IAM User 생성 
 - workshop-user

 - 권한 추가
    - AmazonEC2FullAccess
    - AmazonElasticFileSystemFullAccess
    - AmazonRDSFullAccess
    - AmazonSSMFullAccess

### IAM role 생성

- IAM > 역할 > 역할 만들기
- 사용사례 EC2 선택 
 ![alt text](image-2.png)

- EC2 접속을 위한 SSM 정책 부여
![alt text](image-3.png)

- IAM Role 이름 설정
  ![alt text](image-4.png)

### IAM role 사용
#### Launch template EC2 Role 지정 
 - Launch template 생성시 EC2 IAM Role 로 지정
    ![alt text](image.png)

***