

# 常量说明
## 返回码
    ErrCode: {
        EC_OK: 200, 
        EC_BAD_REQUEST: 400,     
        EC_SIGN_ERROR: 498,
        EC_DATABASE_ERROR: 500,
        EC_UPDATE_ERROR: 601,
        EC_INVALID_PARAM: 602,
        EC_USER_EXIST: 612,
        EC_USER_NOT_EXIST: 620,
        EC_USER_PWD_ERROR: 621,
        //这几个错误码是给后台回调用的
        EC_SYSTEM_INVALID_JSON_FORMAT: 4001,
        EC_SYSTEM_INVALID_PARA: 4002,
        EC_SYSTEM_FREQUECY: 4003
    }

## UGC状态

     ResourceStatus:{
        READY:0,        //视频处理完成
        NOT_READY:1,    //等待视频处理
    }


## 审核状态

     ReviewStatus:{
        NotReivew:0,    //未审核
        Normal:1,       //正常
        Porn:2,         //涉黄
    }

# 拉取视频列表
## 接口名称
get_ugc_list
## 功能说明
拉取服务端视频列表，该接口不需要鉴权。
## 请求方式
### 请求方法
POST
### 请求地址
/get_ugc_list

### 参数说明
参数名 | 类型 | 必填 | 描述 | 默认值 | 示例 
:-: | :-: | :-: | :-: | :-: | :-: 
index | number | 否 | 页码 | 0 | 0 
count | number | 否| 一页数量 | 10 | 10 

### 请求示例
curl -d "index=0&count=10" "/get_ugc_list"

### 接口应答
参数名 | 类型 |  描述 | 示例 
:-: | :-: | :-: | :-: 
code | number | 返回码 |  200 
message| string | 返回消息 |  
data | object | 返回数据 |
data.list | array | 视频列表 | 
data.list.n | ugcinfo | 视频详细信息 |
### ugcinfo
参数名 | 类型 |  描述 | 示例 
:-: | :-: | :-: | :-: 
status | integer | 视频状态 |  0 
review_status| integer | 视频审核状态 |  0
userid | string | 所属用户id | 
file_id | string | 文件id | 
title | string | 视频标题 |
frontcover | string | 视频封面 |
location | string | 上传地理位置 |
play_url | string | 播放地址 |
create_time | string | 上传时间 |
nickname | string | 用户昵称 |
avatar | string | 用户头像 |



### 应答示例

    {
    	"code": 200,  
    	"message": "OK",
    	"data": {
    		"list": [
    			{
    			    "status":0,
    			    "review_status":0,
    				"userid":"xxx",			//用户id
    				"nickname":"xxx",		//昵称
    				"avatar":"xxx",			//头像url
    				“file_id":"xxx",        //点播文件id
    				"title":"xxxx“,         //标题
    				"frontcover":"xxx",     //封面图url
    				"location":"xxx",       //地理位置
    				"play_url":"xxx",       //播放地址
    				"create_time":"xxxx-xx-xx xx:xx:xx",  //创建时间
    			}
    		]
    	}
    }

# 用户注册
## 接口名称
register
## 功能说明
用户注册

## 请求方式

### 请求方法

POST
### 请求地址
/register

### 参数说明

参数名 | 类型 | 必填 | 描述 | 默认值 | 示例 
:-: | :-: | :-: | :-: | :-: | :-: 
userid | string | 是 |用户id |   |   user001
password | string | 是| 密码 |  |    md5(md5(password)+userid)

### 请求示例


### 接口应答
参数名 | 类型 |  描述 | 示例 
:-: | :-: | :-: | :-: 
code | number | 返回码 | 200 
message| string | 返回消息 |  

### 应答示例

    {
    	"code" : 200, 
    	"message": "", 
    }


# 登录
## 接口名称
login
## 功能说明
客户端使用帐号和密码登录，登录成功后返回令牌信息，cos配置信息和点播配置信息，其中token用于随后的权限校验，用户在调用需要鉴权的接口时需要带上 userid 和 HTTP_LITEAV_SIG 两个数据，其中 HTTP_LITEAV_SIG = md5(token+req.url+userid)。

## 请求方式

### 请求方法

POST

### 请求地址
/login

### 参数说明
参数名 | 类型 | 必填 | 描述 | 默认值 | 示例 
:-: | :-: | :-: | :-: | :-: | :-: 
userid | string | 是 |用户id |   |   user001
password | string | 是| 密码 |  |    md5(md5(password)+userid)


### 请求示例


### 接口应答

参数名 | 类型 |  描述 | 示例 
:-: | :-: | :-: | :-:
code | number | 返回码 | 200 
message| string | 返回消息 |  
data| object | 返回数据 |  
data.token | string | 鉴权令牌 | 
data.refresh_token | string | 刷新鉴权令牌 | 
data.expires | integer | 过期时间 | 300
data.cos_info | object | cos配置信息 | 
data.cos_info.Bucket | string | cos bucket名 | 
data.cos_info.Region | string | cos bucket所在地域 | 
data.cos_info.Appid | string | cos Appid |
data.cos_info.SecretId | string | cos Secretid |
data.vod_info.Appid | string | vod appid |
data.vod_info.SubAppId | string | vod SubAppId |
data.vod_info.SecretId | string | vod SecretId |




### 应答示例

    {
    	"code": 200,  
    	"message": "OK",
    	"data":{ 
    		"token": "xxx",    //随机数
    		"refresh_token": "xxx", //续期token，随机数
    		"expires": 300,  // 过期时间（秒）
    		"roomservice_sign": {       //登录roomservice的签名
    			"sdkAppID": 123456,     // 云通信 sdkappid
    			"accountType": "xxxx",  // 云通信 账号集成类型
    			"userID": "xxxx",       // 用户id
    			"userSig": "xxxxxxxx",  // 云通信用户签名
    		},
    		"cos_info": {
    		    "Bucket": "xxx",           //cos bucket名
    		    "Region": "xxx",           //cos bucket所在地域
    		    "Appid":  "xxx",           //cos appid
    		    "SecretId": "xxx"          //cos secretid
    		}
    	}
    }

# 获取 COS 签名

## 接口名称
get_cos_sign
## 功能说明
获取 cos 上传签名，可用于头像上传
## 请求方式

### 请求方法
POST

### 请求地址
/get_cos_sign

### 参数说明

无

### 请求示例


### 接口应答
参数名 | 类型 |  描述 | 示例 
:-: | :-: | :-: | :-: 
message| string | 返回消息 |  
data| object | 返回数据 |  
data.signKey | string | 按照cos的签名规则计算出来的签名key | 
data.keyTime | string | 按照cos的签名规则，需要的签名有效期 | 



### 应答示例

    {
    	"code": 200,
    	"message": "OK",
    	"data": {
    		"signKey":"xxxx", 
    		"keyTime":"xxxx"      
    	}
    }

# 上报用户数据

## 接口名称
upload_user_info
## 功能说明
注册完成后，上传用户头像、昵称等信息,后台将根据请求数据带的userid更新相应用户属性。

## 请求方式

### 请求方法

POST

### 请求地址

/upload_user_info


### 参数说明

参数名 | 类型 | 必填 | 描述 | 默认值 | 示例 
:-: | :-: | :-: | :-: | :-: | :-: 
nickname | string | 是 |用户id |   |   user001
avatar | string | 是| 头像 |  |   
sex | string | 是| 性别 0:male,1:female,-1:unknown |  |    
frontcover | string | 是| 封面地址 |  |  

### 请求示例


### 接口应答

参数名 | 类型 |  描述 | 示例 
:-: | :-: | :-: | :-: 
code | number | 返回码 | 200 
message| string | 返回消息 |  

### 应答示例

    {
    	"code": 200,
    	"message": "OK"
    }





# 获取用户数据

## 接口名称
get_user_info
## 功能说明
获取用户头像，昵称等信息
## 请求方式

### 请求方法
POST

### 请求地址

/get_user_info

### 参数说明

无
### 请求示例


无

### 接口应答

参数名 | 类型 |  描述 | 示例 
:-: | :-: | :-: | :-: 
code | number | 返回码 | 200 
message| string | 返回消息 |  
data| object | 返回数据 |  
data.nickname | string | 昵称 | 
data.avatar | string | 头像 | 
data.sex | integer | 性别 | 
data.frontcover | string | 封面 | 

### 应答示例

    {
    	"code": 200,
    	"message": "OK",
    	"data": {
    		"nickname":"xxx",
    		"avatar":"http://xxxx",
    		"sex":0 //0:male,1:female,-1:unknown
    		"frontcover":"http://xxxx",     //封面图url
    	}
    }

# 获取点播上传签名


## 接口名称
get_vod_sign
## 功能说明



## 请求方式

### 请求方法
POST

### 请求地址

/get_vod_sign

### 参数说明

无


### 请求示例




### 接口应答

参数名 | 类型 |  描述 | 示例 
:-: | :-: | :-: | :-: 
code | number | 返回码 | 200 
message| string | 返回消息 |  
data| object | 返回数据 |  
data.signature | string | 签名参数 | 

### 应答示例


    {
    	"code": 200,
    	"message": "OK",
    	"data": {
    		"signature":"xxx", //请求的点播签名
    	}
    }



# 发布小视频

## 接口名称
upload_ugc
## 功能说明

## 请求方式

### 请求方法

POST

### 请求地址
/upload_ugc

### 参数说明

参数名 | 类型 | 必填 | 描述 | 默认值 | 示例 
:-: | :-: | :-: | :-: | :-: | :-: 
file_id | string | 是 |点播文件id |   |   user001
title | string | 是| 标题 |  |   
frontcover | string | 是| 封面URL |  |    
location | string | 是| 地理位置 |  |  
play_url | string | 是| 播放地址 |  |  

### 请求示例


### 接口应答

参数名 | 类型 |  描述 | 示例 
:-: | :-: | :-: | :-: 
code | number | 返回码 | 200 
message| string | 返回消息 |  

### 应答示例
    {
    	"code": 200, //602,参数错误; 601,更新失败; 500,数据库操作失败
    	"message": "OK"
    }





