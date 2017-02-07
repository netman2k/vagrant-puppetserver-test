# vagrant-puppetserver-test

PuppetServer 구성 및 Puppet 코드 테스트를 위한 Vagrant 설정

각 Vagrantfile에는 기본적인 다중 PuppetServer 구동에 필요한 초기화를 자동으로 대부분 해준다. 

**PuppetCA의 초기화 구문**
```
yum install -y git
git clone https://github.com/netman2k/puppet-repository.git
cd puppet-repository && ./scripts/init.sh

hostnamectl set-hostname puppetca01.example.com
systemctl restart systemd-hostnamed
systemctl stop firewalld

echo "dns_alt_names = puppetca.example.com, puppet.example.com, puppetca, puppet" >> /etc/puppetlabs/puppet/puppet.conf

systemctl start puppetserver
```
위 명령은 vagrant의 Provisiong에 의해 실행되며, 최소한의 동작을 시킬 수 있는 수준까지 생성시켜 준다.


***

### PuppetCA VM 구동
cd PuppetCA
vagrant up


***

### PuppetServer VM 구동
```
cd PuppetServer01
vagrant up
```

Vagrant 구동 후 일정 시간이 지나면 다음 커맨드에 의해 PuppetCA에게 SSL Certification을 요청하게 된다. 
```
puppet agent -t --server=puppetca.exmple.com --ca_server=puppetca.example.com --dns_alt_names=puppet.example.com,puppet  -w 30
```
이 때 PuppetCA VM으로 접근하여 다음 커맨드로 Certification Request를 허용해주도록 한다.
```
puppet cert sign puppet01.example.com --allow-dns-alt-names
```

만일 추가적으로 PuppetServer가 필요하다면 PuppetServer01 디렉토리를 복사한 후 필요에의해 Vagrantfile을 수정하여 사용한다.


***

### Edge 구동
```
cd Edge1
vagrant up
```
