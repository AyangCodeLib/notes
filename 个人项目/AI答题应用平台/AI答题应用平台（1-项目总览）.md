# 项目介绍
阿洋文档是一款基于Vue3+Spring Boot+Redis+ChatGLM+RxJava+SSE的**AI答题应用平台**。
用户可以基于AI快速快速制作并发布答题应用，支持检索、分享、在线答题并基于AI得到回答总结；管理员可以集中管理和审核应用。

## 项目三大阶段

1. 第一阶段，开发本地的`MBTI性格测评的答题小程序`。
2. 第二阶段，开发`答题应用平台`。用户可以通过上传题目和自定义打分规则，创建答题应用，供其他用户使用。
3. 第三阶段，让AI为平台赋能，做一个`AI智能答题应用平台`。用户只需设定主题，就能通过AI生成题目、用AI分析用户答案，极大降低创建答题应用的成本。
# 核心业务流程
## 总流程：
![QQ_1722053601054.png](https://cdn.nlark.com/yuque/0/2024/png/40608915/1722053606493-3865bbb7-36ff-4570-a01c-b9bf436409e5.png#averageHue=%23f7f7f7&clientId=u11f18a45-ffe9-4&from=paste&height=1083&id=u26efc759&originHeight=1083&originWidth=1702&originalType=binary&ratio=1&rotation=0&showTitle=false&size=409581&status=done&style=none&taskId=u8981e84e-bf67-4c91-a6f0-cdbf7a9fbff&title=&width=1702)
## 自定义上传题目流程：
![QQ_1722067050367.png](https://cdn.nlark.com/yuque/0/2024/png/40608915/1722067050261-806e490e-903d-4ca3-8609-ffbceacbf431.png#averageHue=%23f6f6f6&clientId=u11f18a45-ffe9-4&from=paste&height=796&id=ub936844e&originHeight=796&originWidth=1165&originalType=binary&ratio=1&rotation=0&showTitle=false&size=152623&status=done&style=none&taskId=u788820fc-659e-4fcd-a506-0939cac9c6c&title=&width=1165)
## 用户答题流程：
![QQ_1722067086084.png](https://cdn.nlark.com/yuque/0/2024/png/40608915/1722067088011-c584aca0-f8e0-4ee7-b6d5-cacc825bcb29.png#averageHue=%23f8f8f8&clientId=u11f18a45-ffe9-4&from=paste&height=782&id=ubb99dd35&originHeight=782&originWidth=1176&originalType=binary&ratio=1&rotation=0&showTitle=false&size=139097&status=done&style=none&taskId=u323209be-d29f-4981-afa7-1786f91b85c&title=&width=1176)
## AI创建题目流程
![QQ_1722067110858.png](https://cdn.nlark.com/yuque/0/2024/png/40608915/1722067110285-7477e9d7-601c-447d-9630-d36835f744f6.png#averageHue=%23f9f9f9&clientId=u11f18a45-ffe9-4&from=paste&height=780&id=u88eb9d66&originHeight=780&originWidth=1165&originalType=binary&ratio=1&rotation=0&showTitle=false&size=127668&status=done&style=none&taskId=ub580cad9-557c-48bf-87ca-48fb424a2ba&title=&width=1165)
## 用户答题AI总结流程
![QQ_1722067133051.png](https://cdn.nlark.com/yuque/0/2024/png/40608915/1722067132487-25bdb9ce-3460-41df-898c-c326501436f7.png#averageHue=%23f7f7f7&clientId=u11f18a45-ffe9-4&from=paste&height=749&id=udbf5b05b&originHeight=749&originWidth=1187&originalType=binary&ratio=1&rotation=0&showTitle=false&size=163321&status=done&style=none&taskId=ufe8e7005-f46a-4168-bee2-1ec17c24175&title=&width=1187)
## 时序图
![QQ_1722067156262.png](https://cdn.nlark.com/yuque/0/2024/png/40608915/1722067156241-cc1df924-1f4e-49eb-980b-d4a512c6e920.png#averageHue=%23f8f9f4&clientId=u11f18a45-ffe9-4&from=paste&height=786&id=ua55adae2&originHeight=786&originWidth=1172&originalType=binary&ratio=1&rotation=0&showTitle=false&size=233768&status=done&style=none&taskId=u3ea1c4de-e8a8-472a-8f08-62408a40e62&title=&width=1172)
# 项目功能梳理
## 小程序

- 在线答题
## 平台

- 用户模块
   - 注册
   - 登录
   - 管理用户-增删改查（仅管理员可用）
- 应用模块
   - 创建应用（名称、描述、上传图片、应用类型）
   - 修改应用（用户）
   - 审核发布和下架应用（管理员）
   - 管理应用-增删改查（管理员）
   - 应用分享（扫码分享）
- 题目模块
   - 创建题目（名称、选项）
   - 修改题目
   - 删除题目
   - AI生成题目
- 评分模块
   - 多种评分策略
   - 创建评分结果
   - 题目得分设置
- 回答模块
   - 提交选择
   - 回答记录
   - AI分析总结回答
- 统计分享模块
   - 应用评分结果分析
# 技术选型
## 开发工具

- 前端IDE：WebStorm
- 后端IDE：IDEA
- [CodeGeex 智能编程助手](https://codegeex.cn/)
## 前端
### Web网页开发

- Vue3
- Vue-CLI脚手架
- Pinia状态管理
- Axios请求库
- Arco Design组件库
- 前端工程化：ESLint+Pretter+TypeScript
- 富文本编辑器
- QRCode.js二维码生成
- **OpenAPI前端代码生成**
### 小程序开发

- React
- Taro跨端开发框架
- Taro UI 组件库
## 后端

- Java Spring Boot 开发框架
- 存储层： MySQL数据库+Redis缓存+腾讯云COS对象存储
- MyBatis-Plus及MyBatis X自动生成
- Redission分布式锁
- Caffeine本地缓存
- **基于ChatGLM大模型实现AI能力**
- **RxJava响应式框架+多线程/线程池实现**
- **ShardingSphere分库分表+分布式ID雪花算法**
- **SSE服务端推送**
- **多种设计模式**
- **多角度项目优化：性能、稳定性、幂等性优化等**
# 架构设计
![QQ_1722067348698.png](https://cdn.nlark.com/yuque/0/2024/png/40608915/1722067348414-c2778240-c11a-4c7f-b713-749abcdcc8b8.png#averageHue=%239cf996&clientId=u11f18a45-ffe9-4&from=paste&height=748&id=u88c88f65&originHeight=748&originWidth=1025&originalType=binary&ratio=1&rotation=0&showTitle=false&size=188872&status=done&style=none&taskId=u40e7de9e-bad9-4652-82f8-6c33d9af821&title=&width=1025)
# 代码仓库
代码仓库：[https://github.com/AyangCodeLib/ayangAI](https://github.com/AyangCodeLib/ayangAI)
