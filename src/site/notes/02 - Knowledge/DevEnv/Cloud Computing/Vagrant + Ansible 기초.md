---
{"dg-publish":true,"permalink":"/02 - Knowledge/DevEnv/Cloud Computing/Vagrant + Ansible 기초/","tags":["type/study","context/studies","theme/devenv","status/completed"]}
---

# Vagrant + Ansible 기초

> Vagrant로 VM을 자동 프로비저닝하고, Ansible로 소프트웨어 설치·설정을 자동화하는 조합

## Vagrant 설치 (Ubuntu/WSL)

```bash
# HashiCorp GPG 키 등록
wget -O- https://apt.releases.hashicorp.com/gpg | sudo gpg --dearmor -o /usr/share/keyrings/hashicorp-archive-keyring.gpg
echo "deb [signed-by=/usr/share/keyrings/hashicorp-archive-keyring.gpg] https://apt.releases.hashicorp.com $(lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/hashicorp.list

# 설치
sudo apt update && sudo apt install -y vagrant
vagrant --version
```

## Ansible 설치

```bash
sudo apt install -y ansible
```

## WSL에서 Windows VirtualBox 연동

Ansible은 Windows를 제어 노드로 지원하지 않으므로 WSL에서 실행해야 한다.

```bash
# WSL이 Windows VirtualBox를 제어할 수 있도록 설정
export VAGRANT_WSL_ENABLE_WINDOWS_ACCESS="1"
export PATH="$PATH:/mnt/c/Program Files/Oracle/VirtualBox"

# VirtualBox WSL2 플러그인 설치
vagrant plugin install virtualbox_WSL2
```

> Vagrant와 Ansible을 모두 WSL에 두어야 SSH 키 경로 문제가 없다.

## Ansible 인벤토리 설정

```ini
# /etc/ansible/hosts
[cluster]
master.cloud.org
worker1.cloud.org
worker2.cloud.org

[master]
master.cloud.org ansible_user=vagrant ansible_ssh_private_key_file=~/.vagrant.d/...

[worker]
worker1.cloud.org ansible_user=vagrant ansible_ssh_private_key_file=~/.vagrant.d/...
worker2.cloud.org ansible_user=vagrant ansible_ssh_private_key_file=~/.vagrant.d/...
```

## Ansible 기본 명령

```bash
# 연결 테스트
ansible cluster -m ping

# 플레이북 실행
ansible-playbook playbook.yml
```

## 플레이북 기본 구조

```yaml
---
- hosts: all
  become: yes
  tasks:
    - name: 패키지 설치
      yum:
        name: htcondor
        state: present

- hosts: master
  tasks:
    - name: 마스터 설정 배포
      copy:
        src: master.conf
        dest: /etc/condor/condor_config.local
```

## 관련 개념

- [[02 - Knowledge/DevEnv/Cloud Computing/index\|index]] — 클라우드 컴퓨팅 DevEnv 개요
