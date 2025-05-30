# [CCCR] 프라이빗 오픈 클라우드를 위한 오픈스택 구축 및 운영 (1)

<br>
<br>


1. 파일 > 가상시스템 가져오기 선택하여 vm 생성
![](https://velog.velcdn.com/images/jupiter-j/post/239fc948-33ec-43f3-8e46-d6eabbef6788/image.png)

2. 네트워크 설정 , 이름 설정, 공유폴더 설정 
> 파일>도구>네트워크 관리자
hostonly 네트워크 2개 확인
192.68.56.1/24
192.168.57.1/24


![](https://velog.velcdn.com/images/jupiter-j/post/4e98cd7b-1596-48aa-9c32-e2d6a7196780/image.png)

3. 네트워크 어뎁터 2, 3은 hostonly로 설정, 만들어둔 네트워크 어뎁터 이름을 선택하기

![](https://velog.velcdn.com/images/jupiter-j/post/31171e2d-1ddd-47f1-86e9-d5b3e838a3e3/image.png)

4. putty 접속
> 사용자 vagrant / vagrant
ssh vagrant@192.18.56.200

![](https://velog.velcdn.com/images/jupiter-j/post/8a823492-ac65-474f-b40f-7a03842e89a1/image.png)


> ## 오픈스택 설치 
해당 실습은 이미 koll-ansible이 설치되었다는 가정하에 진행함으로 환경이 없다면 아래 링크로 설치
https://docs.openstack.org/kolla-ansible/2024.1/user/quickstart.html

### 파이썬 가상환경 활성화
```

(os-venv) vagrant@openstack-aio:~$ tail ~/.bashrc
# this, if it's already enabled in /etc/bash.bashrc and /etc/profile
# sources /etc/bash.bashrc).
if ! shopt -oq posix; then
  if [ -f /usr/share/bash-completion/bash_completion ]; then
    . /usr/share/bash-completion/bash_completion
  elif [ -f /etc/bash_completion ]; then
    . /etc/bash_completion
  fi
fi
source os-venv/bin/activate ## 추가

```

<br>


### kolla-ansible 깃허브 
https://opendev.org/openstack/kolla-ansible

![](https://velog.velcdn.com/images/jupiter-j/post/5a20944f-5b34-4aac-b0ee-507a1c4efee0/image.png)

<br>

## 도메인
오픈스택에는 물리적인 데이터들을 나누는 단위로 리전을 사용하여 논리적인 구분단위로 도메인과 프로젝트라는 개념을 사용한다. 

도메인은 하나의 리전에 포함되는 가장 큰 논리적 단위이다. 도메인 안에는 프로젝트와 사용자 및 그룹이 포함되며 이를 통해 리소스에 대한 격리 및 접근제어 역할을 수행한다. 

관리자 권한의 계정으로만 작업 가능함.
![](https://velog.velcdn.com/images/jupiter-j/post/1b86bd26-f5a4-40f2-8d99-be95637d6519/image.png)
- 도메인 생성: `openstack domain create new-domain`

![](https://velog.velcdn.com/images/jupiter-j/post/fed9d938-c8c1-479a-8076-6217c69759b2/image.png)

- 도메인 삭제
최상위 단위이기 때문에 비활성화 후 삭제를 할수있다. 

![](https://velog.velcdn.com/images/jupiter-j/post/19410a36-1f24-4dde-9824-9eecaa08bda5/image.png)
* 비활성화: `openstack domain set --disable new-domain`
* 삭제: `openstack domain delete new-domain`
```
(os-venv) vagrant@openstack-aio:~$ openstack domain set --disable new-domain
(os-venv) vagrant@openstack-aio:~$ openstack domain delete new-domain
```

### 대시보드 접속
![](https://velog.velcdn.com/images/jupiter-j/post/35a9ba88-287b-438e-a1be-7073a539e17d/image.png)
* pw/id를 모를경우
kolla ansible기준 /etc/kolla/admin-openrc.sh 파일에 해당 값이 존재함 확인하기 
![](https://velog.velcdn.com/images/jupiter-j/post/b848e0e5-3402-4137-8ace-739d6f53985b/image.png)


* 프로젝트 확인시 왼쪽 상단 잘보기 
![](https://velog.velcdn.com/images/jupiter-j/post/f96264bb-2d7e-4347-baeb-045111f8b430/image.png)


![](https://velog.velcdn.com/images/jupiter-j/post/2962fe29-e5ad-4dd9-aa83-4b72c4a9a014/image.png)
인증> 프로젝트의 위치에 있는 값 2개는 오픈스택 어느버전이든 default로 설치되어있음. 오픈스택 서비스가 동작하기 위해 사용됨

### 프로젝트 생성 - GUI

![](https://velog.velcdn.com/images/jupiter-j/post/3fb92396-a752-4839-8b3e-ec034d06ac96/image.png)

![](https://velog.velcdn.com/images/jupiter-j/post/73429a17-6227-4a8b-91fa-be20f0a5bade/image.png)

![](https://velog.velcdn.com/images/jupiter-j/post/ac6ed057-6e56-4364-aa2c-423fc82dfb8c/image.png)
멤버관리 > 프로젝트 편집: 유저 권한/그룹권한을 부여할 수 있음. 
멤버관리 > Quotas : cpu,memory등 수정 가능

도메인은 활성화 상태에서 바로 삭제할수 없으나 프로젝트는 활성화 상태에서도 바로 삭제가 가능하다. 

### 프로젝트 생성 - CLI
```

(os-venv) vagrant@openstack-aio:/etc/kolla$ openstack project list
+----------------------------------+---------+
| ID                               | Name    |
+----------------------------------+---------+
| 00855a5cafa646478a16f350df1f00f6 | admin   |
| e1cbe4b32b1744bf8f78d964db3215fc | service |
+----------------------------------+---------+
(os-venv) vagrant@openstack-aio:/etc/kolla$

```
- 서브 프로젝트 생성: `openstack project create --parent new-project sub-project`
프로젝트 명은 중복으로 만들 수 없다. 
삭제시 프로젝트가 서브와 중첩되어있기 때문에 하위 프로젝트인 서브 프로젝트부터 삭제해야한다. 

![](https://velog.velcdn.com/images/jupiter-j/post/8df4b731-463c-4804-85e4-46cd30ce8b59/image.png)


### 할당량 관리 
관리>시스템>기본 : 앞으로 생성할 모든 프로젝트의 기본값을 수정
인증>멤버관리 > Quotas : 단일 프로젝트 기본값 수정 
![](https://velog.velcdn.com/images/jupiter-j/post/eea358ac-e807-4a53-a56e-7c7b3ca6ad93/image.png)![](https://velog.velcdn.com/images/jupiter-j/post/1e397de4-6ca2-407a-93ef-38501a26b258/image.png)
--옵션을 사용하여 compute, volume등을 확인 가능 


<br>

# 실습
> 
1. 도메인 생성 : practice-domain
2. 도메인 안에 프로젝트 생성 : upper-project
3. 리소스 기본 할당량을 조정 및 확인
4. 프로젝트 중첩해서 생성 : lower-project
5. 리소스 할당량 조정 및 확인: lower-project의 리소스 할당량 변경
5. 프로젝트 및 도메인 삭제
lower-project 삭제 > upeer-project 삭제 > practice-doamin 비활성화 > 삭제 

```

(os-venv) vagrant@openstack-aio:/etc/kolla$ openstack domain create practice-domain
+-------------+----------------------------------+
| Field       | Value                            |
+-------------+----------------------------------+
| description |                                  |
| enabled     | True                             |
| id          | 200f3f3aeffc4ed7870b10a9250d0cf5 |
| name        | practice-domain                  |
| options     | {}                               |
| tags        | []                               |
+-------------+----------------------------------+
(os-venv) vagrant@openstack-aio:/etc/kolla$ openstack domain list
+----------------------------------+------------------+---------+--------------------+
| ID                               | Name             | Enabled | Description        |
+----------------------------------+------------------+---------+--------------------+
| 200f3f3aeffc4ed7870b10a9250d0cf5 | practice-domain  | True    |                    |
| 678ffb142e184d89bc5aaeebccbc86cd | heat_user_domain | True    |                    |
| default                          | Default          | True    | The default domain |
+----------------------------------+------------------+---------+--------------------+
(os-venv) vagrant@openstack-aio:/etc/kolla$ openstack project create --domain practice-domain upper-project
+-------------+----------------------------------+
| Field       | Value                            |
+-------------+----------------------------------+
| description |                                  |
| domain_id   | 200f3f3aeffc4ed7870b10a9250d0cf5 |
| enabled     | True                             |
| id          | 09a54747b7ba4ec18d92bc440c3eca63 |
| is_domain   | False                            |
| name        | upper-project                    |
| options     | {}                               |
| parent_id   | 200f3f3aeffc4ed7870b10a9250d0cf5 |
| tags        | []                               |
+-------------+----------------------------------+
(os-venv) vagrant@openstack-aio:/etc/kolla$ openstack project list
+----------------------------------+---------------+
| ID                               | Name          |
+----------------------------------+---------------+
| 00855a5cafa646478a16f350df1f00f6 | admin         |
| 09a54747b7ba4ec18d92bc440c3eca63 | upper-project |
| e1cbe4b32b1744bf8f78d964db3215fc | service       |
+----------------------------------+---------------+
(os-venv) vagrant@openstack-aio:/etc/kolla$ openstack project create lower-project --domain practice-domain --parent 09a54747b7ba4ec18d92bc440c3eca63
+-------------+----------------------------------+
| Field       | Value                            |
+-------------+----------------------------------+
| description |                                  |
| domain_id   | 200f3f3aeffc4ed7870b10a9250d0cf5 |
| enabled     | True                             |
| id          | e1279735f0ae459b93ff5323526c87cc |
| is_domain   | False                            |
| name        | lower-project                    |
| options     | {}                               |
| parent_id   | 09a54747b7ba4ec18d92bc440c3eca63 |
| tags        | []                               |
+-------------+----------------------------------+
(os-venv) vagrant@openstack-aio:/etc/kolla$ openstack project list
+----------------------------------+---------------+
| ID                               | Name          |
+----------------------------------+---------------+
| 00855a5cafa646478a16f350df1f00f6 | admin         |
| 09a54747b7ba4ec18d92bc440c3eca63 | upper-project |
| e1279735f0ae459b93ff5323526c87cc | lower-project |
| e1cbe4b32b1744bf8f78d964db3215fc | service       |
+----------------------------------+---------------+
(os-venv) vagrant@openstack-aio:/etc/kolla$

## 할당량

(os-venv) vagrant@openstack-aio:/etc/kolla$ openstack quota show --compute lower-project
+----------------------+-------+
| Resource             | Limit |
+----------------------+-------+
| cores                |    20 |
| instances            |    10 |
| ram                  | 51200 |
| fixed-ips            |    -1 |
| injected-file-size   | 10240 |
| injected-path-size   |   255 |
| injected-files       |     5 |
| key-pairs            |   100 |
| properties           |   128 |
| server-groups        |    10 |
| server-group-members |    10 |
| floating-ips         |    -1 |
| secgroup-rules       |    -1 |
| secgroups            |    -1 |
+----------------------+-------+
(os-venv) vagrant@openstack-aio:/etc/kolla$ openstack quota show --compute upper-project
+----------------------+-------+
| Resource             | Limit |
+----------------------+-------+
| cores                |    20 |
| instances            |    10 |
| ram                  | 51200 |
| fixed-ips            |    -1 |
| injected-file-size   | 10240 |
| injected-path-size   |   255 |
| injected-files       |     5 |
| key-pairs            |   100 |
| properties           |   128 |
| server-groups        |    10 |
| server-group-members |    10 |
| floating-ips         |    -1 |
| secgroup-rules       |    -1 |
| secgroups            |    -1 |
+----------------------+-------+

(os-venv) vagrant@openstack-aio:/etc/kolla$ openstack quota set --ram 512 lower-project --force
(os-venv) vagrant@openstack-aio:/etc/kolla$ openstack quota show --compute lower-project
+----------------------+-------+
| Resource             | Limit |
+----------------------+-------+
| cores                |    40 |
| instances            |    20 |
| ram                  |   512 |
| fixed-ips            |    -1 |
| injected-file-size   | 10240 |
| injected-path-size   |   255 |
| injected-files       |     5 |
| key-pairs            |   100 |
| properties           |   128 |
| server-groups        |    10 |
| server-group-members |    10 |
| floating-ips         |    -1 |
| secgroup-rules       |    -1 |
| secgroups            |    -1 |
+----------------------+-------+
## 삭제

(os-venv) vagrant@openstack-aio:/etc/kolla$ openstack project delete lower-project
(os-venv) vagrant@openstack-aio:/etc/kolla$ openstack project delete upper-project
(os-venv) vagrant@openstack-aio:/etc/kolla$
(os-venv) vagrant@openstack-aio:/etc/kolla$ openstack domain set --disable practice-domain
(os-venv) vagrant@openstack-aio:/etc/kolla$
(os-venv) vagrant@openstack-aio:/etc/kolla$ openstack project list
+----------------------------------+---------+
| ID                               | Name    |
+----------------------------------+---------+
| 00855a5cafa646478a16f350df1f00f6 | admin   |
| e1cbe4b32b1744bf8f78d964db3215fc | service |
+----------------------------------+---------+
(os-venv) vagrant@openstack-aio:/etc/kolla$ openstack domain list
+----------------------------------+------------------+---------+--------------------+
| ID                               | Name             | Enabled | Description        |
+----------------------------------+------------------+---------+--------------------+
| 200f3f3aeffc4ed7870b10a9250d0cf5 | practice-domain  | False   |                    |
| 678ffb142e184d89bc5aaeebccbc86cd | heat_user_domain | True    |                    |
| default                          | Default          | True    | The default domain |
+----------------------------------+------------------+---------+--------------------+
(os-venv) vagrant@openstack-aio:/etc/kolla$ openstack domain delete practice-domain
(os-venv) vagrant@openstack-aio:/etc/kolla$ openstack domain list
+----------------------------------+------------------+---------+--------------------+
| ID                               | Name             | Enabled | Description        |
+----------------------------------+------------------+---------+--------------------+
| 678ffb142e184d89bc5aaeebccbc86cd | heat_user_domain | True    |                    |
| default                          | Default          | True    | The default domain |
+----------------------------------+------------------+---------+--------------------+
(os-venv) vagrant@openstack-aio:/etc/kolla$

```
>도메인 생성 : practice-domain
![](https://velog.velcdn.com/images/jupiter-j/post/d7ccd1d6-9fee-42fd-acfc-8fea600e0146/image.png)
도메인 안에 프로젝트 생성 : upper-project
![](https://velog.velcdn.com/images/jupiter-j/post/4d791650-4740-487b-a25d-54e87d2d9bd4/image.png)
프로젝트 중첩해서 생성 : lower-project
![](https://velog.velcdn.com/images/jupiter-j/post/db5e6f98-e040-4c62-b799-1ba039a65d24/image.png)
리소스 기본 할당량을 조정 및 확인
![](https://velog.velcdn.com/images/jupiter-j/post/28c94ff6-08c5-4997-8ac6-91795ec534b8/image.png)
프로젝트 할당량 변경
![](https://velog.velcdn.com/images/jupiter-j/post/d01c918f-8536-4cf9-8ba6-953b3c740a81/image.png)
도메인, 프로젝트 삭제
![](https://velog.velcdn.com/images/jupiter-j/post/6d44a4c3-954e-4610-a367-9997401f03c2/image.png)



