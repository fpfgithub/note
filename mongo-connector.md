#### pip命令执行出错
    pip install --upgrade pip
    DEPRECATION: Failed to find 'pip' at https://pypi.gongchang.com/pip/.
    It is suggested to upgrade your index to support normalized names as the name in /simple/{name}.

    解决办法 : 更换获取pypi的默认镜像源 --index https://pypi.mirrors.ustc.edu.cn/simple/

    ``` [root@fpf152 ~]# pip install ./mongo-connector --index https://pypi.mirrors.ustc.edu.cn/simple/
    You are using pip version 7.1.0, however version 9.0.1 is available.
    You should consider upgrading via the 'pip install --upgrade pip' command.
    Processing ./mongo-connector
    Collecting pymongo>=2.9 (from mongo-connector==2.5.1)
    /usr/lib/python2.6/site-packages/pip/_vendor/requests/packages/urllib3/util/ssl_.py:90: InsecurePlatformWarning: A true SSLContext object is not available. This prevents urllib3 from configuring SSL appropriately and may cause certain SSL connections to fail. For more information, see https://urllib3.readthedocs.org/en/latest/security.html#insecureplatformwarning.
      InsecurePlatformWarning
    /usr/lib/python2.6/site-packages/pip/_vendor/requests/packages/urllib3/util/ssl_.py:90: InsecurePlatformWarning: A true SSLContext object is not available. This prevents urllib3 from configuring SSL appropriately and may cause certain SSL connections to fail. For more information, see https://urllib3.readthedocs.org/en/latest/security.html#insecureplatformwarning.
      InsecurePlatformWarning
      Downloading https://mirrors.ustc.edu.cn/pypi/web/packages/82/26/f45f95841de5164c48e2e03aff7f0702e22cef2336238d212d8f93e91ea8/pymongo-3.4.0.tar.gz (583kB)
        100% |████████████████████████████████| 585kB 472kB/s
    Installing collected packages: pymongo, mongo-connector
      Running setup.py install for pymongo
      Running setup.py install for mongo-connector
    Successfully installed mongo-connector-2.5.1 pymongo-3.4.0 ```

#### 安装和es对应版本的connector
    [root@fpf152 ~]# ps aux | grep elasticsearch
    [root@fpf152 ~]#
    [root@fpf152 ~]# pip install 'mongo-connector[elastic5]' --index https://pypi.mirrors.ustc.edu.cn/simple/
