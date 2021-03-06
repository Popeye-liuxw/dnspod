# DNSPod
使用python 获取本机ip 更新动态DNS记录到donpod

具体接口说明文档参考：https://www.dnspod.cn/docs/records.html#dns


接口地址： https://dnsapi.cn/Record.Ddns

HTTP请求方式：POST

请求参数：

公共参数
domain_id 或 domain, 分别对应域名ID和域名, 提交其中一个即可
record_id 记录ID，必选
sub_domain 主机记录，如 www
record_line 记录线路，通过API记录线路获得，中文，比如：默认，必选
record_line_id 线路的ID，通过API记录线路获得，英文字符串，比如：‘10=1’ 【record_line 和 record_line_id 二者传其一即可，系统优先取 record_line_id】
value IP地址，例如：6.6.6.6，可选

响应代码：

共通返回
-15 域名已被封禁
-7 企业账号的域名需要升级才能设置
-8 代理名下用户的域名需要升级才能设置
6 域名ID错误
7 不是域名所有者或没有权限
8 记录ID错误
21 域名被锁定
22 子域名不合法
23 子域名级数超出限制，比如免费套餐域名不支持三级域名
24 泛解析子域名错误，比如免费套餐载名不支持 a*
25 轮循记录数量超出限制，比如免费套餐域名只能存在两条轮循记录
26 记录线路错误，比如免费套餐域名不支持移动、国外

注意事项：

如果1小时之内，提交了超过5次没有任何变动的记录修改请求，该记录会被系统锁定1小时，不允许再次修改，所以在开发和测试的过程中，请自行处理IP变动，仅在本地IP发生变动的情况下才调用本接口。
如何理解没有任何变动的记录修改请求？比如原记录值已经是 1.1.1.1，新的请求还要求修改为 1.1.1.1。

示例:
curl -X POST https://dnsapi.cn/Record.Ddns -d 'login_token=LOGIN_TOKEN&format=json&domain_id=2317346&record_id=16894439&record_line_id=10%3D3&sub_domain=www'

返回参考：

JSON:
{
    "status": {
        "code":"1",
        "message":"Action completed successful",
        "created_at":"2015-01-18 17:23:58"
    },
    "record": {
        "id":16909160,
        "name":"@",
        "value":"111.111.111.111"
    }
}

字段说明:

id: 记录ID, 即为 record_id
name: 子域名
value”: 记录值

注意：

record_line_id 形如 “10=3”，其中可能会包含等号，即 “=”，如果是通过类似 URL 传递参数，需要将 ‘=’ 转义成 ‘%3D’


公共参数

所有的接口都需要这些参数，在此统一说明，下面的接口将不再列出。这些参数必须和接口参数一起提交。

login_token 用于鉴权的 API Token

format {json,xml} 返回的数据格式，可选，默认为xml，建议用json

lang {en,cn} 返回的错误语言，可选，默认为en，建议用cn

error_on_empty {yes,no} 没有数据时是否返回错误，可选，默认为yes，建议用no

user_id 用户的ID，可选，仅代理接口需要， 用户接口不需要提交此参数

说明：

API Token 生成方法详见：https://support.dnspod.cn/Kb/showarticle/tsid/227/  完整的 API Token 是由 ID,Token 组合而成的，用英文的逗号分割
旧的 “用户名 + 密码” 的鉴权方式仍能继续使用，推荐开发者优先使用 login_token 来鉴权


共通返回

这些返回在所有的接口都有可能返回，它们是所有接口共通的，在此统一说明，下面的接口将不再列出。另外，文档中的参考返回只列出JSON格式的，XML格式类似，就不再列出了。

-1 登陆失败
-2 API使用超出限制
-3 不是合法代理 (仅用于代理接口)
-4 不在代理名下 (仅用于代理接口)
-7 无权使用此接口
-8 登录失败次数过多，帐号被暂时封禁
85 帐号异地登录，请求被拒绝
-99 此功能暂停开放，请稍候重试
1 操作成功
2 只允许POST方法
3 未知错误
6 用户ID错误 (仅用于代理接口)
7 用户不在您名下 (仅用于代理接口)
83 该帐户已经被锁定，无法进行任何操作
85 该帐户开启了登录区域保护，当前IP不在允许的区域内
