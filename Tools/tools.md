## 리눅스 서버 부하 테스트
- [ ] stress : CPU, 메모리, I/O 및 디스크 부하를 생성하는 데 사용

    ```
    sudo dnf install -y stress-ng

    # 1코어에 CPU 부하를 60%로 설정 (stress)
    stress-ng --cpu 1 --cpu-load 60 --timeout 60s

    # 메모리 부하 생성 (stress)
    stress --vm 2 --vm-bytes 256M --timeout 60

    ```

- [ ] 테스트 접속시
    - 웹 브라우저 강제 새로고침 :  Ctrl + Shift + R
    - Microsoft Edge 로 ALB DNS 접속(Chrome 사용시 정상테스트 불가)

***

## DB(mysql) 서버접속

- [ ] DB : 서버에 DB 직접설치
    - 접속 : mysql -h [hostname] -P 3306 -u [ID] -p

    ```
    # MySQL 공식 리포지토리를 시스템에 추가
    # Amazon Linux 2023 사용 시 el9 버전 레포지토리를 사용
    sudo dnf install https://dev.mysql.com/get/mysql80-community-release-el9-1.noarch.rpm

    sudo dnf update -y

    # 서버설치시
    sudo dnf install mysql-community-server

    # 클라이언트만 설치시
    sudo dnf install mysql-community-client

    ```