# 申请借款 **个人中心**  
####  [查询(申请)列表](#查询申请列表)  
####  [查询(合同)列表](#查询合同列表)  
####  [增加申请借款](#保存申请借款)  
####  [修改申请](#修改借款申请)  
####  [申请放款](#根据id申请放款)  
####  [查询借款(合同)](#根据id查询借款合同)  
####  [查询借款(申请)](#根据id查询借款申请)  
--------------

## 查询申请列表

#### 基本信息
* 请求类型：`HTTP`
* 请求地址：`/app/guborrowmoneys/apply`
* 请求方式：`GET`
* 请求类型：`int`
* 响应类型：`R`

### 接口描述
> 根据企业id 查询所有借款申请列表
> 状态为申请范围内

#### 请求数据

|参数名称|是否必填|类型|长度|描述|默认值|备注|
|:----:|:----:|:----:|:----:|:---:|:---:|:---:|
|token|√|String||验证登录详情||
|pageNum||Integer||分页开始位置|0|整数|
|pageSize||Integer||每页显示数量|10|整数|

#### 响应数据
|参数名称|是否必填|描述|说明| 
|:----:|:----:|:----:|:----:|  
|borrowId|√|申请编号||
|userName|√|借款人||
|createTime|√|创建时间||
|borrowAmount|√|借款金额||
|borrowArrange|√|借款期限||
|status|√|状态|0:草稿,1:待审核,2:审核不通过,3:审核通过并生成合同|

### 实例
#### 请求实体

#### 响应参数
```json
{
    "msg": "success",
    "code": 200,
    "data": {
        "total": 4,
        "list": [
            {
                "borrowId": "15169506077438136950",
                "userName": "贵州省马大姐食品股份有限公司",
                "createTime": "2018-01-26 15:10:07",
                "borrowAmount": null,
                "borrowArrange": 12,
                "status": "0"
            },
            {
                "borrowId": "15169506219702616984",
                "userName": "贵州省马大姐食品股份有限公司",
                "createTime": "2018-01-26 15:10:21",
                "borrowAmount": null,
                "borrowArrange": 12,
                "status": "0"
            },
            {
                "borrowId": "15169508075017460594",
                "userName": "贵州省马大姐食品股份有限公司",
                "createTime": "2018-01-26 15:13:27",
                "borrowAmount": null,
                "borrowArrange": 12,
                "status": "0"
            },
            {
                "borrowId": "15169508106372877071",
                "userName": "贵州省马大姐食品股份有限公司",
                "createTime": "2018-01-26 15:13:30",
                "borrowAmount": null,
                "borrowArrange": 12,
                "status": "0"
            }
        ],
        "pageNum": 1,
        "pageSize": 10,
        "pages": 1,
        "size": 4
    }
}
```

### 数据库操作  
```sql
select
        borrow_id AS borrowId,
            application_amount as approvedAmount,
            (select gu.user_name from gguser gu where gu.user_id = gb.user_id) as userName,
            borrow_arrange as borrowArrange,
            approved_amount as approvedAmount,
            create_time as createTime,
            status as status,
            record_address
        from guborrowmoney gb
        where user_id = #{userId} -- 查询 本用户下
        and contract_no is null
        and status in ('0', '1', '2', '3') -- 状态为 2.审核不通过（合同） 3审核通过并生成合同,4申请放款,5放款完成
        order by borrow_id
```
------------------

## 查询合同列表

#### 基本信息
* 请求类型：`HTTP`
* 请求地址：`/app/guborrowmoneys/compact`
* 请求方式：`GET`
* 请求类型：`int`
* 响应类型：`R`

### 接口描述
根据企业id 查询所有借款合同列表
并且状态在合同范围内

#### 请求数据

|参数名称|是否必填|类型|长度|描述|默认值|备注|传参方式|
|:----:|:----:|:----:|:----:|:---:|:----:|:---:|:---:|
|token|√|String||验证登录详情|||Header/Params||
|pageNum||Integer||分页开始位置|0|整数|Params||
|pageSize||Integer||每页显示数量|10|整数|Params||

#### 响应数据
|参数名称|是否必填|描述|说明|  
|:----:|:----:|:----:|:----:|  
|borrowId|√|申请编号||
|contractNo|√|借款合同号||
|userName|√|借款人||
|bankName|√|出借人||
|createTime|√|创建时间||
|borrowAmount|√|借款金额||
|rate|√|月利率||
|borrowArrange|√|借款期限||
|status|√|状态|2:审核不通过,3:审核通过并生成合同,4:申请放款,5:放款完成|

### 实例
#### 请求实体

#### 响应参数
```json
{
    "msg": "success",
    "code": 200,
    "data": {
        "total": 3,
        "list": [
            {
                "borrowId": "15162430025263970647",
                "contractNo": "1231654645",
                "userName": "贵州省马大姐食品股份有限公司",
                "bankName": "交通银行",
                "createTime": "2018-01-18 10:36:42",
                "borrowAmount": null,
                "approvedAmount": 6000000,
                "rate": 2.17,
                "borrowArrange": 12,
                "status": "4"
            },
            {
                "borrowId": "15162432129967426182",
                "contractNo": "1231654645",
                "userName": "贵州省马大姐食品股份有限公司",
                "bankName": "交通银行",
                "createTime": "2018-01-18 10:40:12",
                "borrowAmount": null,
                "approvedAmount": 5000000,
                "rate": 2.17,
                "borrowArrange": 8,
                "status": "4"
            },
            {
                "borrowId": "15162432129967426183",
                "contractNo": "1231654645",
                "userName": "贵州省马大姐食品股份有限公司",
                "bankName": "工商银行",
                "createTime": "2018-01-18 10:40:12",
                "borrowAmount": null,
                "approvedAmount": 5000000,
                "rate": 2.17,
                "borrowArrange": 8,
                "status": "4"
            }
        ],
        "pageNum": 1,
        "pageSize": 10,
        "pages": 1,
        "size": 3
    }
}
```

### 数据库操作  
```sql
select
    borrow_id AS borrowId,
    contract_no as contractNo,
    borrow_amount as borrowAmount,
    rate as rate,
    (select go.organ_name from ggorgan go where go.organ_id = gb.bank_id) as bankName,
    product_id,
    (select gu.user_name from gguser gu where gu.user_id = gb.user_id) as userName,
    borrow_arrange as borrowArrange,
    create_time as createTime,
    status as status,
    record_address
from guborrowmoney gb
where user_id = 'userId'
and contract_no is not null  -- 查询合同号不能为 null 的
and status in ('2', 3', '4', '5') -- 状态为 2.审核不通过（合同） 3审核通过并生成合同,4申请放款,5放款完成
order by borrow_id
```  
---------------------

## 保存申请借款

#### 基本信息
* 请求类型：`HTTP`
* 请求地址：`/app/guborrowmoneys/apply`
* 请求方式：`POST`
* 请求类型：`JSON`
* 响应类型：`R`

### 接口描述
保存借款  
1. 根据type 判断是临时保存还是添加  
2. 如果是临时保存 status 0  
3. 如果是用户点击保存 status 1 待审核  
4. 保存草稿    
	-  关联所有证书处    
5. 如果其他资料表中存在数据则更新。否则添加

#### 请求数据
|参数名称|是否必填|类型|长度|描述|默认值|备注|
|:----:|:----:|:----:|:----:|:---:|:----:|:---:|
|token|√|String||验证登录详情|||Header/Params||
|bankId|√|String|32|金融机构代码||||
|productId|√|String|32|产品id||||
|rate|√|BigDecimal||产品利率||小数点后保留两位|
|borrowArrange|√|Integer|2|借款期限||正整数|
|borrowAmount|√|BigDecimal|12|借款金额||正整数|
|remark|√|String|200|文本||
|letterId||String| 32|推荐函id||
|policyId||String|32|保险单id||
|assuranceId||String| 32| 担保id||
|status|√|String| 1 |状态||0:草稿, 1:待审核|
|recordAddress||String|200|草稿地址||
|fileName||String|50|图片名称||
|FilePath||String| 200|图片地址|||
|managerCards||String|500|资证||多个资证id连接的字符串|
|contractIds||String|500|合同编号||多个合同编号连接的字符串||
|financialIds||String|500|财务报表编号||多个财务报表编号连接的字符串||
|invoiceIds||String|500|发票||多个财务报表编号连接的字符串||
|assetIds||String|500|资产id||多个资产id连接的字符串||

#### 响应数据
|参数名称|是否必填|描述|默认值|  
|:----:|:----:|:----:|:----:|  

### 实例
#### 请求实体
```json
{
    "address": [
        {
            "fileName": "file1",
            "filePath": "filepaths1"
        },
        {
            "fileName": "file2",
            "filePath": "filepaths2"
        },
        {
            "fileName": "file3",
            "filePath": "filepaths3"
        }
    ],
    "productId": "15154930825584651576",
    "contractIds": "12321;34534645;234124;6746;1324,",
    "assetIds": "12321342314;12341234;123421354;1234123",
    "managerCards": "123;123123;234534;457;47",
    "remark": "asdfadfasd",
    "bankId": "15154930825582067531",
    "borrowAmount": 6000000,
    "borrowArrange": 12,
    "policyId": "15154930825604840510",
    "recordAddress": "http://localhost:8080/asfdasd",
    "rate": 2.17,
    "letterId": "15154930825603998372",
    "financialIds": "2345;12341234;5764;7612341234",
    "assuranceId": "15154930825605853160",
    "invoiceIds": "12341234;123412452435;12341234;123123",
    "status": "0"
}
```

#### 响应参数
```json
{
	"code": 201,
	"message": "success"
}
```

### 数据库操作  
```sql
start transcation;

insert into guborrowmoney (
	`borrow_id`,  -- 借款id 自动生成
	`borrow_amount`, -- 借款金额
	`rate`, -- 利率
	`bank_id`, 
	`product_id`, 
	`user_id`, -- 当前登录用户id
	`borrow_arrange`, 
	`create_time`, 
	`letter_id`, -- 推荐函id 默认 null
	`policy_id`,  -- 保险单id 默认 null
	`assurance_id`,  -- 担保id 默认 null
	`contract_img`,  -- 合同图片
	`cert_id`, -- 认证id
	`status`,  -- 状态(0,草稿,1待审核,2,审核不通过,3审核通过并生成合同,4申请放款,5放款完成)
	`record_address`, -- 草稿记录地址
	`remark`, 
	`flag`, ) 
values(
	'borrowId', 
	'borrowAmount', 
	'rate', 
	'bankId', 
	'productId', 
	'userId', 
	'borrowArrange', 
	'createTime', 
	'letterId', 
	'policyId', 
	'assuranceId', 
	'contractImg', 
	'certId', 
	'status', 
	'recordAddress', 
	'remark', 
	'flag') 

insert into guapplydata (
	`apply_id`,
	`manager_cards`,
	`contract_ids`,
	`financial_ids`,
	`invoice_ids`,
	`asset_ids`,
	`other_id`,
	`flag`,
	`remark`)
values (
	'apply_id',
	'manager_cards',
	'contract_ids',
	'financial_ids',
	'invoice_ids',
	'asset_ids',
	'other_id',
	'flag',
	'remark'
)

commit;
```
---------------------

## 修改借款申请

#### 基本信息
* 请求类型：`HTTP`
* 请求地址：`/app/guborrowmoneys/{borrowId}/apply`
* 请求方式：`PUT`
* 请求类型：`JSON`
* 响应类型：`R`

### 接口描述
修改借款申请 
1. 修改借款申请，并关联修改其他资料


#### 请求数据
|参数名称|是否必填|类型|长度|描述|默认值|备注|
|:----:|:----:|:----:|:----:|:---:|:----:|:---:|
|token|√|String||验证登录详情|||Header/Params||
|bankId|√|String|32|金融机构代码||||
|productId|√|String|32|产品id||||
|rate|√|BigDecimal||产品利率||小数点后保留两位|
|borrowArrange|√|Integer|2|借款期限||正整数|
|borrowAmount|√|BigDecimal|12|借款金额||正整数|
|remark|√|String|200|文本||
|letterId||String| 32|推荐函id||
|policyId||String|32|保险单id||
|assuranceId||String| 32| 担保id||
|status|√|String| 1 |状态||0:草稿, 1:待审核|
|recordAddress||String|200|草稿地址||
|fileName||String|50|图片名称||
|FilePath||String| 200|图片地址|||
|managerCards||String|500|资证||多个资证id连接的字符串|
|contractIds||String|500|合同编号||多个合同编号连接的字符串||
|financialIds||String|500|财务报表编号||多个财务报表编号连接的字符串||
|invoiceIds||String|500|发票||多个财务报表编号连接的字符串||
|assetIds||String|500|资产id||多个资产id连接的字符串||

#### 响应数据
|参数名称|是否必填|描述|默认值|  
|:----:|:----:|:----:|:----:|  

### 实例
#### 请求实体
```json
{
    "address": [
        {
            "fileName": "file1",
            "filePath": "filepaths1"
        },
        {
            "fileName": "file2",
            "filePath": "filepaths2"
        },
        {
            "fileName": "file3",
            "filePath": "filepaths3"
        }
    ],
    "productId": "15154930825584651576",
    "contractIds": "12321;34534645;234124;6746;1324,",
    "assetIds": "12321342314;12341234;123421354;1234123",
    "managerCards": "123;123123;234534;457;47",
    "remark": "asdfadfasd",
    "bankId": "15154930825582067531",
    "borrowAmount": 6000000,
    "borrowArrange": 12,
    "policyId": "15154930825604840510",
    "recordAddress": "http://localhost:8080/asfdasd",
    "rate": 2.17,
    "letterId": "15154930825603998372",
    "financialIds": "2345;12341234;5764;7612341234",
    "assuranceId": "15154930825605853160",
    "invoiceIds": "12341234;123412452435;12341234;123123",
    "status": "0"
}
```

#### 响应参数
```json
{
	"code": 201,
	"message": "success"
}
```

### 数据库操作  
```sql
start transcation;

update guborrowmoney set
`borrow_amount` = 'borrowAmount',   -- 借款id 自动生成
`rate` = 'rate',  -- 借款金额
`bank_id` = 'bankId',  -- 利率
`product_id` = 'productId',  
`user_id` = 'userId',  
`borrow_arrange` = 'borrowArrange',  -- 当前登录用户id
`create_time` = 'createTime',  
`letter_id` = 'letterId',  
`policy_id` = 'policyId',  -- 推荐函id 默认 null
`assurance_id` = 'assuranceId',   -- 保险单id 默认 null
`contract_img` = 'contractImg',   -- 担保id 默认 null
`cert_id` = 'certId',   -- 合同图片
`status` = 'status',  -- 认证id
`record_address` = 'recordAddress',   -- 状态(0,草稿,1待审核,2,审核不通过,3审核通过并生成合同,4申请放款,5放款完成)
`remark` = 'remark',  -- 草稿记录地址
`flag` = 'flag' 
where `borrow_id` = 'borrowId' 


update guapplydata set
	`manager_cards` = 'manager_cards',
	`contract_ids` = 'contract_ids',
	`financial_ids` = 'financial_ids',
	`invoice_ids` = 'invoice_ids',
	`asset_ids` = 'asset_ids',
	`other_id` = 'other_id',
	`flag` = 'flag',
	`remark` = 'remark'
where `apply_id` = 'apply_id';

commit;
```

-----------------------

## 根据id查询借款合同

#### 基本信息
* 请求协议：`HTTP`
* 请求地址：`/app/guborrowmoneys/{borrowId}/compact`
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
| contractNo| √	| 借款合同号	||
| borrowId| √	| 申请编号	||
| borrowAmount| √	| 借款金额	||
| rate| √	| 利率	||
| interest| √	| 借款利息	||
| borrowArrange| √	| 借款期限	||
| status| √	| 状态	||
| createTime| √	| 创建时间	||
| contractImg| √	| 合同图片	||
| payType| √ | 还款方式	||
| remark| 	| 备注	||
| acceptName| √	| 收款人	||
| bank| √	| 开户银行	||
| accountNo| √	| 账号	||
| amount| √	| 放款金额	||
| letterId| √	| 推荐函	||
| policyId| √	| 保险	||
| proposalNo| √	| 保单号	||
| acceptanceStartTime| √	| 承保开始时间	||
| acceptanceEndTime| √	| 承保结束时间	||
| assuranceId| √	| 担保	||
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

## 根据id申请放款

#### 基本信息
* 请求类型：`HTTP`
* 请求地址：`/app/guborrowmoneys/{borrowId}/compact`
* 请求方式：`PUT`
* 请求类型：`JSON`
* 响应类型：`R`

### 接口描述
根据企业id 申请借款合同
添加借款合同,并更改申请借款为待审核

#### 请求数据


|参数名称|是否必填|类型|长度|描述|默认值|备注|
|:----:|:----:|:----:|:----:|:---:|:----:|:---:|
|token|√|String||验证登录详情|
|borrowId|√|String|32|申请借款id|
|acceptName| √ |String|32| 收款人 ||收款人名称|
|contractNo| √ |String|32| 合同号 ||
|bank| √ |String|32 |开户行 ||开户行名称||
|accountNo| √ |String| 20| 账号 |||
|amount| √ |BigDecimal|12| 放款金额 ||
|remark|  |String|200| 备注 ||备注说明|

#### 响应数据
|参数名称|是否必填|描述|默认值|  
|:----:|:----:|:----:|:----:|  

### 实例
#### 请求实体
```json
{
	"applyMoney": {
		"amount": 500000,
		"bank": "招商银行",
		"contractNo": "15156606633151158065",
		"accountNo": "21546413513213132131",
		"acceptName": "张三",
		"borrowId": "15154979522381392010",
		"remark": "asdfasdkjfhalksjdf"
	},
	"borrowId": "15154979522381392010",
	"userId": "15264311168"
}
```
#### 响应参数
```json
{
    "msg": "修改成功",
    "code": 201
}
```

### 数据库操作  
```sql
start transaction;
insert into guapplymoney (
	`apply_id`,
	`contract_no`,
	`accept_name`,
	`bank`,
	`account_no`,
	`amount`,
	`user_id`,
	`bank_status`,
	`gov_status`,
	`input_time`,
	`update_time`,
	`remark`
) values (
	'apply_id',
	'contract_no',
	'accept_name',
	'bank',
	'account_no',
	'amount',
	'user_id',
	'bank_status',
	'gov_status',
	'input_time',
	'update_time',
	'remark'
)

update guborrowmonetcompact 
set status = 4 
where borrow_id = 'borrowId';

commit;
```
------------------------

## 根据id查询借款申请

#### 基本信息
* 请求类型：`HTTP`
* 请求地址：`/app/guborrowmoneys/{borrowId}/apply`
* 请求方式：`GET`
* 请求类型：`String`
* 响应类型：`R`


### 接口描述
根据企业id 查询借款申请
并加载相关：
- 推荐函
- 保险
- 担保
- ...

#### 请求数据

|参数名称|是否必填|类型|长度|描述|默认值|备注|
|:----:|:----:|:----:|:----:|:---:|:----:|:---:|
|token|√|String||验证登录详情|
|borrowId|√|String|32|申请借款id|


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
------------------
## 根据id修改保存草稿

#### 基本信息
* 请求类型：`HTTP`
* 请求地址：`/app/guborrowmoneys/{borrowId}/status`
* 请求方式：`PUT`
* 请求类型：`JSON`
* 响应类型：`R`

### 接口描述


#### 请求数据
|参数名称|是否必填|类型|长度|描述|默认值|备注|
|:----:|:----:|:----:|:----:|:---:|:----:|:---:|
|token|√|String|| 登录验证|||


#### 响应数据
|参数名称|是否必填|描述|默认值|  
|:----:|:----:|:----:|:----:|  


### 实例
#### 请求实体
```json

```

#### 响应参数
```json
{
    "msg": "success",
    "code": 201,
    "message": "修改成功"
}
```

### 数据库操作  
```sql
update guborrowmoney SET status = '1' where borrow_id = '15162430025263970647'
```
------------------

