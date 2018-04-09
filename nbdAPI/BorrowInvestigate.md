# 贷款端

#### [尽调审贷](#尽调审贷列表)
#### [添加尽调](#尽调)
#### [添加审贷](#审贷)
#### [授信签约列表](#授信签约)
#### [添加授信](#授信)
#### [添加签约](#签约)
#### [查询放款列表](#放款列表)
#### [综合查询借款申请列表](#借款申请列表)
#### [综合查询借款合同列表](#借款合同列表)
#### [查看借款详情](#根据id查询借款详情)
#### [查询合同详情](#根据id查询借款合同详情)

## 尽调审贷列表

#### 基本信息
* 请求类型：`HTTP`
* 请求地址：`/sys/bank/{type}/investigate`
* 请求方式：`GET`
* 请求类型：`int`
* 响应类型：`R`

### 接口描述
根据类型和借款人名称模糊查询申请列表
type 是审核状态
borrowName 是用户输入的借款人名称

#### 请求数据
|参数名称|是否必填|类型|长度|描述|默认值|备注|
|:----:|:----:|:----:|:----:|:---:|:----:|:---:|
|token|√|String||验证登录详情|
|pageNum||Integer||分页开始位置|0|整数|
|pageSize||Integer||每页显示数量|10|整数|
|borrowName||String|32|借款人名称|null|模糊条件|
|type||String|1|审核状态|1|1:待审核 2:审核不通过|



#### 响应数据
|参数名称|是否必填|描述|默认值|  
|:----:|:----:|:----:|:----:|  
|borrowId|√|借款id||
|userName|√|借款人||
|productName|√|金融产品||
|createTime|√|申请时间||
|borrowAmount|√|借款金额||
|borrowArrange|√|借款期限||
|status|√|状态||


### 实例
#### 请求实体


#### 响应参数
```json
{
    "msg": "success",
    "code": 200,
    "data": {
        "total": 1,
        "list": [
            {
                "borrowId": "15162546185842306333",
                "userName": "张三",
                "productName": "王者理财方案",
                "createTime": "2018-01-18 13:49:03",
                "borrowAmount": 6000000,
                "borrowArrange": 12,
                "status": "1",
                "investigate": true  
            }
        ],
        "pageNum": 1,
        "pageSize": 10,
        "pages": 1,
        "size": 1
    }
}
```

### 数据库操作  
```sql
select
  borrow_id as borrowId,
  (select user_name from gguser where user_id = gb.user_id) as userName,
  (select product_name from ggproduct where product_id = gb.product_id) as productName,
  start_date as startDate,
  borrow_amount as borrowAmount,
  borrow_arrange as borrowArrange,
  status as status
from guborrowmoney gb where status = 'type'  -- type 1:待审核 或者 2:审核不通过
and user_id in (select user_id from gguser where user_name like concat('%', 'userName', '%'));
```
------------------

## 添加企业尽调

#### 基本信息
* 请求类型：`HTTP`
* 请求地址：`/sys/bank/{borrowId}/guduediligence`
* 请求方式：`POST`
* 请求类型：`JSON`
* 响应类型：`R`

### 接口描述
1. 首先 状态为1：待审核的状态尽调
2. 如果尽调没有与申请关联的尽调信息责尽调
3. 如果有关联的尽调信息 责审贷

#### 请求数据

|参数名称|是否必填|类型|长度|描述|默认值|备注|
|:----:|:----:|:----:|:----:|:---:|:----:|:---:|
|token|√|String||验证登录详情|||
|pageNum||Integer||分页开始位置|0||
|pageSize||Integer||每页显示数量|10||
|creditTime||Date|  |征信时间||日期控件|
|finishNum| |Integer||  结清笔数|0|正整数|
|loanAmount| |BigDecimal|12|  负债余额|0|金额小数点后2位|
|loanNum| |Integer||负债笔数|0|正整数|
|illegalAmount||BigDecimal|12 |  不良余额|0|金额小数点后2位|
|overdueNum| | Integer| | 逾期笔数|0|正整数|
|months| | Integer| |月份数|0|正整数|
|maxDueMonths| |Integer||  最长逾期月数|0|正整数|
|maxAmountPerMonth||BigDecimal|12|  单月最高逾期总额|0|金额小数点后2位|
|dueReport||File||  尽调报告||File控件|
|remark||String|200|  备注||


#### 响应数据
|参数名称|是否必填|描述|默认值|  
|:----:|:----:|:----:|:----:|  


### 实例
#### 请求实体
```json
{
		"company": {
			"type": "1", 
			"creditTime": "2017-12-28 00:00:00", 
			"finishNum": "10", 
			"loanAmount": "500000", 
			"loanNum": "1", 
			"illegalAmount": "3000", 
			"overdueNum": "1", 
			"months": "3", 
			"maxDueMonths": "4", 
			"maxAmountPerMonth": "60000", 
			"dueReport": null, 
			"remark": null 
			},
		"person": {
			"type": "2", 
			"creditTime": "2017-12-28 00:00:00", 
			"finishNum": "0", 
			"loanAmount": "0", 
			"loanNum": "0", 
			"illegalAmount": "0", 
			"overdueNum": "0", 
			"months": "0", 
			"maxDueMonths": "0", 
			"maxAmountPerMonth": "0", 
			"dueReport": null, 
			"remark": null 
		},
		"remark": null,
		"userId": "15264311168"
	}
```
#### 响应参数
```json
{
	"code": "",
	"message": "",
	"data": 
}
```

### 数据库操作  
```sql
start transaction;

insert into guduedetail (
	`creditTime`,
	`finishNum`,
	`loanAmount`,
	`loanNum`,
	`illegalAmount`,
	`overdueNum`,
	`months`,
	`maxDueMonths`,
	`maxAmountPer_month`,
	`dueCode`)
values (  -- 企业
	'creditTime',   
	'finishNum', 
	'loanAmount', 
	'loanNum', 
	'illegalAmount', 
	'overdueNum', 
	'months', 
	'maxDueMonths', 
	'maxAmountPer_month', 
	'dueCode'),
	( -- 个人
	'creditTime', 
	'finishNum', 
	'loanAmount', 
	'loanNum', 
	'illegalAmount', 
	'overdueNum', 
	'months', 
	'maxDueMonths', 
	'maxAmountPer_month', 
	'dueCode')

insert into guduediligence (
	`due_id`,
	`user_id`,
	`due_code`,
	`due_type`,
	`due_report`,
	`credit_time`,
	`remark`,
	`flag`,
	`contract_id`) 
values (
	'due_id',
	'user_id',
	'due_code',
	'due_type',
	'due_report',
	'credit_time',
	'remark',
	'flag',
	'contract_id') 

commit;
```
------------------

## 审贷

#### 基本信息
* 请求类型：`HTTP`
* 请求地址：`/sys/bank/{borrowId}/audit`
* 请求方式：`PUT`
* 请求类型：`JSON`
* 响应类型：`R`

### 接口描述


#### 请求数据
|参数名称|是否必填|类型|长度|描述|默认值|备注|
|:----:|:----:|:----:|:----:|:---:|:----:|:---:|
|token|√|String||验证登录详情|||
|borrowId| √|String| 32| 需要审核的借款id|||
|auditStatus||String|1 | 审核状态（1待审核，2审核不通过，3审核通过）| |
|remark| |String|200|  备注| |


#### 响应数据
|参数名称|是否必填|描述|默认值|  
|:----:|:----:|:----:|:----:|  


### 实例
#### 请求实体
```json
 {
	"auditId": "15162546185842306333" ,
	"auditStatus": "3" ,
	"remark": "审核通过" 
}
```

#### 响应参数
```json
{
	"code": "201",
	"message": "success",
}
```

### 数据库操作  
```sql
start transaction;

update guborrowmoney set status = 'status' where borrew+id = 'borrewId';

insert into ggauditrecord(
	`audit_id`,
	`series_no`,
	`audit_code`,
	`audit_status`,
	`content`,
	`audit_time`,
	`remark`)
values(
	'audit_id',
	'series_no',
	'audit_code',
	'audit_status',
	'content',
	'audit_time',
	'remark')

commit;	
```
------------------------------------

## 授信签约

#### 基本信息
* 请求类型：`HTTP`
* 请求地址：`/sys/bank/{borrowId}/contract`
* 请求方式：`POST`
* 请求类型：`String`
* 响应类型：`R`

### 接口描述
1. 首先 状态为1：待审核的状态尽调
2. 如果尽调没有与申请关联的尽调信息责尽调
3. 如果有关联的尽调信息 责审贷

#### 请求数据

|参数名称|是否必填|类型|长度|描述|默认值|备注|传参方式|
|:----:|:----:|:----:|:----:|:---:|:----:|:---:|:---:|
|token|√|String||验证登录详情|||Header/Params|
|contractNo||String|20| 借款合同号|
|borrowAmount||String|14| 批贷金额|
|rate||String|4| 月利率|
|interest||String|7| 借款利息|
|startDate||Date|| 借款期限开始|
|endDate||Date|| 借款期限结束|
|contractImg||String|100| 合同电子版|


#### 响应数据
|参数名称|是否必填|描述|默认值|  
|:----:|:----:|:----:|:----:|  


### 实例
#### 请求实体
```json
{
    "contractNo": "",
    "borrowAmount": "",
    "rate": "",
    "interest": "",
    "startDate": "",
    "endDate": "",
    "contractImg": ""
}
```
#### 响应参数
```json
{
	"code": "",
	"message": "",
}
```

### 数据库操作  
```sql
select
	borrow_id,
	(select user_name from gguser where user_id = gb.user_id) as userName,
	(select product_name from ggproduct where product_id = gb.product_id) as productName,
	start_date as createTime,
	borrow_amount as borrowAmount,
	borrow_arrange as borrowArrange,
	status,
	if((select credit_id from ggcredit where be_crediter = gb.user_id and now() > end_date), false, true) as isCredit  -- 是否授信 针对企业查
	from guborrowmoney gb
where status = 3
and user_id in (select user_id from gguser gu where user_name like concat('%', 'borrowName', '%')) -- 模糊查询企业名称
```
------------------

## 授信

#### 基本信息
* 请求类型：`HTTP`
* 请求地址：`/sys/bank/ggcredit/{borrewId}/credit`
* 请求方式：`POST`
* 请求类型：`JSON`
* 响应类型：`R`

### 接口描述


#### 请求数据
|参数名称|是否必填|类型|长度|描述|默认值|备注|
|:----:|:----:|:----:|:----:|:---:|:----:|:---:|
|token|√|String||验证登录详情||Header/Params|
|borrowId| √|String|32| 需要授信的借款id||params|
|creditLevel||String|未定|  信用等级||
|creditAmount||BigDecimal|12|  授信金额||
|creditFile||file|5M|  授信通知书||
|startDate||Date| |  开始日期||yyyy-MM-dd mm:dd:ss|
|endDate||Date||  结束||yyyy-MM-dd mm:dd:ss|
|remark||String|200|  备注||


#### 响应数据
|参数名称|是否必填|描述|默认值|  
|:----:|:----:|:----:|:----:|  


### 实例
#### 请求实体
```json
 {
	"creditLevel": "", 
	"creditAmount": "", 
	"startDate": "", 
	"endDate": "", 
}
```

#### 响应参数
```json
{
	"code": "201",
	"message": "success",
}
```

### 数据库操作  
```sql
update guborrowmoney set status = 'status' where borrew+id = 'borrewId';  -- 是否要更新

insert into ggauditrecord(
	`credit_level`,
	`credit_amount`,
	`credit_file`,
	`start_date`,
	`end_date`,
	`flag`)
values(
	'credit_level',
	'credit_amount',
	'credit_file',
	'start_date',
	'end_date',
	'flag')
```
------------------
## 签约

#### 基本信息
* 请求类型：`HTTP`
* 请求地址：`/sys/bank/{borrowId}/contract`
* 请求方式：`PUT`
* 请求类型：`JSON`
* 响应类型：`R`

### 接口描述


#### 请求数据
|参数名称|是否必填|类型|长度|描述|默认值|备注|
|:----:|:----:|:----:|:----:|:---:|:----:|:---:|
|contractNo||String|String|借款合同号|||
|borrowAmount||BigDecimal|12|借款金额|||
|rate||BigDecimal|5|月利率|||
|interest||BigDecimal|12|借款利息|||
|startDate||Date||借款期限开始||yyyy-MM-dd mm:dd:ss|
|endDate||Date||借款期限结束||yyyy-MM-dd mm:dd:ss|
|remark||String|200|备注|||


#### 响应数据
|参数名称|是否必填|描述|默认值|  
|:----:|:----:|:----:|:----:|  


### 实例
#### 请求实体
```json
{
	"contractNo": "1231654645",
	"borrowAmount": "60000",  		
	"rate": "1.3",  		
	"interest": "3000",  		
	"startDate": "2018-09-09 00:00:00",  		
	"endDate": "2019-09-09 00:00:00",  		
	"remark": "测试备注"
}
```

#### 响应参数
```json
{
    "code": "201",
    "message": "success",
}
```

### 数据库操作  
```sql
update guborrowmoney set contract_no = ?, application_amount = ?, 
rate = ?, interest = ?, bank_id = ?, product_id = ?, user_id = ?,
 borrow_arrange = ?, create_time = ?, start_date = ?, end_date = ?, 
 contract_img = ?, status = ?, remark = ? 
 where borrow_id = ? 

```
------------------
## 放款列表

#### 基本信息
* 请求类型：`HTTP`
* 请求地址：`sys/bank/finish`
* 请求方式：`GET`
* 请求类型：`JSON`
* 响应类型：`R`

### 接口描述


#### 请求数据
|参数名称|是否必填|类型|长度|描述|默认值|备注|
|:----:|:----:|:----:|:----:|:---:|:----:|:---:|


#### 响应数据
|参数名称|是否必填|描述|默认值|  
|:----:|:----:|:----:|:----:|  
|borrowId||申请编号||
|contractNo||借款合同号||
|userName||借款人代码||
|productName||金融产品||
|borrowAmount||借款金额||
|startDate||||
|endDate||||
|status||放贷状态||
|bankStatus||银行审核状态||
|assuranceStatus||担保审核状态||
|agricultureStatus||农委审核状态||


### 实例
#### 请求实体
```json
{

}
```

#### 响应参数
```json
{
    "msg": "success",
    "code": 201,
    "data": {
        "total": 7,
        "list": [
            {
                "borrowId": "15223027537242735477",
                "contractNo": "123123123",
                "userName": "贵州省马大姐食品股份有限公司",
                "productName": null,
                "borrowAmount": "123123.00",
                "startDate": "2018-04-07 08:00:00",
                "endDate": "2018-04-07 08:00:00",
                "status": "5",
                "bankStatus": "1",
                "assuranceStatus": null,
                "agricultureStatus": null
            },
            {
                "borrowId": "15214243013584602537",
                "contractNo": "1231322131",
                "userName": "贵州省马大姐食品股份有限公司",
                "productName": "王者理财方案",
                "borrowAmount": "18.00",
                "startDate": "2018-04-06 08:00:00",
                "endDate": "2018-04-13 08:00:00",
                "status": "5",
                "bankStatus": "1",
                "assuranceStatus": null,
                "agricultureStatus": null
            },
            {
                "borrowId": "15169508116357539179",
                "contractNo": "3312312312",
                "userName": "贵州省马大姐食品股份有限公司",
                "productName": null,
                "borrowAmount": "500000.00",
                "startDate": "2018-01-26 15:13:13",
                "endDate": "2018-01-26 15:13:13",
                "status": "5",
                "bankStatus": "3",
                "assuranceStatus": "2",
                "agricultureStatus": "3"
            },
            {
                "borrowId": "15169479458845338537",
                "contractNo": "3612312312",
                "userName": "贵州省马大姐食品股份有限公司",
                "productName": null,
                "borrowAmount": "6000000.00",
                "startDate": "2018-01-26 14:25:27",
                "endDate": "2018-01-26 14:25:27",
                "status": "5",
                "bankStatus": "3",
                "assuranceStatus": "2",
                "agricultureStatus": null
            },
            {
                "borrowId": "15168634611042822827",
                "contractNo": "3512312312",
                "userName": "贵州省马大姐食品股份有限公司",
                "productName": null,
                "borrowAmount": "6000000.00",
                "startDate": "2018-01-25 14:57:24",
                "endDate": "2018-01-25 14:57:24",
                "status": "5",
                "bankStatus": "2",
                "assuranceStatus": "1",
                "agricultureStatus": "1"
            },
            {
                "borrowId": "15162430025263970647",
                "contractNo": "1231654645",
                "userName": "贵州省马大姐食品股份有限公司",
                "productName": "王者理财方案",
                "borrowAmount": "15.00",
                "startDate": "2018-01-18 10:35:27",
                "endDate": "2018-01-18 10:35:27",
                "status": "5",
                "bankStatus": "3",
                "assuranceStatus": null,
                "agricultureStatus": null
            },
            {
                "borrowId": "15157283274969297373",
                "contractNo": "3112312312",
                "userName": "贵州省马大姐食品股份有限公司",
                "productName": "王者理财方案",
                "borrowAmount": "10.00",
                "startDate": "2018-01-18 10:38:58",
                "endDate": "2018-01-18 10:38:58",
                "status": "5",
                "bankStatus": "1",
                "assuranceStatus": null,
                "agricultureStatus": null
            }
        ],
        "pageNum": 1,
        "pageSize": 10,
        "pages": 1,
        "size": 7
    }
}
```

------------------
## 审贷

#### 基本信息
* 请求类型：`HTTP`
* 请求地址：`{borrowId}/audit`
* 请求方式：`PUT`
* 请求类型：`JSON`
* 响应类型：`R`

### 接口描述


#### 请求数据
|参数名称|是否必填|类型|长度|描述|默认值|备注|
|:----:|:----:|:----:|:----:|:---:|:----:|:---:|
|token|√|String|| 登录验证|||
|auditId|√|String|32| 审核对象|||
|auditStatus|√|String|1| 审核状态||1,审核通过并通知 2,审核不通过 3,审核通过|
|remark|√|String|200| 备注信息|||


#### 响应数据
|参数名称|是否必填|描述|默认值|  
|:----:|:----:|:----:|:----:|  


### 实例
#### 请求实体
```json
 {
	"auditId": "15162432129967426183" ,
	"auditStatus": "1" ,
	"remark": "审核通过不通知" 
}
```

#### 响应参数
```json
{
    "code": "201",
    "message": "success",
}
```

### 数据库操作  
```sql
start transaction;

update guapplymoney set 
contract_no = ?, accept_name = ?, bank = ?, account_no = ?, amount = ?, user_id = ?, 
bank_status = ?, assurance_status = ?, insurance_status = ?, agriculture_status = ?, 
input_time = ?, updator = ?, update_time = ?, remark = ?, flag = ? 
where apply_id = ? 


insert into ggauditrecord (
	audit_id, series_no, audit_code, audit_status, content, audit_time, flag, remark
) values (?, ?, ?, ?, ?, ?, ?, ?) 

commit;
```
------------------
## 借款申请列表

#### 基本信息
* 请求类型：`HTTP`
* 请求地址：`/app/bank/apply`
* 请求方式：`GET`
* 请求类型：``
* 响应类型：`R`

### 接口描述


#### 请求数据
|参数名称|是否必填|类型|长度|描述|默认值|备注|
|:----:|:----:|:----:|:----:|:---:|:----:|:---:|
|token|√|String|| 登录验证|||
|status||String|1| 状态||1待审核 2未通过 4待签约|
|borrowName||String|32|申请企业名称||模糊查询|
|pageSize||Integer||分页开始||0||
|pageSize||Integer||每页显示条数||10||


#### 响应数据
|参数名称|是否必填|描述|默认值|  
|:----:|:----:|:----:|:----:|  
|borrowId||  借款编号||
|userName||  借款人||
|productName||  金融产品||
|startDate||  申请时间||
|borrowAmount||  借款金额||
|status||  状态||
|borrowArrange||  借款期限||

### 实例
#### 请求实体
```json
 {
}
```

#### 响应参数
```json
{
    "msg": "success",
    "code": 200,
    "data": {
        "total": 120,
        "list": [
            {
                "borrowId": "15227476318932515770",
                "userName": "贵州省马大姐食品股份有限公司",
                "productName": "订单融资贷款2-兴义农商行",
                "startDate": "2018-04-03 17:24:03",
                "borrowAmount": 12312,
                "status": "1",
                "borrowArrange": "2"
            },
            {
                "borrowId": "15227352236081416078",
                "userName": "贵州省马大姐食品股份有限公司",
                "productName": "流动资金贷款2-兴义农商行",
                "startDate": "2018-04-03 13:57:15",
                "borrowAmount": 123123,
                "status": "1",
                "borrowArrange": "2"
            },
            {
                "borrowId": "15227255136148457166",
                "userName": "贵州省马大姐食品股份有限公司",
                "productName": "订单融资贷款2-兴义农商行",
                "startDate": "2018-04-03 11:15:26",
                "borrowAmount": 12312312,
                "status": "1",
                "borrowArrange": "1"
            },
            {
                "borrowId": "15226650163333965797",
                "userName": "贵州省马大姐食品股份有限公司",
                "productName": "订单融资贷款2-兴义农商行",
                "startDate": "2018-04-02 18:27:13",
                "borrowAmount": 12123123,
                "status": "1",
                "borrowArrange": "1"
            },
            {
                "borrowId": "15220303940341997476",
                "userName": "贵州省马大姐食品股份有限公司",
                "productName": null,
                "startDate": "2018-03-26 10:09:54",
                "borrowAmount": 3513121321,
                "status": "2",
                "borrowArrange": "1"
            },
            {
                "borrowId": "15217864683358201656",
                "userName": "贵州省马大姐食品股份有限公司",
                "productName": null,
                "startDate": "2018-03-23 14:24:43",
                "borrowAmount": 12312000000,
                "status": "1",
                "borrowArrange": "1"
            },
            {
                "borrowId": "15217139334021290617",
                "userName": "贵州省马大姐食品股份有限公司",
                "productName": null,
                "startDate": "2018-03-22 18:16:28",
                "borrowAmount": null,
                "status": "1",
                "borrowArrange": "1"
            },
            {
                "borrowId": "15217138603753380340",
                "userName": "贵州省马大姐食品股份有限公司",
                "productName": null,
                "startDate": "2018-03-22 18:15:16",
                "borrowAmount": null,
                "status": "1",
                "borrowArrange": "1"
            },
            {
                "borrowId": "15216304959576300440",
                "userName": "贵州省马大姐食品股份有限公司",
                "productName": "借款履约保证保险",
                "startDate": "2018-03-21 19:08:51",
                "borrowAmount": null,
                "status": "1",
                "borrowArrange": "1"
            },
            {
                "borrowId": "15216265330083401216",
                "userName": "贵州省马大姐食品股份有限公司",
                "productName": "借款履约保证保险",
                "startDate": "2018-03-21 18:02:04",
                "borrowAmount": null,
                "status": "1",
                "borrowArrange": "1"
            }
        ],
        "pageNum": 1,
        "pageSize": 10,
        "pages": 12,
        "size": 10
    }
}
```

### 数据库操作  
```sql
start transaction;

select
        borrow_id as borrowId,
        contract_no as contractNo,
        (select user_name from gguser where user_id = gb.user_id) as userName,
        (select product_name from ggproduct where product_id = gb.product_id) as productName,
        application_amount as borrowAmount,
        start_date as startDate,
        borrow_arrange as borrowArrange,
        status
        from guborrowmoney gb
where
contract_no is null
        AND status = #{status}
        AND status in ('1', '2', '3')
        and user_id in (select user_id from gguser where user_name like concat('%',#{borrowName},'%'));
commit;
```
------------------
## 借款合同列表

#### 基本信息
* 请求类型：`HTTP`
* 请求地址：`/app/bank/loans`
* 请求方式：`GET`
* 请求类型：``
* 响应类型：`R`

### 接口描述


#### 请求数据
|参数名称|是否必填|类型|长度|描述|默认值|备注|
|:----:|:----:|:----:|:----:|:---:|:----:|:---:|
|token|√|String|| 登录验证|||
|status||String|1| 状态||1待审核 2未通过 4待签约|
|borrowName||String|32|申请企业名称||模糊查询|
|pageSize||Integer||分页开始||0||
|pageSize||Integer||每页显示条数||10||


#### 响应数据
|参数名称|是否必填|描述|默认值|  
|:----:|:----:|:----:|:----:|  
|contractNo| |  合同号 ||
|endDate| |结束日期||
|bankName| |  出借人LoansRecord||
|borrowId| |  借款编号||
|userName| |  借款人||
|productName| |  金融产品||
|startDate| |  申请时间||
|borrowAmount| |  借款金额||
|status| |  状态||

### 实例
#### 请求实体
```json
 {
}
```

#### 响应参数
```json
{
    "msg": "success",
    "code": 200,
    "data": {
        "total": 21,
        "list": [
            {
                "contractNo": "123123123",
                "endDate": "2018-04-07 08:00:00.0",
                "bankName": null,
                "borrowId": "15223027537242735477",
                "userName": "贵州省马大姐食品股份有限公司",
                "productName": null,
                "startDate": "2018-04-07 08:00:00",
                "borrowAmount": 123123,
                "approvedAmount": 123123,
                "status": "5"
            },
            {
                "contractNo": "1231322131",
                "endDate": "2018-04-13 08:00:00.0",
                "bankName": "交通银行",
                "borrowId": "15214243013584602537",
                "userName": "贵州省马大姐食品股份有限公司",
                "productName": "王者理财方案",
                "startDate": "2018-04-06 08:00:00",
                "borrowAmount": 18,
                "approvedAmount": 18,
                "status": "5"
            },
            {
                "contractNo": "12312312312",
                "endDate": "2018-04-26 08:00:00.0",
                "bankName": "交通银行",
                "borrowId": "15214243013583626990",
                "userName": "贵州省马大姐食品股份有限公司",
                "productName": "王者理财方案",
                "startDate": "2018-04-04 08:00:00",
                "borrowAmount": 16,
                "approvedAmount": 16,
                "status": "4"
            },
            {
                "contractNo": "3162312312",
                "endDate": "2018-03-12 17:10:56.0",
                "bankName": "交通银行",
                "borrowId": "15208459705748525321",
                "userName": "贵州省马大姐食品股份有限公司",
                "productName": "借款申请产品",
                "startDate": "2018-03-12 17:10:56",
                "borrowAmount": 555500,
                "approvedAmount": 555500,
                "status": "3"
            },
            {
                "contractNo": "3152312312",
                "endDate": "2018-03-12 17:02:43.0",
                "bankName": "交通银行",
                "borrowId": "15208454771546481507",
                "userName": "贵州省马大姐食品股份有限公司",
                "productName": "借款申请产品",
                "startDate": "2018-03-12 17:02:43",
                "borrowAmount": 111111111111,
                "approvedAmount": null,
                "status": "6"
            },
            {
                "contractNo": "3142312312",
                "endDate": "2018-03-12 16:56:54.0",
                "bankName": "交通银行",
                "borrowId": "15208451280001780996",
                "userName": "贵州省马大姐食品股份有限公司",
                "productName": "借款申请产品",
                "startDate": "2018-03-12 16:56:54",
                "borrowAmount": 123123,
                "approvedAmount": null,
                "status": "6"
            },
            {
                "contractNo": "3132312312",
                "endDate": "2018-01-26 15:13:13.0",
                "bankName": "工商银行",
                "borrowId": "15169508116357539179",
                "userName": "贵州省马大姐食品股份有限公司",
                "productName": null,
                "startDate": "2018-01-26 15:13:13",
                "borrowAmount": 500000,
                "approvedAmount": null,
                "status": "5"
            },
            {
                "contractNo": "3122312312",
                "endDate": "2018-01-26 15:13:12.0",
                "bankName": "工商银行",
                "borrowId": "15169508106372877071",
                "userName": "贵州省马大姐食品股份有限公司",
                "productName": null,
                "startDate": "2018-01-26 15:13:12",
                "borrowAmount": 666666,
                "approvedAmount": 666666,
                "status": "4"
            },
            {
                "contractNo": "3112312312",
                "endDate": "2018-01-26 15:13:09.0",
                "bankName": "工商银行",
                "borrowId": "15169508075017460594",
                "userName": "贵州省马大姐食品股份有限公司",
                "productName": null,
                "startDate": "2018-01-26 15:13:09",
                "borrowAmount": 666666,
                "approvedAmount": 666666,
                "status": "3"
            },
            {
                "contractNo": "3102312312",
                "endDate": "2018-01-26 15:10:03.0",
                "bankName": "工商银行",
                "borrowId": "15169506219702616984",
                "userName": "贵州省马大姐食品股份有限公司",
                "productName": null,
                "startDate": "2018-01-26 15:10:03",
                "borrowAmount": 6000000,
                "approvedAmount": 6000000,
                "status": "3"
            }
        ],
        "pageNum": 1,
        "pageSize": 10,
        "pages": 3,
        "size": 10
    }
}
```

### 数据库操作  
```sql
select
        borrow_id as borrowId,
        contract_no as contractNo,
        (select organ_name from ggorgan where organ_id = gb.bank_id) as bankName,
        (select user_name from gguser where user_id = gb.user_id) as userName,
        (select product_name from ggproduct where product_id = gb.product_id) as productName,
        application_amount as borrowAmount,
        approved_amount as approvedAmount,
        start_date as startDate,
        end_date as endDate,
        status
from guborrowmoney gb
where
	contract_no is not null
	AND status = #{status}
	AND status in ('2', '3', '4', '5')
	and user_id in (select user_id from gguser where user_name like concat('%',#{borrowName},'%'));
```
------------------
## 根据id查询借款详情

#### 基本信息
* 请求类型：`HTTP`
* 请求地址：`/sys/bank/{borrowId}/borrow`
* 请求方式：`GET`
* 请求类型：`String`
* 响应类型：`R`

### 接口描述


#### 请求数据
|参数名称|是否必填|类型|长度|描述|默认值|备注|
|:----:|:----:|:----:|:----:|:---:|:----:|:---:|
|token|√|String|| 登录验证|||
|borrowId|√|String||查询id|

#### 响应数据
|参数名称|是否必填|描述|默认值|  
|:----:|:----:|:----:|:----:|  
|bankId| √ | 银行||
|productId| √ | 产品||
|rate| √ | 利率||
|borrowAmount| √ | 借款金额||
|remark|  | 备注||
|letter|  | 推荐函||
|policyMain|  | 保险||
|assurance|  | 担保||
|managerCards|  | 经营证照||
|contracts|  | 购销合同||
|financials|  | 财务报表||
|invoices|  | 发票列表||
|assets|  | 资产列表||
|images|  | 其他资料||
|cardId|| 资证id||
|cardName|| 证件名称||
|companyName|| 公司名称||
|cardType|| 类型||
|cardNo|| 证件编号||
|endDate|| 证件有效期||
|remark|| 备注||
|contractId|| 编码||
|partnerId|| 合作对象||
|contractType|| 合作关系||
|startDate|| 合作开始日期||
|endDate|| 开做结束日期||
|contractAmount|| 合同金额||
|linkType|| 联系电话||
|id|| 报表id||
|chartTime|| 年,月||
|remark|| 备注||
|inputTime|| 创建时间||
|contractId||编号||
|invoiceCode||发票代码||
|invoiceNo||发票号码||
|invoiceAmount||发票金额||
|createDate||开票日期||
|invoiceHeader||发票抬头||
|remark||备注||||
|assetId|| 编号||
|assetHolder|| 产权人||
|assetType|| 产权关系||
|assetNature||  产权性质||
|province||省||
|city||市||
|county||区||
|town||城镇||
|village||街道||
|address||详细地址||
|area||  面积||
|futurePrice|| 预估值||
|mortgageAmount|| 抵押金额||
|netWorth|| 净值||

### 实例
#### 请求实体
```json
```
#### 响应参数
```json
{
    "msg": "success",
    "code": 200,
    "data": {
        "bankId": "15161843217798364881",
        "productId": null,
        "rate": 1.5,
        "borrowAmount": null,
        "remark": "asdfadfasd",
        "letter": {
            "letterId": "201801200001",
            "referrer": "nongwei",
            "accepter": "123123",
            "startDate": "2018-01-20 00:00:00",
            "endDate": "2018-02-20 00:00:00",
            "isTakeEffect": "无效"
        },
        "policyMain": {
            "businessNo": "15155516905141164280",
            "riskCode": "1234",
            "insureAppli": "15264311168",
            "insuredCode": "c0a86a52c2ae44b4979a5ca8",
            "amount": 2000000,
            "status": "5"
        },
        "assurance": {
            "assureId": "654780",
            "productType": "4567",
            "userName": "15264311168",
            "assureCompany": "456456",
            "assureAmount": 20000,
            "status": "4"
        },
        "managerCards": [
            {
                "cardId": "15160118706999703465",
                "cardName": "营业执照",
                "companyName": "贵州省马大姐食品股份有限公司",
                "cardType": "01",
                "cardNo": "1234587",
                "endDate": "2017-12-31 00:00:00",
                "remark": "备注"
            }
        ],
        "contracts": [
            {
                "contractId": "15160133073853215176",
                "partnerId": "撒打算",
                "contractType": "供应商",
                "startDate": null,
                "endDate": null,
                "contractAmount": null,
                "linkType": ""
            }
        ],
        "financials": [
            {
                "id": "",
                "chartTime": "",
                "remark": "",
                "inputTime": ""
            },
            {
                "id": "",
                "chartTime": "",
                "remark": "",
                "inputTime": ""
            }
        ],
        "invoices": [
            {
                "contractId":"", 
                "invoiceCode":"", 
                "invoiceNo":"", 
                "invoiceAmount":"", 
                "createDate":"", 
                "invoiceHeader":"", 
                "remark":""
            },
            {
                "contractId":"", 
                "invoiceCode":"", 
                "invoiceNo":"", 
                "invoiceAmount":"", 
                "createDate":"", 
                "invoiceHeader":"", 
                "remark":""
            }
        
        ],
        "assets": [
            {
                "assetId": "",
                "assetHolder": "",
                "assetType": "",
                "assetNature": "",
                "province": "",
                "city": "",
                "county": "",
                "town": "",
                "village": "",
                "address": "",
                "area": "",
                "futurePrice": "",
                "mortgageAmount": "",
                "netWorth": ""
            },
            {
                "assetId": "",
                "assetHolder": "",
                "assetType": "",
                "assetNature": "",
                "province": "",
                "city": "",
                "county": "",
                "town": "",
                "village": "",
                "address": "",
                "area": "",
                "futurePrice": "",
                "mortgageAmount": "",
                "netWorth": ""
            }
        ],
        "images": {
            "identity": "789795"
        }
    }
}
```

### 数据库操作  
```sql
start transaction;
select 
	borrow_id
	contract_no,
	borrow_amount,
	rate,
	bank_id,
	product_id,
	user_id,
	borrow_arrange,
	create_time,
	start_date,
	end_date,
	letter_id,
	policy_id,
	assurance_id,
	contract_img,
	cert_id,
	status,
	record_address,
	updator,
	update_time,
	remark,
	flag 
from 
	guborrowmoney 
where borrow_id = '1234567899867564'

select * from ggmanagercard where id in ("132312312","123123124236", "1234543534");  -- 资政表
select * from ggcontract where id in ("132312312","123123124236", "1234543534");  -- 合同
select * from ggfinancial where id in ("132312312","123123124236", "1234543534");  -- 财务报表
select * from gginvoice where id in ("132312312","123123124236", "1234543534");  -- 发票
select * from ggasset where id in ("132312312","123123124236", "1234543534");  -- 资产

commit;
```


## 待放款列表

#### 基本信息
* 请求类型：`HTTP`
* 请求地址：`/app/bank/finish`
* 请求方式：`GET`
* 请求类型：`int`
* 响应类型：`R`

### 接口描述


#### 请求数据

|参数名称|是否必填|类型|长度|描述|默认值|备注|
|:----:|:----:|:----:|:----:|:---:|:---:|:---:|
|token|√|String||验证登录详情||
|pageNum||Integer||分页开始位置|0|整数|
|pageSize||Integer||每页显示数量|10|整数|

#### 响应数据
|参数名称|是否必填|描述|说明| 
|:----:|:----:|:----:|:----:|  
|borrowId| | 申请编号|
|contractNo| | 借款合同号|
|userName| | 借款人代码|
|productName| | 金融产品|
|borrowAmount| | 借款金额|
|startDate| ||
|endDate| ||
|status| | 0:草稿,1:待审核,2:审核不通过,3:审核通过并生成合同|

### 实例
#### 请求实体

#### 响应参数
```json
{
    "msg": "success",
    "code": 201,
    "data": {
        "total": 7,
        "list": [
            {
                "borrowId": "15223027537242735477",
                "contractNo": "123123123",
                "userName": "贵州省马大姐食品股份有限公司",
                "productName": null,
                "borrowAmount": "123123.00",
                "startDate": "2018-04-07 08:00:00",
                "endDate": "2018-04-07 08:00:00",
                "status": "5",
                "bankStatus": "1",
                "assuranceStatus": null,
                "agricultureStatus": null
            },
            {
                "borrowId": "15214243013584602537",
                "contractNo": "1231322131",
                "userName": "贵州省马大姐食品股份有限公司",
                "productName": "王者理财方案",
                "borrowAmount": "18.00",
                "startDate": "2018-04-06 08:00:00",
                "endDate": "2018-04-13 08:00:00",
                "status": "5",
                "bankStatus": "1",
                "assuranceStatus": null,
                "agricultureStatus": null
            },
            {
                "borrowId": "15169508116357539179",
                "contractNo": "3312312312",
                "userName": "贵州省马大姐食品股份有限公司",
                "productName": null,
                "borrowAmount": "500000.00",
                "startDate": "2018-01-26 15:13:13",
                "endDate": "2018-01-26 15:13:13",
                "status": "5",
                "bankStatus": "3",
                "assuranceStatus": "2",
                "agricultureStatus": "3"
            },
            {
                "borrowId": "15169479458845338537",
                "contractNo": "3612312312",
                "userName": "贵州省马大姐食品股份有限公司",
                "productName": null,
                "borrowAmount": "6000000.00",
                "startDate": "2018-01-26 14:25:27",
                "endDate": "2018-01-26 14:25:27",
                "status": "5",
                "bankStatus": "3",
                "assuranceStatus": "2",
                "agricultureStatus": null
            },
            {
                "borrowId": "15168634611042822827",
                "contractNo": "3512312312",
                "userName": "贵州省马大姐食品股份有限公司",
                "productName": null,
                "borrowAmount": "6000000.00",
                "startDate": "2018-01-25 14:57:24",
                "endDate": "2018-01-25 14:57:24",
                "status": "5",
                "bankStatus": "2",
                "assuranceStatus": "1",
                "agricultureStatus": "1"
            },
            {
                "borrowId": "15162430025263970647",
                "contractNo": "1231654645",
                "userName": "贵州省马大姐食品股份有限公司",
                "productName": "王者理财方案",
                "borrowAmount": "15.00",
                "startDate": "2018-01-18 10:35:27",
                "endDate": "2018-01-18 10:35:27",
                "status": "5",
                "bankStatus": "3",
                "assuranceStatus": null,
                "agricultureStatus": null
            },
            {
                "borrowId": "15157283274969297373",
                "contractNo": "3112312312",
                "userName": "贵州省马大姐食品股份有限公司",
                "productName": "王者理财方案",
                "borrowAmount": "10.00",
                "startDate": "2018-01-18 10:38:58",
                "endDate": "2018-01-18 10:38:58",
                "status": "5",
                "bankStatus": "1",
                "assuranceStatus": null,
                "agricultureStatus": null
            }
        ],
        "pageNum": 1,
        "pageSize": 10,
        "pages": 1,
        "size": 7
    }
}
```

### 数据库操作  
```sql
{
    "msg": "success",
    "date": {
        "total": 0,
        "list": [
            "borrowId": "", 
            "contractNo": "", 
            "userName": "", 
            "productName": "", 
            "borrowAmount": "", 
            "startDate": "", 
            "endDate": "", 
            "status": ""
        ],
        "pageNum": 0,
        "pageSize": 10,
        "pages": 0,
        "size": 0
    },
    "code": 200
}
```
-----------------------

## 根据id查询借款合同详情

#### 基本信息
* 请求协议：`HTTP`
* 请求地址：`/sys/bank/{borrowId}/compact`
* 请求方式：`GET`
* 请求类型：`String`
* 响应类型：`R`

### 接口描述
根据企业id 查询借款合同
并加载相关：
- 收款信息
- 推荐函
- 保险
- 担保

#### 请求数据

|参数名称|是否必填|类型|长度|描述|默认值|备注|
|:----:|:----:|:----:|:----:|:---:|:----:|:---:|
|token|√|String||验证登录详情|
|borrowId|√|String|32|申请借款id|

#### 响应数据
|参数名称|是否必填|描述|默认值|  
|:----:|:----:|:----:|:----:|  
| contractNo| √ | 借款合同号 ||
| borrowId| √   | 申请编号  ||
| borrowAmount| √   | 借款金额  ||
| rate| √   | 利率    ||
| interest| √   | 借款利息  ||
| borrowArrange| √  | 借款期限  ||
| status| √ | 状态    ||
| createTime| √ | 创建时间  ||
| contractImg| √    | 合同图片  ||
| payType| √ | 还款方式 ||
| remark|   | 备注    ||
| acceptName| √ | 收款人   ||
| bank| √   | 开户银行  ||
| accountNo| √  | 账号    ||
| amount| √ | 放款金额  ||
| letterId| √   | 推荐函   ||
| policyId| √   | 保险    ||
| proposalNo| √ | 保单号   ||
| acceptanceStartTime| √    | 承保开始时间    ||
| acceptanceEndTime| √  | 承保结束时间    ||
| assuranceId| √    | 担保    ||
| notifierType | | 通知人类型 ||
| payImg | | 放款凭证 ||
| createTime | | 通知时间 ||

### 实例
#### 请求实体
```json
{

}
```
#### 响应参数
```json
{
    "msg": "success",
    "code": 200,
    "data": {
        "contractNo": "123",
        "borrowId": "15154979522381392010",
        "borrowAmount": 500000,
        "rate": 2.17,
        "interest": 1085000,
        "borrowArrange": 11,
        "status": "0",
        "createTime": "2018-01-11 16:01:35",
        "contractImg": null,
        "payType": "15154930825584651576",
        "remark": "asdfadfasd",
        "acceptName": "15154930825605853160",
        "bank": null,
        "accountNo": null,
        "amount": null,
        "letterId": "15154930825603998372",
        "policyId": "15154930825604840510",
        "proposalNo": null,
        "acceptanceStartTime": null,
        "acceptanceEndTime": null,
        "assuranceId": "15154930825605853160",
        "payNotices": [
            {
                "notifierType": "",
                "payImg": "",
                "createTime": ""
            },
            {
                "notifierType": "",
                "payImg": "",
                "createTime": ""
            }
        ]
    }
}
```

### 数据库操作  
```sql
select 
    contract_no as contractNo
    borrow_id as borrowId
    borrow_amount as borrowAmount
    rate as rate
    FORMAT((borrow_amount*rate),2) as interest 
    borrow_arrange as borrowArrange
    status
    create_time as createTime
    contract_img as contractImg
    (select pay_type from ggproductdetail where product_id = product_id) as payType
    letter_id as letterId
    policy_id as policyId
    assurance_id as assuranceId
    remark
from guborrowmoney
where borrow_id = 'borrowId'  

-- 根据 查询响应数据拼接
letter_id
policy_id
assurance_id 
```
------------------------

