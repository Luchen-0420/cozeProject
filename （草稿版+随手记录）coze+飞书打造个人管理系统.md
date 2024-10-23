# 教程简介

coze结合飞书打造个人管理系统：财务、健康、个人成长、职业规划等方面实现半自动化。针对p人的一项改造工程。

目前设想是记录由coze完成，调阅+建议调整需要微调模型，封装api在coze内调用

## 一、书籍管理

## 二、智能待办

用户每周制定周计划、日计划。由大模型帮助理解、排序用户当前任务的优先级，进行每日推送

## 三、

# 书籍管理工作流

该模板主要功能：

1. 书单入库
2. 每添加一本书至书单库同时生成一篇阅读笔记模板存储

## 一、准备工作

### 1. 新建飞书表格

| 字段名     | 飞书字段类型 | 字段说明                                                                 |
|------------|---------------|--------------------------------------------------------------------------|
| 书籍名称   | 文本        | 书名                                               |
| 封面图     | 附件        | 封面图                                         |
| 类型       | 单选        | 属于什么类型                              |
| 作者       | 文本        | 作者                              |
| 简介       | 文本         | 书籍简介              |
| 进度      | 进度         | 阅读进度        |
| 开始阅读   | 日期       | xxxx-xx-xx形式 |
| 结束阅读   | 日期       | xxxx-xx-xx形式 |
| 笔记链接     | 超链接   | 填写阅读笔记的超链接                              |
| 个人推荐指数   | 评分         | 个人觉得这篇书是否值得观看                   |

![528ff5801fdee42a805fa780228431d](https://github.com/user-attachments/assets/7cd3b6d6-e6fa-4889-aba9-1af93e9b5292)


### 2. 盘点现有插件

现有插件：

1. 豆瓣图书搜索插件（获取图书的基础信息）
2. 豆瓣图书简介插件（获取书籍简介）
3. 新建飞书云文档
4. 写入飞书表格插件

### 3. 创建bot并发布飞书

创建1个bot并发布飞书，后台可以看到应用创建成功。

![创建应用成功](https://github.com/user-attachments/assets/257e74b4-56ac-4805-8b46-cfed9079a183)

### 4. 为应用开通云文档相关资源的权限

新建群组

![新建群组](https://github.com/user-attachments/assets/da93f9f9-afa3-43b8-9400-b8f4a26f9b14)

添加群机器人

![添加群机器人](https://github.com/user-attachments/assets/e183d1c9-cdf6-4cbe-93e1-11ba04c35147)

![机器人添加成功](https://github.com/user-attachments/assets/e90e292c-514a-4b23-9fe6-dae936e0ed53)

到目标文件夹内添加群组，点击添加协作者

![添加协作者](https://github.com/user-attachments/assets/181df4f7-e7c4-4533-aec4-ce7a59f92d87)

![添加群组](https://github.com/user-attachments/assets/5c96563c-ec0c-4ad3-a7a8-c63c48395877)

![添加成功](https://github.com/user-attachments/assets/6aadab15-82a5-47b6-b2c4-1b1124c0876f)

![设置权限](https://github.com/user-attachments/assets/8050680a-87db-4ff7-99c9-eff85b2b4ca4)


### 5. 做异常处理

无法在工作流中实现新建文档到目标文件夹下，因为文件夹权限设置有问题，如果用自定义插件来实现的话要面临每次请求都会走到更新token的情况。

不过只要工作流能发布，到应用中就不存在这个问题。

最终解决方法是增加代码节点做异常处理，略过插件返回null的情况直接走序列化内容写进飞书表格。

## 2024-10-23 记录

和之前一样，对新增进飞书表格的数据进行序列化处理，相同逻辑代码但是报错了。

解决方法：上一个demo中涉及对多选tag的处理，这次不涉及，注释掉转二维数组的代码就可以了。

关于上传书籍封面图至飞书表格，字段设置是附件，这里要先调用上传素材接口拿到返回的file_token作为输入。

[上传素材接口文档](https://open.feishu.cn/document/server-docs/docs/drive-v1/media/upload_all?appId=cli_a78030a894f99013)

看了下需要tenant_access_token，有点不想要封面图这个字段了。算了还是写吧，总比user_access_token好。

![tenant_access_token](https://github.com/user-attachments/assets/d7c132d1-6053-4bb1-aad1-025adb3fdb20)

用官方提供的，省力。写得人烦躁不堪。

![插件获取认证授权](https://github.com/user-attachments/assets/01a4bb6b-c282-4095-a618-0e78dac2c92d)

其实既然都用到了tenant_access_token，不如把新建文档的节点也更改算了，这样就不用做异常处理了，直接自己写个接口新建。



