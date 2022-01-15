### 사전 준비

1. ntp 설정
2. 최대 File Descriptors 숫자 설정
    1. `ulimit -n` **명령어로 값 확인 만약 1024 값이면 변경 필요.
    2. /etc/security/limits.conf 파일을 열고 다음 내용을 추가해 주세요.
        
        ```bash
        sudo /etc/security/limits.conf
        
        root soft nofile 65536
        root hard nofile 65536
        * soft nofile 65536
        * hard nofile 65536
        ```
        
    3. 저장 후 시스템을 리부팅 시켜줍니다
3. 네트워크 커널 매개 변수 최적화
    1. Fluentd 인스턴스가 많은 고부하의 환경이라면 네트워크 커널 매개 변수 최적화가 필요합니다. 
        
        다음 구성을 `/etc/sysctl.conf`에 추가합니다.
        
        ```bash
        net.core.somaxconn = 1024
        net.core.netdev_max_backlog = 5000
        net.core.rmem_max = 16777216
        net.core.wmem_max = 16777216
        net.ipv4.tcp_wmem = 4096 12582912 16777216
        net.ipv4.tcp_rmem = 4096 12582912 16777216
        net.ipv4.tcp_max_syn_backlog = 8096
        net.ipv4.tcp_slow_start_after_idle = 0
        net.ipv4.tcp_tw_reuse = 1
        net.ipv4.ip_local_port_range = 10240 65535
        ```
        
    2. 만약 변경사항을 적용했다면 sysctl -p 명령을 사용하거나 노드를 재부팅해야 합니다.
    

### 설정

1. `/etc/td-agent/td-agent.conf` 파일 설정
    
    ```bash
    sudo vim /etc/td-agent/td-agent.conf
    ```
    

### 설치

1. curl 등록
    
    ```bash
    curl -L https://toolbelt.treasuredata.com/sh/install-ubuntu-focal-td-agnet4.sh | sh
    ```
    
    [https://docs.fluentbit.io/manual/installation/linux/ubuntu](https://docs.fluentbit.io/manual/installation/linux/ubuntu) 참고
    

### 실행

1. service 시작
    
    ```bash
    sudo systemctl start td-agent.service
    ```
    
2. 상태 확인
    
    ```bash
    sudo systemctl status td-agent.service
    ```
    
3. reboot 시 자동으로 실행 설정
    
    ```bash
    sudo systemctl enable td-agent.service
    ```
    
4. Logstash service 및 관련 process 모두 중지
    
    ```bash
    sudo systemctl kill td-agent.service
    ```
