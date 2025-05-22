

组织架构变动回调事件，组织事件常见类型：

| 事件类型（EventType）   | 说明          |
| ----------------- | ----------- |
| `org_dept_create` | 部门创建        |
| `org_dept_modify` | 部门修改        |
| `org_dept_remove` | 部门删除        |
| `user_add_org`    | 新员工入职（加入企业） |
| `user_modify_org` | 员工信息变更      |
| `user_leave_org`  | 员工离职（退出企业）  |

DingTalk maven

```xml

```


钉钉 -> wiki

员工信息数据表 

|                              |          |
| ---------------------------- | -------- |
| sys00-name                   | 姓名       |
| sys00-email                  | 邮箱       |
| sys00-deptIds                | 部门id列表   |
| sys00-mainDeptId             | 主部门id    |
| sys00-dept                   | 部门       |
| sys00-mainDept               | 主部门      |
| sys00-position               | 职位       |
| sys00-mobile                 | 手机号      |
| sys00-jobNumber              | 工号       |
| sys00-tel                    | 分机号      |
| sys00-workPlace              | 办公地点     |
| sys00-remark                 | 备注       |
| sys00-confirmJoinTime        | 入职时间     |
| sys01-employeeType           | 员工类型     |
| sys01-employeeStatus         | 员工状态     |
| sys01-probationPeriodType    | 试用期      |
| sys01-regularTime            | 转正日期     |
| sys01-positionLevel          | 岗位职级     |
| sys02-realName               | 身份证姓名    |
| sys02-certNo                 | 证件号码     |
| sys02-birthTime              | 出生日期     |
| sys02-sexType                | 性别       |
| sys02-nationType             | 民族       |
| sys02-certAddress            | 身份证地址    |
| sys02-certEndTime            | 证件有效期    |
| sys02-marriage               | 婚姻状况     |
| sys02-joinWorkingTime        | 首次参加工作时间 |
| sys02-residenceType          | 户籍类型     |
| sys02-address                | 住址       |
| sys02-politicalStatus        | 政治面貌     |
| sys09-personalSi             | 个人社保账号   |
| sys09-personalHf             | 个人公积金账号  |
| sys03-highestEdu             | 最高学历     |
| sys03-graduateSchool         | 毕业院校     |
| sys03-graduationTime         | 毕业时间     |
| sys03-major                  | 所学专业     |
| sys04-bankAccountNo          | 银行卡号     |
| sys04-accountBank            | 开户行      |
| sys05-contractCompanyName    | 合同公司     |
| sys05-contractType           | 合同类型     |
| sys05-firstContractStartTime | 首次合同起始日  |
| sys05-firstContractEndTime   | 首次合同到期日  |
| sys05-nowContractStartTime   | 现合同起始日   |
| sys05-nowContractEndTime     | 现合同到期日   |
| sys05-contractPeriodType     | 合同期限     |
| sys05-contractRenewCount     | 续签次数     |
| sys06-urgentContactsName     | 紧急联系人姓名  |
| sys06-urgentContactsRelation | 联系人关系    |
| sys06-urgentContactsPhone    | 联系人电话    |
| sys07-haveChild              | 有无子女     |
| sys07-childName              | 子女姓名     |
| sys07-childSex               | 子女性别     |
| sys07-childBirthDate         | 子女出生日期   |

needed

|                            |        |
| -------------------------- | ------ |
| 字段code                     | 业务含义   |
| sys00-name                 | 姓名     |
| sys00-email                | 邮箱     |
| sys00-dept                 | 部门     |
| sys00-mainDept             | 主部门    |
| sys00-position             | 职位     |
| sys00-mobile               | 手机号    |
| sys00-jobNumber            | 工号     |
| sys00-confirmJoinTime      | 入职时间   |
| sys01-employeeType         | 员工类型   |
| sys01-employeeStatus       | 员工状态   |
| sys01-positionLevel        | 岗位职级   |
| sys02-realName             | 身份证姓名  |
| sys02-certNo               | 证件号码   |
| sys02-birthTime            | 出生日期   |
| sys02-sexType              | 性别     |
| sys02-nationType           | 民族     |
| sys05-contractType         | 合同类型   |
| sys05-nowContractStartTime | 现合同起始日 |
| sys05-nowContractEndTime   | 现合同到期日 |
| sys05-contractPeriodType   | 合同期限   |

### 钉钉开发者权限申请

您好，

我是后端开发朱润峰，因公司内部系统需与钉钉进行深度集成，对接钉钉组织架构，实现人员和部门数据同步、自动变更感知，现申请开通开发者权限。

主要用途如下：

- 获取钉钉组织架构（部门、用户信息）；
    
- 实现组织变更自动同步（回调事件）；
    
- 后续支持其他扩展功能。
    

所有接口调用均在后端完成，数据加密传输，访问受控。请您协助开通开发者权限并授权。

谢谢！

申请人：朱润峰
日期：2025年05月22日


