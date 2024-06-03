## ID/PW를 사용하여 리눅스 인스턴스에 접근하기
- [ ] 참고 URL : https://catalog.workshops.aws/general-immersionday/ko-KR/basic-modules/10-ec2/ec2-linux/3-ec2-1
- [ ] Session Manager는 대화형 원클릭 브라우저 기반 셸 또는 AWS CLI를 통해 Amazon EC2 인스턴스를 관리할 수 있는 AWS Systems Manager의 기능입니다. Session Manager를 사용하여 계정의 인스턴스에 세션을 시작할 수 있습니다. 세션이 시작된 후, 다른 연결 유형을 통해 bash 명령을 실행할 수 있습니다.


```

#!/bin/bash

# ec2-user 사용자 암호 설정
echo "Setting password for ec2-user..."
echo "ec2-user:mypassword" | sudo chpasswd

# SSH 패스워드 인증 허용 설정
echo "Allowing password authentication for SSH..."
sudo sed -i 's/PasswordAuthentication no/PasswordAuthentication yes/' /etc/ssh/sshd_config

# SSH 서비스 재시작
echo "Restarting SSH service..."
sudo systemctl restart sshd


```