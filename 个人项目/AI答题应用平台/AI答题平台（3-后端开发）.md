# 需求开发
- P0 为核心，非做不可
- P1 为重点功能，最好做
- P2 为实用功能，有空就做1811212265457647618_0.21451330583951012
- P3 可做可不做，时间充裕再做
## 项目功能梳理
按照模块梳理功能：

- 用户模块
   - 注册 P0
   - 登录 P0
   - 管理用户-增删改查（仅管理员可用） P1
- 应用模块
   - 创建应用 P0
   - 修改应用 P1
   - 删除应用 P1
   - 查看应用列表 P0
   - 查看应用详情 P0
   - 查看自己创建的应用 P1
   - 管理应用-增删改查（仅管理员可用）P0
   - 审核发布和下架应用（仅管理员可用）P0
   - 应用分享（扫码查看）P2
- 题目模块
   - 创建题目（包括题目选项得分设计）P0
   - 修改题目 P1
   - 删除题目 P1
   - 管理题目-增删改查（仅管理员可用）P1
   - AI生成题目 P1
- 评分模块
   - 创建评分结果 P0
   - 修改评分结果 P1
   - 删除评分结果 P1
   - 根据回答计算评分结果（多种评分策略）
      - 自定义评分规则 - 测评类 P0
      - 自定义评分规则 - 打分类 P0
      - AI评分 P1
   - 管理评分结果 - 增删改查（仅管理员可用）P1
- 回答模块
   - 提交回答（创建）P0
   - 查看某次回答的评分结果 P0
   - 查看自己提交的回答列表 P1
   - 管理回答 - 增删改查（仅管理员可用）P1
- 统计分析模块
   - 应用评分结果分析和查看 P2
## 核心业务流程
![](https://cdn.nlark.com/yuque/0/2024/png/40608915/1722053606493-3865bbb7-36ff-4570-a01c-b9bf436409e5.png?x-oss-process=image%2Fformat%2Cwebp%2Fresize%2Cw_1168%2Climit_0#averageHue=%23f7f7f7&from=url&id=k7TsB&originHeight=743&originWidth=1168&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)
文字描述：

1. 用户注册 => 用户登录
2. 用户创建应用 => 创建题目（包括题目选项得分） => 创建评分规则（评分策略和评分结果）
3. 管理员管理应用，审核发布（或下架）应用
4. 用户查看和检索应用列表，进入应用详情页，在线答题并提交回答
5. 经过评分模块结算后，用户可查看本次评分结果
# 库表设计
对应需求分析中的功能梳理的模块，应该有 6 张表（统计分析表本节先不做）。
库名：ayangAI
## 用户表
```sql
-- 用户表
create table if not exists user
(
    id           bigint auto_increment comment 'id' primary key,
    userAccount  varchar(256)                           not null comment '账号',
    userPassword varchar(512)                           not null comment '密码',
    unionId      varchar(256)                           null comment '微信开放平台id',
    mpOpenId     varchar(256)                           null comment '公众号openId',
    userName     varchar(256)                           null comment '用户昵称',
    userAvatar   varchar(1024)                          null comment '用户头像',
    userProfile  varchar(512)                           null comment '用户简介',
    userRole     varchar(256) default 'user'            not null comment '用户角色：user/admin/ban',
    createTime   datetime     default CURRENT_TIMESTAMP not null comment '创建时间',
    updateTime   datetime     default CURRENT_TIMESTAMP not null on update CURRENT_TIMESTAMP comment '更新时间',
    isDelete     tinyint      default 0                 not null comment '是否删除',
    index idx_unionId (unionId)
) comment '用户' collate = utf8mb4_unicode_ci;

```
## 应用表
```sql
-- 应用表
create table if not exists app
(
    id              bigint auto_increment comment 'id' primary key,
    appName         varchar(128)                       not null comment '应用名',
    appDesc         varchar(2048)                      null comment '应用描述',
    appIcon         varchar(1024)                      null comment '应用图标',
    appType         tinyint  default 0                 not null comment '应用类型（0-得分类，1-测评类）',
    scoringStrategy tinyint  default 0                 not null comment '评分策略（0-自定义，1-AI）',
    reviewStatus    int      default 0                 not null comment '审核状态：0-待审核, 1-通过, 2-拒绝',
    reviewMessage   varchar(512)                       null comment '审核信息',
    reviewerId      bigint                             null comment '审核人 id',
    reviewTime      datetime                           null comment '审核时间',
    userId          bigint                             not null comment '创建用户 id',
    createTime      datetime default CURRENT_TIMESTAMP not null comment '创建时间',
    updateTime      datetime default CURRENT_TIMESTAMP not null on update CURRENT_TIMESTAMP comment '更新时间',
    isDelete        tinyint  default 0                 not null comment '是否删除',
    index idx_appName (appName)
) comment '应用' collate = utf8mb4_unicode_ci;
```
审核四件套
```sql
reviewStatus    int      default 0                 not null comment '审核状态：0-待审核, 1-通过, 2-拒绝',
reviewMessage   varchar(512)                       null comment '审核信息',
reviewerId      bigint                             null comment '审核人 id',
reviewTime      datetime                           null comment '审核时间',
```
## 题目表
每个应用对应一个题目表的记录，使用 questionContent 这一 JSON 字段，整体更新和维护该应用的题目列表、选项信息。
```sql
-- 题目表
create table if not exists question
(
    id              bigint auto_increment comment 'id' primary key,
    questionContent text                               null comment '题目内容（json格式）',
    appId           bigint                             not null comment '应用 id',
    userId          bigint                             not null comment '创建用户 id',
    createTime      datetime default CURRENT_TIMESTAMP not null comment '创建时间',
    updateTime      datetime default CURRENT_TIMESTAMP not null on update CURRENT_TIMESTAMP comment '更新时间',
    isDelete        tinyint  default 0                 not null comment '是否删除',
    index idx_appId (appId)
) comment '题目' collate = utf8mb4_unicode_ci;
```
questionContent 结构，注意区分 result 和 score，分别用于统计两种不同应用类型的结果。
```json
[
  {
    "options": [
      {
        "result": "I", // 如果是测评类，则用 reslut 来保存答案属性
        "score": 1,    // 如果是得分类，则用 score 来设置本题分数
        "value": "A选项", //选项内容
        "key": "A"   //选项 key
      },
      {
        "result": "E", // 如果是测评类，则用 reslut 来保存答案属性
        "score": 0,
        "value": "B选项",
        "key": "B"
      }
    ],
    "title": "题目"
  }
]
```
## 评分结果表
用户提交答案后，会获得一定的回答评定，例如 ISTJ 之类的，评分结果表就是存储这些数据的表。
```sql
-- 评分结果表
create table if not exists scoring_result
(
    id               bigint auto_increment comment 'id' primary key,
    resultName       varchar(128)                       not null comment '结果名称，如物流师',
    resultDesc       text                               null comment '结果描述',
    resultPicture    varchar(1024)                      null comment '结果图片',
    resultProp       varchar(128)                       null comment '结果属性集合 JSON，如 [I,S,T,J]',
    resultScoreRange int                                null comment '结果得分范围，如 80，表示 80及以上的分数命中此结果',
    appId            bigint                             not null comment '应用 id',
    userId           bigint                             not null comment '创建用户 id',
    createTime       datetime default CURRENT_TIMESTAMP not null comment '创建时间',
    updateTime       datetime default CURRENT_TIMESTAMP not null on update CURRENT_TIMESTAMP comment '更新时间',
    isDelete         tinyint  default 0                 not null comment '是否删除',
    index idx_appId (appId)
) comment '评分结果' collate = utf8mb4_unicode_ci;
```
**不同类型的应用使用不同的字段。**测评类用 resultProp，得分类用 resultScoreRange。
### 测评类应用
resultProp 的结构，结果属性集合 JSON：
```
["I", "S", "T", "J"]
复制代码
```
### 得分类应用
默认 resultScoreRange 字段的使用规则是“大于等于设定的分数则命中对应的 result”。
举一个得分类应用的例子：

- resultName：知识大师
- resultDesc：你真棒棒哦，知识掌握地非常出色！
- resultIcon：预留字段，如果想界面好看点，可以给 result 设定图片
- resultScoreRange：9

示例数据如下：
```sql
-- 得分 评分结果初始化
INSERT INTO demo.scoring_result (id, resultName, resultDesc, resultPicture, resultProp, resultScoreRange, createTime, updateTime, isDelete, appId, userId) VALUES (17, '知识大师', '你真棒棒哦，知识掌握地非常出色！', null, null, 9, '2024-04-25 15:05:44', '2024-05-09 12:28:21', 0, 2, 1);
INSERT INTO demo.scoring_result (id, resultName, resultDesc, resultPicture, resultProp, resultScoreRange, createTime, updateTime, isDelete, appId, userId) VALUES (18, '地理小能手！', '你对于地理知识了解得相当不错，但还有一些小地方需要加强哦！', null, null, 7, '2024-04-25 15:05:44', '2024-05-09 12:28:21', 0, 2, 1);
INSERT INTO demo.scoring_result (id, resultName, resultDesc, resultPicture, resultProp, resultScoreRange, createTime, updateTime, isDelete, appId, userId) VALUES (19,
```
## 用户答题记录表
```
-- 用户答题记录表
create table if not exists user_answer
(
    id              bigint auto_increment primary key,
    appId           bigint                             not null comment '应用 id',
    appType         tinyint  default 0                 not null comment '应用类型（0-得分类，1-角色测评类）',
    scoringStrategy tinyint  default 0                 not null comment '评分策略（0-自定义，1-AI）',
    choices         text                               null comment '用户答案（JSON 数组）',
    resultId        bigint                             null comment '评分结果 id',
    resultName      varchar(128)                       null comment '结果名称，如物流师',
    resultDesc      text                               null comment '结果描述',
    resultPicture   varchar(1024)                      null comment '结果图标',
    resultScore     int                                null comment '得分',
    userId          bigint                             not null comment '用户 id',
    createTime      datetime default CURRENT_TIMESTAMP not null comment '创建时间',
    updateTime      datetime default CURRENT_TIMESTAMP not null on update CURRENT_TIMESTAMP comment '更新时间',
    isDelete        tinyint  default 0                 not null comment '是否删除',
    index idx_appId (appId),
    index idx_userId (userId)
) comment '用户答题记录' collate = utf8mb4_unicode_ci;
复制代码
```
resultId 可能为空，是因为 AI 分析策略不会从结果表中选取结果，没有 resultId。
为什么要有冗余字段？因为回答记录一旦设置，几乎不会更改，便于查询，不用联表，节约开发成本。
还有可能通过异步的方式、或者题目答案没提交（只答一半的时候），先临时保存回答记录。
choices 是 JSON 结构，题目选项的 key 数组：
```
["A", "B", "C"]
复制代码
```
只存储选项的优点是，可以节约存储空间；但缺点是应用的题目如果发生修改，就对应不上了。
如果更严谨一些，可能要对应题目的 id（或者题目编号、题目的 key），比如：
```sql
{
  1: "A",
  2: "B"
}
```
