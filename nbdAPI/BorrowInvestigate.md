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

## 尽调审贷列表

#### 基本信息
* 请求类型：`HTTP`
* 请求地址：`http://127.0.0.1:8080/bank/{type}/investigate`
* 请求方式：`GET`
* 请求类型：`int`
* 响应类型：`R`

### 接口描述

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

## 尽调

#### 基本信息
* 请求类型：`HTTP`
* 请求地址：`http://127.0.0.1:8080/app/guduediligence/{borrowId}`
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
* 请求地址：`http://127.0.0.1:8080/app/bank/{borrowId}/audit`
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
* 请求地址：`http://127.0.0.1:8080/guborrowmoneys/contract`
* 请求方式：`GET`
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
|pageNum||Integer||分页开始位置|0||Params|
|pageSize||Integer||每页显示数量|10||Params|
|borrowName||String|20|申请企业名称||模糊查询|Params|



#### 响应数据
|参数名称|是否必填|描述|默认值|  
|:----:|:----:|:----:|:----:|  


### 实例
#### 请求实体

#### 响应参数
```json
{
	"code": "",
	"message": "",
	"data": {
		"total": 1,
        "list": [
            {
                "borrowId": "15157238340051392993",
                "userName": "张三",
                "productName": null,
                "createTime": "2018-01-12 10:23:50",
                "borrowAmount": 6000000,
                "borrowArrange": 12,
                "status": "3",
                "isCredit": false
            },{
                "borrowId": "15157238340051392993",
                "userName": "张三",
                "productName": null,
                "createTime": "2018-01-12 10:23:50",
                "borrowAmount": 6000000,
                "borrowArrange": 12,
                "status": "3",
                "isCredit": false
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
* 请求地址：`http://127.0.0.1:8080/app/ggcredit/{borrewId}/credit`
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
* 请求地址：`http://127.0.0.1:8080/app/bank/{borrowId}/contract`
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
* 请求地址：`http://127.0.0.1:8080/app/bank/{borrowId}/contract`
* 请求方式：`PUT`
* 请求类型：`JSON`
* 响应类型：`R`

### 接口描述


#### 请求数据
|参数名称|是否必填|类型|长度|描述|默认值|备注|
|:----:|:----:|:----:|:----:|:---:|:----:|:---:|



#### 响应数据
|参数名称|是否必填|描述|默认值|  
|:----:|:----:|:----:|:----:|  


### 实例
#### 请求实体
```json
{

}
```

#### 响应参数
```json

```

### 数据库操作  
```sql

```
------------------
## 审贷

#### 基本信息
* 请求类型：`HTTP`
* 请求地址：`http://127.0.0.1:8080/app/bank/{borrowId}`
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
* 请求地址：`127.0.0.1:8080/app/bank/apply`
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
* 请求地址：`127.0.0.1:8080/app/bank/apply`
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
