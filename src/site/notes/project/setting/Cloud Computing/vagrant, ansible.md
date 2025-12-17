---
dg-publish: true
---

## **1. 실습 개요 및 환경 구성 Rationale**

본 과제는 **HTCondor를 이용한 분산 작업 환경을 구축하고, 이 과정을 Vagrant와 Ansible을 통해 자동화**하는 것을 목표로 한다.

초기에는 Windows 환경의 **VirtualBox**를 이용하여 1개의 마스터 노드와 2개의 슬레이브 노드를 수동으로 구성하고, 간단한 계산 및 웹 크롤링 작업을 분산 처리하는 실습을 진행했다. 이 수동 구성 과정을 자동화하기 위해 **Vagrant**와 **Ansible** 도입을 결정했다.

그러나 조사 결과, 자동화의 핵심 도구인 **Ansible의 제어 노드는 Windows 환경을 지원하지 않는 제약**이 있었다. 이를 해결하기 위해 **WSL(Windows Subsystem for Linux)**을 사용하여 Linux 환경을 구축하고, 그 안에서 자동화 스크립트를 실행하기로 했다. (Production 환경에서는 권장되지 않는 방식이지만, 학습 목적이므로 채택했다.)

최종적으로 다음과 같은 아키텍처를 채택했으며, 그 이유는 아래와 같다.

- **Host OS (VirtualBox 구동)**: **Windows**
    
    - 이미 HTCondor 초기 테스트를 위해 VM들이 설치되어 있었고, 문제 발생 시 GUI를 통해 상태를 직접 확인하고 디버깅하기 용이하여 그대로 사용했다.
        
- **제어 환경 (Vagrant, Ansible 구동)**: **WSL (Ubuntu)**
    
    - **Ansible**은 Linux 환경이 필수적이므로 WSL에 설치했다.
        
    - **Vagrant** 또한 WSL에 함께 설치했다. Ansible이 VM에 접속하려면 Vagrant가 생성한 SSH Key의 경로를 참조해야 하는데, 만약 Vagrant가 Windows에 있고 Ansible이 WSL에 있다면 경로 문제나 권한 문제가 발생할 확률이 매우 높기 때문이었다.
        
    - WSL의 Vagrant가 Windows의 VirtualBox를 제어할 수 있게 해주는 **전용 플러그인의 존재를 확인하고 테스트**를 마친 뒤, 위와 같은 구조를 최종적으로 확정했다.
        

---

### **2. HashiCorp GPG 키 및 Vagrant 저장소 등록**

WSL(Ubuntu) 환경에서 HashiCorp GPG 키를 `/usr/share/keyrings/` 경로에 저장하고, apt 저장소를 시스템에 등록하였습니다. 이후 `sudo apt update` 및 `sudo apt install -y vagrant` 명령어를 통해 최신 버전의 Vagrant를 설치하였습니다.

![1.png](/img/user/Image/1.png)

---

### **3. Vagrant 설치 완료 및 버전 확인**

Vagrant 설치 후 `vagrant --version` 명령어를 통해 Vagrant 2.4.5가 정상적으로 설치되었음을 확인하였습니다.

![2.vagrant버전확인.png](/img/user/Image/2.vagrant%EB%B2%84%EC%A0%84%ED%99%95%EC%9D%B8.png)

---

### **4. WSL 환경 대비 설정 및 Vagrant 프로젝트 디렉토리 생성 및 초기화**

Windows 환경에 설치된 VirtualBox를 사용하기 위해 `VAGRANT_WSL_ENABLE_WINDOWS_ACCESS` 환경변수를 설정하고, `virtualbox_WSL2` 플러그인을 설치하였습니다. `vrepo` 디렉토리를 생성한 후 그 안에서 `vagrant init` 명령어를 실행하여 초기 Vagrantfile을 생성하였습니다. 생성된 기본 Vagrantfile을 `cat` 명령어를 통해 확인하였습니다.

![3.png](/img/user/Image/3.png)

---

### **5. CentOS 7 박스 기반 초기 VM 생성 및 설정**

기존 Vagrantfile을 삭제한 후 `vagrant init centos/7` 명령어로 다시 초기화하여 CentOS 7 박스를 사용하도록 설정하였습니다. 이후 `vagrant up` 명령어를 통해 가상머신을 부팅하면서, CentOS 7 박스를 다운로드하고 VirtualBox를 통해 기본 VM을 생성하였습니다.

![4.png](/img/user/Image/4.png)

![5.png](/img/user/Image/5.png)

---

### **6. 마스터 노드 접속 및 네트워크 확인**

`vagrant ssh master` 명령어로 마스터 노드에 접속한 뒤 `hostname`, `ip br addr` 명령어를 통해 호스트 이름과 네트워크 인터페이스 설정이 올바르게 적용되었는지 확인하였습니다. eth1에 설정된 192.168.56.101 주소가 출력되었습니다.

![6.png](/img/user/Image/6.png)

---

### **7. 워커1 노드 접속 및 네트워크 확인**

workerl.cloud.org에 접속하여 `hostname`, `ip br addr` 명령어로 네트워크 상태를 확인한 결과, eth1에 192.168.56.102가 바인딩된 것을 확인하였습니다.

![7.png](/img/user/Image/7.png)

---

### **8. 워커2 노드 접속 및 네트워크 확인**

worker2.cloud.org에 접속하여 동일하게 IP가 192.168.56.103으로 설정되었는지 확인하였습니다. 이를 통해 마스터-워커 간 사설 네트워크 구성이 정상적으로 완료되었음을 확인할 수 있었습니다.

![8.png](/img/user/Image/8.png)

---

### **9. Ansible 설치**

WSL 환경에서 마스터 노드로 사용할 머신에 Ansible을 설치하는 과정입니다. `sudo apt install -y ansible` 명령어를 통해 필요한 Python 관련 패키지들과 함께 설치하였으며, HTCondor 자동 배포를 위한 준비를 마쳤습니다.

![9.png](/img/user/Image/9.png)

---

### **10. Ansible 인벤토리 파일 설정**

`/etc/ansible/hosts` 파일에 `[cluster]`, `[master]`, `[worker]` 그룹을 정의하고, 각 노드별로 SSH 접속이 가능하도록 `ansible_user` 및 `ansible_ssh_private_key_file` 경로를 지정하였습니다. 이는 이후 Ansible 플레이북을 통해 HTCondor를 설치하고 설정하는 기반이 되었습니다.

![10.png](/img/user/Image/10.png)

---

### **11. 마스터와 워커 노드 간 네트워크 연결 및 확인**

WSL 환경에서 각 노드에 대해 `ping` 명령어를 수행하여 사설 IP 주소 기반 통신이 가능한지 확인하였습니다. 초기에는 연결되지 않았지만, 이후 master.cloud.org, workerl.cloud.org, worker2.cloud.org 간 ICMP 통신이 정상적으로 이루어지는 것을 확인하였습니다.

![11.png](/img/user/Image/11.png)

---

### **12. Ansible ping 모듈 테스트**

Ansible의 `ping` 모듈을 사용하여 `cluster` 그룹에 속한 모든 노드에 명령을 보내고, 정상적으로 `pong` 응답을 받은 것을 확인하였습니다. 이는 Ansible이 SSH를 통해 각 노드와 통신할 수 있음을 의미합니다.

![12.png](/img/user/Image/12.png)

---

### **13. CentOS 저장소 설정용 플레이북 작성**

`Centos-Repo.yaml` 파일을 생성하여, CentOS 7의 `base`, `updates`, `extras` 저장소를 kakao 미러 서버로 등록하였습니다. 이 저장소는 이후 yum 패키지 설치 시 사용됩니다.

![13.png](/img/user/Image/13.png)

---

### **14. HTCondor 설치용 Ansible 플레이북 작성 (1)**

`htcondor.yaml` 플레이북 파일의 일부입니다. SELinux를 비활성화하고, Firewall을 중지한 뒤, `/etc/hosts` 파일을 각 노드에 배포하는 작업부터 Condor 패키지를 설치하는 작업까지 순차적으로 정의되어 있습니다.

![14.png](/img/user/Image/14.png)

---

### **15. HTCondor 설치용 Ansible 플레이북 작성 (2)**

HTCondor의 마스터 노드 및 워커 노드에 대해 각각 설정 파일을 배포하고 서비스를 활성화하는 작업이 포함되어 있습니다. `DAEMON_LIST`, `NETWORK_INTERFACE` 등의 설정을 통해 클러스터 통신이 원활하게 이루어질 수 있도록 구성하였습니다.

![15.png](/img/user/Image/15.png)

---

### **16. 저장소 설정 플레이북 실행 결과**

`ansible-playbook Centos-Repo.yaml` 실행 결과, 3개의 노드 모두에서 저장소 파일이 정상적으로 배포되었음을 확인할 수 있습니다.

![16.png](/img/user/Image/16.png)

![17.png](/img/user/Image/17.png)

---

### **17. HTCondor 클러스터 상태 확인**

마스터 노드에 SSH 접속한 뒤 `condor_status` 명령어를 통해 클러스터에 등록된 워커 노드의 상태를 확인하였습니다. worker1.cloud.org와 worker2.cloud.org 모두 `Unclaimed Idle` 상태로, 작업 수신 준비가 완료된 상태임을 확인할 수 있습니다.

![18.png](/img/user/Image/18.png)

---

### **18. Python3 및 관련 라이브러리 설치**

HTCondor에서 Python 기반 분산 작업 실행을 위해 Python3, pip 및 필수 라이브러리(packaging, setuptools, wheel)를 설치하는 Ansible 플레이북을 실행하였습니다. 모든 노드에서 관련 모듈이 정상적으로 설치되었습니다.

![19.png](/img/user/Image/19.png)

![20.png](/img/user/Image/20.png)

---

### **19. 웹 크롤링 Condor 작업 실행 구성**

HTCondor를 이용해 병렬로 뉴스 기사 HTML을 크롤링하기 위한 작업 구성을 작성한 화면입니다.

- **crawler.py**: 입력된 URL에서 HTML 본문 텍스트를 추출하여 .txt 파일로 저장하는 크롤링 스크립트입니다. BeautifulSoup을 이용해 HTML 태그를 제거하고 순수 텍스트만 저장합니다.
    
- **crawler.jds**: Condor 제출 스크립트로, 각 작업은 crawler.sh를 실행하며 하나의 URL을 크롤링하도록 구성되어 있습니다. 작업 ID에 따라 input/output/log 파일 경로가 자동 지정됩니다. CPU 1개, 메모리 512MB, 디스크 100MB 자원이 요청됩니다.
    
- **crawler.sh**: 작업 실행용 Bash 스크립트로, Condor에서 전달받은 ID를 바탕으로 crawler.py를 실행합니다. 입력은 `url_${ID}.txt`, 출력은 `article_${ID}.txt`로 지정됩니다.
    

---

### **20. 실행결과**

![Pasted image 20251007174242.png](/img/user/Image/Pasted%20image%2020251007174242.png)