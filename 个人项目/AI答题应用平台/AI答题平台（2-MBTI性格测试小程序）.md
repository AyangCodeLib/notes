# 参考项目
参考项目：[https://www.16personalities.com/ch](https://www.16personalities.com/ch)
# MBTI实现方案
核心组成部分：题目、用户答案、评分规则
## 题目结构
```json
[
    {
        "title": "你通常更喜欢",
        "options": [
            {
                "result": "I",
                "value": "独自工作",
                "key": "A"
            },
            {
                "result": "E",
                "value": "与他人合作",
                "key": "B"
            }
        ]
    }
]
```
逻辑：title代表标题，option代表的每个元素代表选项，key为选项，value表示值，result表示题目对应的结果
结构思路：为了便于java对象设计，相比于拿选项作为key，结构更清晰，更易于理解，更易于扩展，前后端都可以声明类型
优点：更灵活、排序
缺点：占用空间（可以进行预处理）
## 用户答案结构
用户提交答案的时候，仅需传递一个数组，数组内是选项：` ["A"]`，按数组顺序匹配对应的题目。
优点：不用再完整传递题目的结构，节省传输体积，提高性能。
```java
["A","A","A","B","A","A","A","B","B","A"]
```
## 评分规则
### MBTI评分原理
根据维基百科介绍，MBTI 全称是迈尔斯-布里格斯类型指标（英语：Myers-Briggs Type Indicator，简称MBTI）
参考：[https://zh.wikipedia.org/wiki/%E9%82%81%E7%88%BE%E6%96%AF-%E5%B8%83%E9%87%8C%E6%A0%BC%E6%96%AF%E6%80%A7%E6%A0%BC%E5%88%86%E9%A1%9E%E6%B3%95](https://zh.wikipedia.org/wiki/%E9%82%81%E7%88%BE%E6%96%AF-%E5%B8%83%E9%87%8C%E6%A0%BC%E6%96%AF%E6%80%A7%E6%A0%BC%E5%88%86%E9%A1%9E%E6%B3%95)
4 个对立组合，共 16 种结果：
 ![QQ_1722072205841.png](https://cdn.nlark.com/yuque/0/2024/png/40608915/1722072207970-65aebea3-1074-4ed4-a7f2-91f2d739dc1f.png#averageHue=%23fdfdfd&clientId=u74e06037-7560-4&from=paste&height=259&id=u5a2fb051&originHeight=259&originWidth=1170&originalType=binary&ratio=1&rotation=0&showTitle=false&size=100005&status=done&style=none&taskId=u21b9dad8-d7f2-4312-a4b8-71d8f4280b8&title=&width=1170)
![QQ_1722072254548.png](https://cdn.nlark.com/yuque/0/2024/png/40608915/1722072255175-d7cbe141-5093-4095-87db-7a6cc3714718.png#averageHue=%23e0e1e2&clientId=u74e06037-7560-4&from=paste&height=907&id=u85f93d82&originHeight=609&originWidth=580&originalType=binary&ratio=1&rotation=0&showTitle=false&size=69704&status=done&style=none&taskId=u292317a1-f554-4553-a56b-2a029f187a3&title=&width=864)
每道题目都是为四个组合的其中之一设定，为了判断这个人属于组合里的哪个偏好。
比如这一题，为了判断是 I 人还是 E 人（result 字段）：
```json
[
    {
        "title": "你通常更喜欢",
        "options": [
            {
                "result": "I",
                "value": "独自工作",
                "key": "A"
            },
            {
                "result": "E",
                "value": "与他人合作",
                "key": "B"
            }
        ]
    }
]
```
按照这个思路，我们可以统计题目中所有的偏好，比如答题一共选了 10 个 I，2 个 E，很明显 10 大于 2，因此我们得出他是 I 人，同理可判断是 S / N、T / F 和 J/P
所以我们出题的时候，给每个选择的答案对应设置一个 **属性**。简单举例，比如

- 第一题 A 对应 I，B 对应 E
- 第二题 A 对应 E，B 对应 I
- 第三题 A 对应 S, B 对应 N
- 第四题 A 对应 T, B 对应 F
- 第五题 A 对应 P, B 对应 J

那么如果用户选择了 [A,B,A,A,A]，可以算出用户有两个 I，一个 S ，一个 T 一个 P，很明显他是 ISTP 人格。
## 评分结果计算原理

1. 首先要有一个题目评分结果集合，这里预先创建了很多结果，包括了 MBTI 的所有 16 种角色。
- resultName：ISTJ
- resultDesc：忠诚可靠，被公认为务实，注重细节。
- resultIcon：预留字段，如果想界面好看点，可以给 result 设定图片
- resultProp：[I,S,T,J]

![QQ_1722072462768.png](https://cdn.nlark.com/yuque/0/2024/png/40608915/1722072463282-0b0dd4ef-6a7a-41eb-8cc4-5ad26dd2f69f.png#averageHue=%232c2c2c&clientId=u74e06037-7560-4&from=paste&height=603&id=u1bad9121&originHeight=603&originWidth=1238&originalType=binary&ratio=1&rotation=0&showTitle=false&size=379223&status=done&style=none&taskId=u38542418-f615-4389-a4a4-7f6cd324c7b&title=&width=1238)
题目评分结果对应的 JSON 结构如下：
```json
[
  {
    "resultProp": [
      "I",
      "S",
      "T",
      "J"
    ],
    "resultDesc": "忠诚可靠，被公认为务实，注重细节。",
    "resultPicture": "icon_url_istj",
    "resultName": "ISTJ（物流师）"
  },
  {
    "resultProp": [
      "I",
      "S",
      "F",
      "J"
    ],
    "resultDesc": "善良贴心，以同情心和责任为特点。",
    "resultPicture": "icon_url_isfj",
    "resultName": "ISFJ（守护者）"
  }
]
```

2. 怎么根据每道题目的选项计算出结果呢？

每个结果有一个 resultProp 字段，是一个元素不重复的数组（属性集合），里面的内容和题目选项的 result 字段匹配。
```json
[
    {
        "title": "你通常更喜欢",
        "options": [
            {
                "result": "I",
                "value": "独自工作",
                "key": "A"
            },
            {
                "result": "E",
                "value": "与他人合作",
                "key": "B"
            }
        ]
    }
]
```
此时用户第一题选了 A， 对应的属性如果是 I，那么我们遍历这 16 种结果，然后判断角色对应的 resultProp 里面是否包含 I , 如果包含则对应的角色就 +1 分，不包含则不得分。
最终遍历完所有题目后，我们就能知道这 16 种结果中，哪个角色得分最高，它就是最终的评分结果了。
# 开发相关
Taro 官方文档：[https://taro-docs.jd.com/docs/](https://taro-docs.jd.com/docs/) （跨端开发框架）
```java
npm install -g @tarojs/cli@3.6.28
```
taro-ui：[https://taro-ui.jd.com/#/docs/introduction](https://taro-ui.jd.com/#/docs/introduction)（最推荐，兼容性最好）
微信开发者工具下载：[https://developers.weixin.qq.com/miniprogram/dev/devtools/download.html](https://developers.weixin.qq.com/miniprogram/dev/devtools/download.html)
MBTI题目参考：[http://sssch.net/ArticleDetail.aspx?ArticleID=13188130318](http://sssch.net/ArticleDetail.aspx?ArticleID=13188130318)
请求代码生成：[https://www.npmjs.com/package/@umijs/openapi](https://www.npmjs.com/package/@umijs/openapi)
全局请求处理器：[https://axios-http.com/docs/instance](https://axios-http.com/docs/instance)
