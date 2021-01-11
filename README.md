# 腾讯云人脸核身（FACEID）插件

## 1. 插件介绍
| 标题      | 名称    |
| ----     | ---------------- |
| 中文名称   | 腾讯云人脸核身（FACEID）插件 |
| 英文名称   | tencentcloud-plugin-faceid |
| 最新版本   | v1.0.0 (2021.01.11) |
| 适用平台   | [DCloud uni-app](https://uniapp.dcloud.net.cn) |
| 适用产品   | [腾讯云慧眼人脸核身](https://cloud.tencent.com/product/faceid) |
| GitHub项目| [tencentcloud-uniapp-plugin-faceid](https://github.com/Tencent-Cloud-Plugins/tencentcloud-uniapp-plugin-faceid) |
| gitee项目| [tencentcloud-uniapp-plugin-faceid](https://gitee.com/Tencent-Cloud-Plugins/tencentcloud-uniapp-plugin-faceid) |
| Demo项目   | [tencentcloud-uniapp-plugin-example](https://github.com/Tencent-Cloud-Plugins/tencentcloud-uniapp-plugin-example) |
| 主创团队   | 腾讯云中小企业产品中心（SMB Product Center of Tencent Cloud） |
| 兼容平台   | H5、小程序、APP |

一款帮助开发者在uni-app项目开发中快捷使用腾讯云慧眼人脸核身产品功能的插件。

## 2. 功能特性

- 活体人脸核身(微信小程序)
- 活体人脸比对(微信小程序)
- 照片人脸核身
- 身份证人像照片验真
- 身份证识别及信息核验

## 3. 安装指引

本插件需要调用uniCloud云函数，而使用云函数的前提是：

- 使用DCloud官方开发工具HBuilderX 2.7+；
- 已注册DCloud开发者账号并通过实名认证；
- 开通了uniCloud并创建一个腾讯云的服务空间；

### 3.1. 新建或打开已有项目

1. 打开HBuilderX开发工具；
1. 新建或打开一个uni-app项目；

### 3.2. 导入云函数

1. 访问 DCloud 插件市场的 [腾讯云插件 - 云函数模板](https://ext.dcloud.net.cn/plugin?id=2139) 详情页；
2. 点击详情页右上角 **使用 HBuilderX 导入插件**，将云函数模板导入到您的项目中；
![](./images/guide/guide-1.png)
3. 在项目中打开 _cloudfunctions/tencentcloud-plugin/config.js_ 文件，将腾讯云的密钥信息配置进去，可以在腾讯云 [API 密钥管理](https://console.cloud.tencent.com/cam/capi) 中获取 SecretId、SecretKey 和 APPID；
![](./images/guide/guide-2.png)
4. 在[uniCloud控制台](https://unicloud.dcloud.net.cn/login)注册HBuild账号并登录，创建[云服务空间](https://uniapp.dcloud.net.cn/uniCloud/concepts/space)；
![](./images/guide/guide-3.png)
5. 在项目中打开 _cloudfunctions/tencentcloud-plugin/faceid/config.js 文件，将region配置进去，具体可参考[地域列表](https://cloud.tencent.com/document/api/1073/37989#.E5.9C.B0.E5.9F.9F.E5.88.97.E8.A1.A8)；
6. 绑定云函数的云服务空间，将[云函数](https://uniapp.dcloud.net.cn/uniCloud/concepts/cloudfunction) [**上传部署**](https://uniapp.dcloud.net.cn/uniCloud/quickstart?id=rundebug) 到您的[云服务空间](https://uniapp.dcloud.net.cn/uniCloud/concepts/space)；
![](./images/guide/guide-4.png)

> 如果您之前在使用其它腾讯云产品的 uni-app 插件时已经导入过此云函数模板，则前 4 个步骤可以省略。

> 若导入失败或有其它疑问，请查看 [uniCloud帮助文档](https://uniapp.dcloud.io/uniCloud/README) 云函数相关章节。

### 3.3. 导入插件

1. 访问DCloud插件市场 [腾讯云人脸核身（FACEID）插件](https://ext.dcloud.net.cn/plugin?id=3873) 详情页；
2. 点击详情页右上角 **使用HBuilderX导入插件** ，将插件导入到您的项目中；
3. 在项目中通过 import 语法将插件提供的方法导入到相关业务组件并使用；

> 本插件的默认导入位置是在您项目的“js_sdk”目录下

## 4. 使用指引

### 4.1. 插件API导图

![](./images/faceid-guide.png)

### 4.2. 插件API列表

| API名称          | API对应方法名 |
| ---------------- | ------------- |
| 活体人脸核身 | livenessRecognition |
| 活体人脸比对 | livenessCompare |
| 照片人脸核身 | imageRecognition |
| 获取数字验证码 | getLiveCode |
| 获取动作顺序 | getActionSequence |
| 身份证人像照片验真 | checkIdCardInformation |
| 身份证识别及信息核验 | idCardOCRVerification |

### 4.3. 插件使用示例

```javascript
// 人脸核身-活体人脸核身使用示例
// 从js_sdk列表中导入需要的api
import { livenessRecognition } from "@/js_sdk/tencentcloud-plugin-faceid";
import chooseVideo2Base64 from '@/js_sdk/tencentcloud-plugin-faceid/choose-video-2base64.js';

export default {
  data() {
    return {
      status: false,
      idCard: '430421**********10',
      name: '张三',
      detectResult: ''
    };
  },
  methods: {
    // 开始检测
    async startDetect() {
      try {
        const videoBase64 = await chooseVideo2Base64();
        this.status = true;
        this.detectResult = '';
        uni.showLoading({
          mask: true
        });
        const params = {
          idCard: this.idCard,
          name: this.name,
          videoBase64,
          livenessType: 'SILENT'
        };
        this.detectResult = await livenessRecognition(params);
      } catch (error) {
        uni.showToast({
          icon: 'none',
          title: error.message
        });
      } finally {
        this.status = false;
        uni.hideLoading();
      }
    }
  }
};
```

### 4.4 主要API 说明

#### livenessRecognition(params) ⇒ <code>Promise.&lt;object&gt;</code>
**Returns**: <code>Promise.&lt;object&gt;</code> - result 活体人脸核身结果

| Param          | Type | Required |  Description |
| ---------------- | ------------- | ---- | ---- |
| params | <code>object</code> | <code>true</code> | 活体人脸核身配置参数 |
| params.idCard | <code>string</code> | <code>true</code> | 身份证号 |
| params.name | <code>string</code> | <code>true</code> | 姓名。中文请使用UTF-8编码 |
| params.videoBase64 | <code>string</code> | <code>true</code> | 用于活体检测的视频，视频的BASE64值；BASE64编码后的大小不超过8M，支持mp4、avi、flv格式 |
| params.livenessType | <code>string</code> |  <code>true</code> | 活体检测类型，取值：LIP/ACTION/SILENT。LIP为数字模式，ACTION为动作模式，SILENT为静默模式，三种模式选择一种传入 |
| params.validateData | <code>string</code> |  <code>false</code> | 数字模式传参：数字验证码(1234)，需先调用接口获取数字验证码；动作模式传参：传动作顺序(2,1 or 1,2)，需先调用接口获取动作顺序；静默模式传参：空 |
| params.optional | <code>string</code> |  <code>false</code> | 额外配置，传入JSON字符串 |

### 4.5. 名词解释

服务空间：一个服务空间对应一整套独立的云开发资源，包括数据库、存储空间、云函数等资源。服务空间之间彼此隔离。更多详情请访问 [uniCloud开发文档](https://uniapp.dcloud.io/uniCloud/concepts/space)

云函数：云函数是运行在云端的JavaScript代码，更多详情请见 [uniCloud云函数文档](https://uniapp.dcloud.io/uniCloud/cf-functions)

## 5. 获取入口

| 插件入口       | 链接                                                                      |
| -------------- | ------------------------------------------------------------------------- |
| DCloud插件市场 | [腾讯云人脸核身（FACEID）插件](https://ext.dcloud.net.cn/plugin?id=3873) |

## 6. FAQ
> 1. Q:有没有demo示例？
>    
>    A：请查看demo示例[tencentcloud-uniapp-plugin-example](https://github.com/Tencent-Cloud-Plugins/tencentcloud-uniapp-plugin-example)
> 2. Q:活体人脸核身和活体人脸比对功能支持H5和APP吗？
>    
>    A：不支持,目前这两个功能只支持微信小程序


## 7. GitHub版本迭代记录

### 7.1. tencentcloud-uniapp-plugin-tmt v1.0.0

- 活体人脸核身(微信小程序)
- 活体人脸比对(微信小程序)
- 照片人脸核身
- 身份证人像照片验真
- 身份证识别及信息核验
  
## 8. 联系我们

如果您是用户，欢迎加入我们的[官方交流社区](https://dnspod.chat/category/10)，反馈
- bug和故障，获取帮助。
- 新的插件功能需求。
- 新的开源应用插件适配需求。

如果您是开发者，欢迎参与我们[插件开源项目](https://github.com/Tencent-Cloud-Plugins)，和我们一起修复插件问题，为更多开源应用适配腾讯云插件。
