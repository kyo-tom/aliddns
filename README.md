# aliddns
Simple DDNS utility with Alibaba Cloud (Aliyun) SDK

## Usage
1. Create `.ddns_config` file in home directory, with following content:
```json
{
	"access_key_id" : "<AccessKey ID>",
	"access_key_secret" : "<AccessKey Secret>",
	"connect_timeout" : 1500,
	"read_timeout" : 4000,
	"domain_name" : "ddns4.domain.com,ipv4.domain.com",
	"domain_name_v6" : "ddns6.domain.com,ipv6.domain.com",
	"query_ipv4" : "https://api.ipify.org",
	"query_ipv6" : "https://api6.ipify.org"
}
```
* `access_key_id`: Aliyun AccessKey ID
* `access_key_secret`: Aliyun AccessKey Secret
* `connect_timeout` (optional): Aliyun SDK connection timeout in ms
* `read_timeout` (optional): Aliyun SDK read timeout in ms
* `domain_name`: Domain name to update A record
* `query_ipv4`: URL to query current public IPv4 address, **this URL should return IPv4 plain text!!!**
* `domain_name_v6`: (optional) Domain name to update AAAA record
* `query_ipv6`: (optional if `domain_name_v6` does not exist) URL to query current public IPv6 address, **this URL should return IPv6 plain text, and it should only have AAAA domain record!!!**

2. Simply run this to check and update domain record if needed:
```shell
$ aliddns
Old IPv4:     (ddns4.domain.com)[1.2.3.4]
Current IPv4: (ddns4.domain.com)[1.1.1.1]
Updated:      (ddns4.domain.com)[1.1.1.1]
Old IPv4:     (ipv4.domain.com)[1.1.1.1]
Current IPv4: (ipv4.domain.com)[1.1.1.1]
Skipped:      (ipv4.domain.com)[1.1.1.1]
Old IPv6:     (ddns6.domain.com)[a55a:a55a:a55a:a55a:a55a:a55a:a55a:1]
Current IPv6: (ddns6.domain.com)[a55a:a55a:a55a:a55a:a55a:a55a:a55a:a55a]
Updated:      (ddns6.domain.com)[a55a:a55a:a55a:a55a:a55a:a55a:a55a:a55a]
Old IPv6:     (ipv6.domain.com)[a55a:a55a:a55a:a55a:a55a:a55a:a55a:a55a]
Current IPv6: (ipv6.domain.com)[a55a:a55a:a55a:a55a:a55a:a55a:a55a:a55a]
Skipped:      (ipv6.domain.com)[a55a:a55a:a55a:a55a:a55a:a55a:a55a:a55a]
```

## Dependencies
* curl
* jsoncpp
* alibabacloud-sdk-alidns
* alibabacloud-sdk-core

[Install Alibaba Cloud SDK](https://github.com/aliyun/aliyun-openapi-cpp-sdk/blob/master/README-CN.md)  

When installing Alibaba Cloud SDK, these parts should be compiled and installed: `core` and `alidns`.  
Use following commands:  
```shell
sudo apt-get install libcurl4-openssl-dev libssl-dev uuid-dev libjsoncpp-dev
git clone https://github.com/aliyun/aliyun-openapi-cpp-sdk.git
cd aliyun-openapi-cpp-sdk
mkdir sdk_build
cd sdk_build
sudo cmake -DCMAKE_BUILD_TYPE=Release -DCMAKE_INSTALL_PREFIX=/usr -DBUILD_PRODUCT=alidns ..
sudo make
sudo make install
```
or 
```shell
sudo apt-get install libcurl4-openssl-dev libssl-dev uuid-dev libjsoncpp-dev
git clone https://github.com/aliyun/aliyun-openapi-cpp-sdk.git
sudo sh easyinstall.sh core
sudo sh easyinstall.sh alidns
```

## Build aliddns
Build from source
```shell
sudo apt-get install libcurl4-openssl-dev libssl-dev uuid-dev libjsoncpp-dev
git clone https://gitea.manaphy.cn:8443/kyotom/aliddns.git
cd aliddns
mkdir build
cd build
cmake ../
make
sudo make install
```

## Using aliddns by systemd timer
Create "aliddns.service" and "aliddns.timer" and copy them to "/usr/lib/systemd/system".
Then:
```shell
systemctl daemon-reload
systemctl enable aliddns.timer
systemctl start aliddns.timer
systemctl status aliddns.timer -l
```

- aliddns.service
    ```txt
    [Unit]
    Description=Aliyun DDNS Client.
    Wants=network-online.target
    After=network.target network-online.target
    
    [Service]
    Type=simple
    User=yourname
    Group=yourgroup
    ExecStart=aliddns
    ```
2. aliddns.timer
    ```txt
    [Unit]
    Description=Run Aliyun DDNS Client every 10 minutes.
    
    [Timer]
    OnBootSec=0s
    OnUnitActiveSec=10min
    Unit=aliddns.service
    
    [Install]
    WantedBy=multi-user.target
    ```
