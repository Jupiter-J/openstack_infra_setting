# [CCCR] 프라이빗 오픈 클라우드를 위한 오픈스택 구축 및 운영 (2)

<br>
<br>

# 개념 
## 가상화
 1. 개념: 물리적인 리소스를 논리적으로 추상화
* 스토리지 - RAID / LVM, Thin Provisioning, SDS
* 네트워크 - VLAN / Bonding, SDN / NFV
* 시스템 - 가상머신(VM), 컨테이너

## 클라우드
1. 개념: 네트워크(인터넷)를 통해 언제/어디서든 접근 가능한 온디맨드 방식으로 서비스를 제공하는 형태
2. 특징: 비용/ 시간/ 가시성/ 사전지식
3. 종류: 퍼블릭/ 프라이빗/ 하이브리드 클라우드의 종류로 나뉜다. 

## 오픈스택
1. 개념: 프라이빗 클라우드를 구성하고 우영하는 도구 중 하나 
2. 배포방식: 구성 서비스를 프로세스 형태냐 혹은 컨테이너 형태인가 

<br>

### 오픈스택 아키텍쳐
* 컨트롤러 노드 - 오픈스택 환경 구성 및 관리
-> 클러스터 구성후 사용(3대 이상)

* 컴퓨트 노드 - 생성하는 인스턴스에 리소스 제공
* 네트워크 노드 - 외부 통신과 관련한 부분 담당, 네트워크 구성 
* 스토리지 노드(HCI - 스토리지 서비스를 통한 스토리지 제공

### 오픈스택 컴포넌트
keystone(ID관리), horizon(대시보드), nova(컴퓨트-인스턴스 스케줄링), glance(이미지 관리), neutron(네트워크), cinder(블록스토리지), manila(공유 스토리지), heat(오케스트레이션-배포관리), ceilometer/gnocchi/aodh(사용량 측정)  

### 대시보드
* 도메인 - 논리적인 단위 중 최상위 개체(프로젝트 및 사용자/그룹 포함)
* 프로젝트 - 리소스들이 격리, 할당량 제한, 접근제어
* 사용자(그룹) - 접근제어를 위한 대상  
* 역할(정책) - 개체 별 동작에 대한 가능 여부 결정 


### 역할
* 역할 확인 : `openstack role list`
![](https://velog.velcdn.com/images/jupiter-j/post/a2370148-d29a-404c-9632-b2690ddf2b31/image.png)

* 역할 할당 목록: `openstack role assignment list --names`
![](https://velog.velcdn.com/images/jupiter-j/post/4ccf5d32-bd8e-4a60-bfff-7c528a0f34e9/image.png)
* 역할 생성 : `openstack role add --project demo-project --user demo-user member` 
![](https://velog.velcdn.com/images/jupiter-j/post/0baccc53-8360-411d-8dc2-f5ef3cbb4d52/image.png)
* 역할 삭제 : `openstack role remove --project demo-project --user demo-user member`
![](https://velog.velcdn.com/images/jupiter-j/post/74aaae1e-c52e-4f57-9b94-750a5608c75b/image.png)


## 인스턴스 생성

- 메모리 고려 하여 컨테이너 stop : `sudo docker stop $(sudo docker ps | grep -e heat -e gnocchi -e aodh -e ceilo -e octavia | awk '{print $1}')`



```

(os-venv) vagrant@openstack-aio:/etc/kolla$ openstack role create vm-list-viewer
+-------------+----------------------------------+
| Field       | Value                            |
+-------------+----------------------------------+
| description | None                             |
| domain_id   | None                             |
| id          | a6b6e454884d46dbbe03a0a3a191df92 |
| name        | vm-list-viewer                   |
| options     | {}                               |
+-------------+----------------------------------+
(os-venv) vagrant@openstack-aio:/etc/kolla$ openstack role add --user test-user --project demo-project vm-list-viewer
No user with a name or ID of 'test-user' exists.
(os-venv) vagrant@openstack-aio:/etc/kolla$ openstack role assignment list --names --user -test-user
usage: openstack role assignment list [-h] [-f {csv,json,table,value,yaml}] [-c COLUMN] [--quote {all,minimal,none,nonnumeric}]
                                      [--noindent] [--max-width <integer>] [--fit-width] [--print-empty]
                                      [--sort-column SORT_COLUMN] [--sort-ascending | --sort-descending] [--effective]
                                      [--role <role>] [--role-domain <role-domain>] [--names] [--user <user>]
                                      [--user-domain <user-domain>] [--group <group>] [--group-domain <group-domain>]
                                      [--domain <domain> | --project <project> | --system <system>]
                                      [--project-domain <project-domain>] [--inherited] [--auth-user] [--auth-project]
openstack role assignment list: error: argument --user: expected one argument
(os-venv) vagrant@openstack-aio:/etc/kolla$ openstack role assignment list --names --user test-user
No user with a name or ID of 'test-user' exists.
(os-venv) vagrant@openstack-aio:/etc/kolla$ openstack user create --password 123 test-user
+---------------------+----------------------------------+
| Field               | Value                            |
+---------------------+----------------------------------+
| domain_id           | default                          |
| enabled             | True                             |
| id                  | d3feb4147e2247939ba5c87aa692b2e4 |
| name                | test-user                        |
| options             | {}                               |
| password_expires_at | None                             |
+---------------------+----------------------------------+
(os-venv) vagrant@openstack-aio:/etc/kolla$ openstack role assignment list --names --user test-user

(os-venv) vagrant@openstack-aio:/etc/kolla$ openstack role assignment list --names --user test-user

(os-venv) vagrant@openstack-aio:/etc/kolla$
(os-venv) vagrant@openstack-aio:/etc/kolla$
(os-venv) vagrant@openstack-aio:/etc/kolla$ ㅣㄴ
-bash: ㅣㄴ: command not found
(os-venv) vagrant@openstack-aio:/etc/kolla$ ls
admin-openrc.sh          haproxy                    neutron-server          redis-sentinel
aodh-api                 heat-api                   nova-api                swift
aodh-evaluator           heat-api-cfn               nova-api-bootstrap      swift-account-auditor
aodh-listener            heat-engine                nova-cell-bootstrap     swift-account-reaper
aodh-notifier            horizon                    nova-compute            swift-account-replication-server
ceilometer-central       iscsid                     nova-conductor          swift-account-replicator
ceilometer-compute       keepalived                 nova-libvirt            swift-account-server
ceilometer-notification  keystone                   nova-novncproxy         swift-container-auditor
cinder-api               keystone-fernet            nova-scheduler          swift-container-replication-server
cinder-backup            keystone-ssh               nova-ssh                swift-container-replicator
cinder-scheduler         kolla-toolbox              octavia-api             swift-container-server
cinder-volume            manila-api                 octavia-certificates    swift-container-updater
clouds.yaml              manila-data                octavia-health-manager  swift-object-auditor
config                   manila-scheduler           octavia-housekeeping    swift-object-expirer
cron                     manila-share               octavia-openrc.sh       swift-object-replication-server
demo-user_admin.sh       mariadb                    octavia-worker          swift-object-replicator
fluentd                  mariadb-clustercheck       openvswitch-db-server   swift-object-server
glance-api               memcached                  openvswitch-vswitchd    swift-object-updater
globals.d                neutron-dhcp-agent         passwords.yml           swift-proxy-server
globals.yml              neutron-l3-agent           placement-api           swift-rsyncd
gnocchi-api              neutron-metadata-agent     rabbitmq                tgtd
gnocchi-metricd          neutron-openvswitch-agent  redis
(os-venv) vagrant@openstack-aio:/etc/kolla$ cp admin-openrc.sh test-openrc.sh

```


```

(os-venv) vagrant@openstack-aio:/etc/kolla$ cat test-openrc.sh
# Ansible managed

# Clear any old environment that may conflict.
for key in $( set | awk '{FS="="}  /^OS_/ {print $1}' ); do unset $key ; done
export OS_PROJECT_DOMAIN_NAME='Default'
export OS_USER_DOMAIN_NAME='Default'
export OS_PROJECT_NAME='demo-project'
export OS_TENANT_NAME='admin'
export OS_USERNAME='test-user'
export OS_PASSWORD='123'
export OS_AUTH_URL='http://192.168.56.250:5000'
export OS_INTERFACE='internal'
export OS_ENDPOINT_TYPE='internalURL'
export OS_MANILA_ENDPOINT_TYPE='internalURL'
export OS_IDENTITY_API_VERSION='3'
export OS_REGION_NAME='Seoul'

```
![](https://velog.velcdn.com/images/jupiter-j/post/b75c65ff-b070-404b-b705-b2bef9201289/image.png)

![](https://velog.velcdn.com/images/jupiter-j/post/715f05d5-d517-45b0-b808-ee6827639fd7/image.png)


![](https://velog.velcdn.com/images/jupiter-j/post/a227f8ec-6152-4cd4-bbbf-eb23f07f3312/image.png)

![](https://velog.velcdn.com/images/jupiter-j/post/b46ed21b-22a9-4017-b08b-8d9d279c61d1/image.png)

![](https://velog.velcdn.com/images/jupiter-j/post/066f0bf5-7782-4fa1-9fcb-433795eb2581/image.png)

<br>
<br>

> ## 실습 설정 
* 프로젝트 1개 - practice-project
* 사용자 2개 - operator(권한:admin역할) , normal-user(권한:member역할)
* 인증파일 준비 - operator.sh , normal.sh



```
openstack user list
openstack role assignment list --project practice-project --names
openstack user create --domain default --password '123' operator
openstack user create --domain default --password '123' normal-user
openstack project create practice-project
openstack role add --project practice-project --user operator admin
openstack role add --project practice-project --user normal-user member


(os-venv) vagrant@openstack-aio:/etc/kolla$ cat operator.sh
# Ansible managed

# Clear any old environment that may conflict.
for key in $( set | awk '{FS="="}  /^OS_/ {print $1}' ); do unset $key ; done
export OS_PROJECT_DOMAIN_NAME='Default'
export OS_USER_DOMAIN_NAME='Default'
export OS_PROJECT_NAME='practice-project'
export OS_TENANT_NAME='operator'
export OS_USERNAME='operator'
export OS_PASSWORD='123'
export OS_AUTH_URL='http://192.168.56.250:5000'
export OS_INTERFACE='internal'
export OS_ENDPOINT_TYPE='internalURL'
export OS_MANILA_ENDPOINT_TYPE='internalURL'
export OS_IDENTITY_API_VERSION='3'
export OS_REGION_NAME='Seoul'
export OS_AUTH_PLUGIN='password'
(os-venv) vagrant@openstack-aio:/etc/kolla$ cat normal.sh
# Ansible managed

# Clear any old environment that may conflict.
for key in $( set | awk '{FS="="}  /^OS_/ {print $1}' ); do unset $key ; done
export OS_PROJECT_DOMAIN_NAME='Default'
export OS_USER_DOMAIN_NAME='Default'
export OS_PROJECT_NAME='practice-project'
export OS_TENANT_NAME='admin'
export OS_USERNAME='normal-user'
export OS_PASSWORD='123'
export OS_AUTH_URL='http://192.168.56.250:5000'
export OS_INTERFACE='internal'
export OS_ENDPOINT_TYPE='internalURL'
export OS_MANILA_ENDPOINT_TYPE='internalURL'
export OS_IDENTITY_API_VERSION='3'
export OS_REGION_NAME='Seoul'

```

<br>
<br>

> ## 실습
1. cirrors 이미지를 my-image라는 이름으로 생성
2. 보호 설정을 활성화
3. 삭제 시도
4. test-project 라는 프로젝트 생성
5. test-user 사용자 생성
6. test-user 사용자에 test-project에 대한 member 역할 설정
7. test-user 사용자로 이미지 목록 확인
8. 가시성 설정을 public으로 변경
9. test-user 사용자로 다시 확인


![](https://velog.velcdn.com/images/jupiter-j/post/7fbf4f20-cd42-4038-a017-5d1570b60961/image.png)

### 이미지 생성
* 이미지 생성 : `openstack image create --file cirros-0.6.2-x86_64-disk.img --disk-format qcow2 demo-im `
* 이미지 목록 확인 : ` openstack image list`
```

(os-venv) vagrant@openstack-aio:~$ ls
all-in-one                 cirros-0.6.2-x86_64-disk.img        octavia
amphora-x64-haproxy.d      debian-12-genericcloud-amd64.qcow2  os-venv
amphora-x64-haproxy.qcow2  manila-service-image-master.qcow2   VBoxGuestAdditions_7.1.6.iso
(os-venv) vagrant@openstack-aio:~$ openstack image create --file cirros-0.6.2-x86_64-disk.img --disk-format qcow2 demo-im                     g
+------------------+-----------------------------------------------------------------------------------------------------                     -----------------------------------------+
| Field            | Value                                                                                                                                                             |
+------------------+-----------------------------------------------------------------------------------------------------                     -----------------------------------------+
| container_format | bare                                                                                                                                                              |
| created_at       | 2025-05-28T06:33:33Z                                                                                                                                              |
| disk_format      | qcow2                                                                                                                                                             |
| file             | /v2/images/fd0fec44-42e3-4925-bb31-f9412798797f/file                                                                                                              |
| id               | fd0fec44-42e3-4925-bb31-f9412798797f                                                                                                                              |
| min_disk         | 0                                                                                                                                                                 |
| min_ram          | 0                                                                                                                                                                 |
| name             | demo-img                                                                                                                                                          |
| owner            | 00855a5cafa646478a16f350df1f00f6                                                                                                                                  |
| properties       | os_hidden='False', owner_specified.openstack.md5='', owner_specified.openstack.object='images/demo-i                     mg', owner_specified.openstack.sha256='' |
| protected        | False                                                                                                                                                             |
| schema           | /v2/schemas/image                                                                                                                                                 |
| status           | queued                                                                                                                                                            |
| tags             |                                                                                                                                                                   |
| updated_at       | 2025-05-28T06:33:33Z                                                                                                                                              |
| visibility       | shared                                                                                                                                                            |
+------------------+-----------------------------------------------------------------------------------------------------                     -----------------------------------------+
(os-venv) vagrant@openstack-aio:~$ openstack image list
+--------------------------------------+---------------------+--------+
| ID                                   | Name                | Status |
+--------------------------------------+---------------------+--------+
| 6906bd8f-7956-4d85-ad43-87af6da739b7 | amphora-x64-haproxy | active |
| fd0fec44-42e3-4925-bb31-f9412798797f | demo-img            | active |
| 4d9abb87-9dbb-4a4a-a945-6fbe87dc090c | my-image            | queued |
| 0eb99ac7-03d5-4d35-858f-8a541f9af77b | my-image1           | queued |
| 2d88772b-3fd4-4558-be12-b430b5a00519 | my-image1           | queued |
+--------------------------------------+---------------------+--------+
(os-venv) vagrant@openstack-aio:~$

```
- 보호 해제 후 이미지 삭제 : `openstack image set --unprotected 4d9abb87-9dbb-4a4a-a945-6fbe87dc090c`

![](https://velog.velcdn.com/images/jupiter-j/post/0975594d-9433-451c-8507-693a42fed4ba/image.png)


<br>

### flavor 생성
![](https://velog.velcdn.com/images/jupiter-j/post/ccc7f586-ea8d-4ec9-b33a-f52413b87a10/image.png)

![](https://velog.velcdn.com/images/jupiter-j/post/d9377843-f2b5-4678-9405-4b6c8af7402b/image.png)
![](https://velog.velcdn.com/images/jupiter-j/post/080431f9-ed32-4e4d-a0d5-09219da908ad/image.png)
```

(os-venv) vagrant@openstack-aio:/etc/kolla$ openstack flavor list
+--------------------------------------+------+-----+------+-----------+-------+-----------+
| ID                                   | Name | RAM | Disk | Ephemeral | VCPUs | Is Public |
+--------------------------------------+------+-----+------+-----------+-------+-----------+
| da5dc3b9-9177-4341-b94d-c1299008f590 | mini | 512 |    1 |         0 |     1 | True      |
+--------------------------------------+------+-----+------+-----------+-------+-----------+

(os-venv) vagrant@openstack-aio:/etc/kolla$ openstack flavor list --all
+--------------------------------------+---------+-----+------+-----------+-------+-----------+
| ID                                   | Name    | RAM | Disk | Ephemeral | VCPUs | Is Public |
+--------------------------------------+---------+-----+------+-----------+-------+-----------+
| 200                                  | amphora | 512 |    5 |         0 |     1 | False     |
| da5dc3b9-9177-4341-b94d-c1299008f590 | mini    | 512 |    1 |         0 |     1 | True      |
+--------------------------------------+---------+-----+------+-----------+-------+-----------+

```


- flavor생성: `openstack flavor create --ram 512
--disk 1 --vcpus 1 mini-flavor`
- disk 생성: ` openstack flavor create --ram 1024 --disk 10 --vcpus 1 --ephemeral 1 --swap 12 demo-disk
`

![](https://velog.velcdn.com/images/jupiter-j/post/88b22f22-9b46-46bf-a77f-8ad71d5dcf76/image.png)![](https://velog.velcdn.com/images/jupiter-j/post/6c077d6f-9118-4e15-af95-2fd6811077ef/image.png)

<br>

### 네트워크 생성 

- 일반 사용자는 내부용 네트워크만 생성 가능
- 관리자 네트워크는 외부, 내부 네트워크 생성 가능

![](https://velog.velcdn.com/images/jupiter-j/post/d9ca1f39-953b-48e6-854d-a8d3fcfb041f/image.png)
![](https://velog.velcdn.com/images/jupiter-j/post/26e3aa68-65bb-474f-aa0c-82d56a400f79/image.png)
![](https://velog.velcdn.com/images/jupiter-j/post/0394f943-5b8b-4a63-905a-f4d86b0c6432/image.png)

![](https://velog.velcdn.com/images/jupiter-j/post/a86ba2f9-177a-4aad-9750-e574cfd44fe8/image.png)

```

(os-venv) vagrant@openstack-aio:/etc/kolla$ openstack flavor show demo-flavor
+----------------------------+--------------------------------------+
| Field                      | Value                                |
+----------------------------+--------------------------------------+
| OS-FLV-DISABLED:disabled   | False                                |
| OS-FLV-EXT-DATA:ephemeral  | 0                                    |
| access_project_ids         | None                                 |
| description                | None                                 |
| disk                       | 10                                   |
| id                         | 76df590a-6e24-465b-98f6-a500b6ff4355 |
| name                       | demo-flavor                          |
| os-flavor-access:is_public | True                                 |
| properties                 |                                      |
| ram                        | 2028                                 |
| rxtx_factor                | 1.0                                  |
| swap                       | 0                                    |
| vcpus                      | 1                                    |
+----------------------------+--------------------------------------+
(os-venv) vagrant@openstack-aio:/etc/kolla$ openstack network

```
![](https://velog.velcdn.com/images/jupiter-j/post/2bf269cb-2dbe-4d87-b729-1a3c0010f7aa/image.png)

* 네트워크 생성 : ` openstack network create new-int`
* 네트워크 목록 확인: `openstack network list`

```

(os-venv) vagrant@openstack-aio:/etc/kolla$ openstack network list
+--------------------------------------+------------------------+--------------------------------------+
| ID                                   | Name                   | Subnets                              |
+--------------------------------------+------------------------+--------------------------------------+
| 1b3dc678-3fc3-435c-9d4e-642100de4763 | lb-mgmt-net            | e485a912-594b-4c5d-a7a3-46bee0139c2a |
| b026697c-fb06-4536-b7a1-e58b1dc5f402 | manila_service_network |                                      |
| d52a595d-d6e5-41a0-b01d-1115a4550c42 | demo-network           | 281028ff-1f61-4ce9-824a-a3eb13e01e1a |
+--------------------------------------+------------------------+--------------------------------------+
(os-venv) vagrant@openstack-aio:/etc/kolla$

```
- 서브넷 생성: `openstack subnet create --network demo-network --subnet-range 172.17.0.0/24 --gateway 172.17.0.1 --dhcp --dns-nameserver 8.8.8.8 --allocation-pool start=172.17.0.2,end=172.17.0.254 new-subnet
`
```

(os-venv) vagrant@openstack-aio:/etc/kolla$ openstack subnet create --network demo-network --subnet-range 172.17.0.0/24 --gateway 172.17.0.1 --dhcp --dns-nameserver 8.8.8.8 --allocation-pool start=172.17.0.2,end=172.17.0.254 new-subnet
+----------------------+--------------------------------------+
| Field                | Value                                |
+----------------------+--------------------------------------+
| allocation_pools     | 172.17.0.2-172.17.0.254              |
| cidr                 | 172.17.0.0/24                        |
| created_at           | 2025-05-28T07:23:13Z                 |
| description          |                                      |
| dns_nameservers      | 8.8.8.8                              |
| dns_publish_fixed_ip | None                                 |
| enable_dhcp          | True                                 |
| gateway_ip           | 172.17.0.1                           |
| host_routes          |                                      |
| id                   | 25bc9fb4-aa59-4450-a922-cc9ebb58d25f |
| ip_version           | 4                                    |
| ipv6_address_mode    | None                                 |
| ipv6_ra_mode         | None                                 |
| name                 | new-subnet                           |
| network_id           | d52a595d-d6e5-41a0-b01d-1115a4550c42 |
| project_id           | 00855a5cafa646478a16f350df1f00f6     |
| revision_number      | 0                                    |
| segment_id           | None                                 |
| service_types        |                                      |
| subnetpool_id        | None                                 |
| tags                 |                                      |
| updated_at           | 2025-05-28T07:23:13Z                 |
+----------------------+--------------------------------------+

(os-venv) vagrant@openstack-aio:/etc/kolla$ openstack subnet list
+--------------------------------------+----------------+--------------------------------------+----------------+
| ID                                   | Name           | Network                              | Subnet         |
+--------------------------------------+----------------+--------------------------------------+----------------+
| 25bc9fb4-aa59-4450-a922-cc9ebb58d25f | new-subnet     | d52a595d-d6e5-41a0-b01d-1115a4550c42 | 172.17.0.0/24  |
| 281028ff-1f61-4ce9-824a-a3eb13e01e1a | demo-subnet    | d52a595d-d6e5-41a0-b01d-1115a4550c42 | 192.168.0.0/24 |
| e485a912-594b-4c5d-a7a3-46bee0139c2a | lb-mgmt-subnet | 1b3dc678-3fc3-435c-9d4e-642100de4763 | 10.1.0.0/24    |
+--------------------------------------+----------------+--------------------------------------+----------------+
(os-venv) vagrant@openstack-aio:/etc/kolla$

```
![](https://velog.velcdn.com/images/jupiter-j/post/f3ec74b8-523b-479a-a2ea-8ac2b1e992a0/image.png)

- 네트워크 삭제: `openstack network delete demo-network`
네트워크를 지우면 서브넷은 자동으로 삭제가 된다. 


> ## 실습
1. 이미지 생성 
* 이름: test-img / 파일:cirros / 유형: qcow2/ 가시성:share/ 보호설정은 필요없음
* 이름: debian-img / 파일:debian / 유형: qcow2/ 가시성:public/ 보호설정은 활성화 
2. 플레이버 생성 
* 이름: mini / vcpu:1, ram:512M, root-disk:1G 
* 이름: demo-flavor, vcpu:1, ram:2048M, root-disk:10G
3. 네트워크 생성
* 네트워크 이름: internal, 
* subnet이름: internal-subnet, ip대역: 192.168.100.0/24, gateway:192.168.100.1, DNS:8.8.8.8  


### 이미지 생성 
* test-image 생성 : `openstack image create "test-img" --file cirros-0.6.2-x86_64-disk.img --disk-format qcow2 --container-format bare --shared`
* debian-iamge 생성: `openstack image create "debian-img" --file debian-12-genericcloud-amd64.qcow2 --disk-format qcow2 --container-format bare --public --protected`
![](https://velog.velcdn.com/images/jupiter-j/post/1995d4f4-2c26-45df-8ec1-6d25dc72a3ca/image.png)![](https://velog.velcdn.com/images/jupiter-j/post/d2829601-c7af-4921-934b-75fc1329da20/image.png)![](https://velog.velcdn.com/images/jupiter-j/post/6bf626c5-7e96-4cf8-a1a8-19e4fccc5382/image.png)

<br>

### 플레이버 생성 
* mini-flavor: `openstack flavor create --ram 512 --disk 1 --vcpus 1 mini-flavor`
* demo-flavor: `openstack flavor create --ram 2028 --disk 10 --vcpus 1 demo-flavor`
![](https://velog.velcdn.com/images/jupiter-j/post/00cfec04-d775-42eb-b8eb-f8eaad8eac49/image.png)

<br>

### 네트워크 생성
* 네트워크 생성 : ` openstack network create internal && openstack subnet create internal-subnet --network internal --subnet-range 192.168.100.0/24 --gateway 192.168.100.1 --dns-nameserver 8.8.8.8`

![](https://velog.velcdn.com/images/jupiter-j/post/8a6649da-6c86-4aed-ba1b-3a1d1192ff2c/image.png)![](https://velog.velcdn.com/images/jupiter-j/post/d84db6bd-4fef-490b-9168-7fbbeddf38bc/image.png)![](https://velog.velcdn.com/images/jupiter-j/post/7f1b1ffd-4513-4e23-bb06-7c3f45f70fb1/image.png)

<br>


## 인스턴스 생성
![](https://velog.velcdn.com/images/jupiter-j/post/a9664c86-ea8e-4ebd-a7cd-10a36dd038d3/image.png)

![](https://velog.velcdn.com/images/jupiter-j/post/ff72d248-8317-4f76-8ff3-da35f6dc8fe0/image.png)
![](https://velog.velcdn.com/images/jupiter-j/post/c3bc0b89-213f-4817-871d-24db7e29c1c2/image.png)

```

(os-venv) vagrant@openstack-aio:~$ openstack server create --image test-img --flavor mini-flavor --network internal test-vm
+-------------------------------------+----------------------------------------------------+
| Field                               | Value                                              |
+-------------------------------------+----------------------------------------------------+
| OS-DCF:diskConfig                   | MANUAL                                             |
| OS-EXT-AZ:availability_zone         |                                                    |
| OS-EXT-SRV-ATTR:host                | None                                               |
| OS-EXT-SRV-ATTR:hypervisor_hostname | None                                               |
| OS-EXT-SRV-ATTR:instance_name       |                                                    |
| OS-EXT-STS:power_state              | NOSTATE                                            |
| OS-EXT-STS:task_state               | scheduling                                         |
| OS-EXT-STS:vm_state                 | building                                           |
| OS-SRV-USG:launched_at              | None                                               |
| OS-SRV-USG:terminated_at            | None                                               |
| accessIPv4                          |                                                    |
| accessIPv6                          |                                                    |
| addresses                           |                                                    |
| adminPass                           | vy5RpQnmvzmg                                       |
| config_drive                        |                                                    |
| created                             | 2025-05-28T08:35:49Z                               |
| flavor                              | mini-flavor (6c4f3d8a-799b-44e1-8035-7e56966b086f) |
| hostId                              |                                                    |
| id                                  | e84dd025-363c-41bf-bdf6-ad639ec4cd33               |
| image                               | test-img (2ee4f418-d721-4fe4-aebf-0920206ce358)    |
| key_name                            | None                                               |
| name                                | test-vm                                            |
| progress                            | 0                                                  |
| project_id                          | 00855a5cafa646478a16f350df1f00f6                   |
| properties                          |                                                    |
| security_groups                     | name='default'                                     |
| status                              | BUILD                                              |
| updated                             | 2025-05-28T08:35:50Z                               |
| user_id                             | 58d8e1d0c87143aaad968509ea167b17                   |
| volumes_attached                    |                                                    |
+-------------------------------------+----------------------------------------------------+

```
- 인스턴스 생성 : ` openstack server create --image test-img --flavor mini-flavor --network internal test-vm`


![](https://velog.velcdn.com/images/jupiter-j/post/e580628e-4d45-4a00-8e56-fc888268e02b/image.png)

![](https://velog.velcdn.com/images/jupiter-j/post/643d1f5a-d107-4c34-a53c-0a622792b1c3/image.png)

![](https://velog.velcdn.com/images/jupiter-j/post/721387c7-8124-482a-a5a5-d93a11f92a1f/image.png)

![](https://velog.velcdn.com/images/jupiter-j/post/30cafbcd-a6cb-4105-a5ad-635db5ac46aa/image.png)






### 오픈스택 인스턴스 생성과정
1. horizon 서비스에서 인스턴스 생성 작업 지시
2. keystone 서비스를 통해 인증
3. nova-api 서비스로 인스턴스 생성 요청
4. nova-conductor 서비스로 요청 내용 전달
5. nova-scheduler 서비스로 요청해서 사용 가능한 노드를 확인
6. placement 서비스를 통해 리소스 사용에 대한 정보 확인
7. 정보를 바탕으로 사용 가능한 노드 목록을 선정
8. nova-scheduler에서 노드를 선택
9. nova-conductor 최종으로 선택해서 배치
10. glance-api에 이미지를 요청
11. 백엔드 스토리지에서 선택된 컴퓨트 노드로 이미지를 복사
12. 컴퓨트 노드에 있는 nova-compute 서비스와 hypervisor로 인스턴스를 생성함 




