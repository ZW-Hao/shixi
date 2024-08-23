# CMDB 接口文档

参考地址：[API文档 | 维易文档中心 (veops.cn)](https://veops.cn/docs/docs/cmdb/cmdb_api)



## 实现定时更新物理机操作系统

### 执行步骤：

1. 登录10.9.10.220
2. cd /root/cmdb_new
3. python3 main.py



### main.py

```python
from getUtils import getIPList
from getUtils import getSnByIP
from updateUtils import updateOsVersion
from getOsversion import getOsversion

IPList = getIPList()
#print(IPList)
for ip in IPList:
    sn = getSnByIP(ip)
 #   print(sn)
    osVersion = getOsversion(ip)
    print(ip+":"+osVersion)
    updateOsVersion(sn,osVersion)
print("系统版本检测完成")
```



### 获取CMDB中所有物理机的IP地址和序列号

```python
import hashlib

import requests
from future.moves.urllib.parse import urlparse


URL = "http://10.9.10.220:8000/api/v0.1/ci/s"
KEY = "e017fcfed6924622a07de1811a5a3584"
SECRET = "q1XvkiCbfK#atFUoZ&Eh$c7Nds2lJu3n"


def build_api_key(path, params):
    values = "".join([str(params[k]) for k in sorted((params or {}).keys())
                      if k not in ("_key", "_secret") and not isinstance(params[k], (dict, list))])
    _secret = "".join([path, SECRET, values]).encode("utf-8")
    params["_secret"] = hashlib.sha1(_secret).hexdigest()
    params["_key"] = KEY

    return params


def getSnByIP(ip):
    payload = {
        "q": "private_ip:"+ip,
        "fl": "sn",
        "count": "1"
    }
    data = get_ci(payload)
    sn = data['result'][0]['sn']
    return sn

def getIPList():
    IPList=[]
    payload = {
        "q": "sn:*",
        "fl": "private_ip",
        "count": "2000"
    }
    data = get_ci(payload)

    ipNumbers = data["numfound"]
    for i in range(ipNumbers):
        private_ip_value = data['result'][i]['private_ip']
        IPList.append(private_ip_value)
    return IPList


def get_ci(payload):
    payload = build_api_key(urlparse(URL).path, payload)
    response = requests.get(URL, json=payload)
    return response.json()

```



### 获取操作系统版本

```python
import paramiko
def getOsversion(ip):
    osVersion=''
    #ssh_timeout = 30
    # 创建SSH客户端对象
    ssh_client = paramiko.SSHClient()
    ssh_client.set_missing_host_key_policy(paramiko.AutoAddPolicy())

    try:

        # 或者如果使用SSH密钥
        ssh_client.connect(hostname=ip, key_filename='/root/infra.key', timeout = 30)

        # 执行命令获取操作系统信息
        stdin, stdout, stderr = ssh_client.exec_command('cat /etc/os-release')
        os_info = stdout.read().decode()

        #print(os_info)  # Output the OS information
        # 解析出VERSION字段
        # 初始化变量以保存NAME和VERSION的值
        name_value = ''
        version_value = ''

        # 解析出NAME和VERSION字段
        for line in os_info.splitlines():
            if line.startswith('NAME='):
                name_value = line.split('=', 1)[1].strip('"')
            elif line.startswith('VERSION='):
                version_value = line.split('=', 1)[1].strip('"')

        # 拼接NAME和VERSION的值
        if name_value and version_value:
            osVersion = "{} {}".format(name_value, version_value)
           # print(osVersion)  # 打印拼接后的操作系统全名
    except paramiko.ssh_exception.NoValidConnectionsError as e:
        print(f"Unable to connect to {ip} on port 22: {e}")
    except paramiko.ssh_exception.AuthenticationException:
        print("Authentication failed, please verify your credentials")
    except paramiko.ssh_exception.SSHException as e:
        print(f"SSH connection to {ip} failed: {e}")
    except Exception as e:
        print(f"An error occurred: {e}")

    finally:
        # 关闭SSH连接
        ssh_client.close()

    return osVersion


```



### 更新

```python
import hashlib
import requests
from future.moves.urllib.parse import urlparse

URL = "http://10.9.10.220:8000/api/v0.1/ci"
KEY = "e017fcfed6924622a07de1811a5a3584"
SECRET = "q1XvkiCbfK#atFUoZ&Eh$c7Nds2lJu3n"


def build_api_key(path, params):
    values = "".join([str(params[k]) for k in sorted((params or {}).keys())
                      if k not in ("_key", "_secret") and not isinstance(params[k], (dict, list))])
    _secret = "".join([path, SECRET, values]).encode("utf-8")
    params["_secret"] = hashlib.sha1(_secret).hexdigest()
    params["_key"] = KEY

    return params
def updateOsVersion(sn,osVersion):
    
    payload = {
        "sn": sn,
        "os_version": osVersion,
        "ci_type": 4
    }
    update_ci(payload)

    return 0

def update_ci(payload):
    payload = build_api_key(urlparse(URL).path, payload)
    response = requests.put(URL, json=payload)
    return response.json()

```

