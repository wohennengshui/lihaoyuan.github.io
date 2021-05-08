---
layout: post
title: "iOS_Jenkins打包配置"
date: 2021-05-07
categories: iOS
---

#### 入职以来一直负责项目的Jenkins自动化打包机配置 抽空整理一下相关内容 部分内容为前期笔记记录迁移 可能和现有版本有所修改 各有所长

## 概述
### 自动化打包分为四个环境
* 开发环境
* 测试环境
* 预发布环境
* 正式环境

### Jenkins结构化参数/Git分支配置
* AppChannel 对应四个打包环境
* GitBranch 对应不同分支 （分支详情见 [Git流程规范][Git流程规范Url]）
* 设置BOOL按钮upload 可选 release环境是否上传至appstore -注意如果选择upload则打出的包不是ad-hoc点对点模式，因此发布到蒲公英的无法安装
* 整体编译流程执行build.sh脚本文件

## 打包
### ipa打包 xcodebuild clean/build/archive
1. 清除上次打包留下的工程残留
>`xcodebuild clean -workspace "{workspace}"  -scheme "{target}" -configuration 'Release/Debug'`
2. 在Xcode中编译目标工程文件
>`xcodebuild -workspace "{workspace}"  -scheme "{target}" archive -archivePath "{path}" -configuration 'Release/Debug' -allowProvisioningUpdates`
3. 编译后的内容按照导出格式放到指定目录
>`xcodebuild -exportArchive -archivePath "{path}" -exportPath "{ipa_path}" -exportOptionsPlist "export_path" -allowProvisioningUpdate` 
* 除正式环境外 开发环境 测试环境 预发布环境均使用Debug的configuration，其中正式环境不包含调试信息，代码量和运行速度都优于Debug
* 编译打包成功后会移至目标文件夹，前次ipa文件会在.sh运行时首先删除，无需担心文件累积覆盖
* 本项目ipa文件改名，遵循年月日十分的日期规则
* 编译过程exportOptionsPlist可手动修改，也可借助PlistBuddy
* allowProvisioningUpdates字段用于每次打包时自动更新apple develop的provision描述文件

### plist修改-PlistBuddy简单使用
#### `/usr/libexec/PlistBuddy --help` 查看使用帮助
* apple建议将设置性内容存于属性表文件(plist)文件内，多数采用数组或者字典形式
* PlistBuddy通过键值匹配的方式对数据源进行增删改查
* 本项目内为打出不同的包，在脚本运行过程中动态改变键值，并在编译完成后修改回原值防止与git上的版本冲突，因此无需提前配置
* 后修改为不同环境配置不同plist文件 使得可以分开项目 同时打不同环境的包

## 发布
### 发布至蒲公英
* payer Key 字段 uKey apiKey 
* 安装方式 buildInstallType 选择2 - 密码安装，（1 - 公开安装、3 - 邀请安装、4 - 回答问题安装）
* 钉钉默认文案提示密码 buildPassword 字段
> 通过钉钉自动机器人发布至群内 curl  `https://oapi.dingtalk.com/robot.send?access_token=...`  可选参数-H定义 json方式 并且添加提示内容

### 发布至appstore 
* 验证 xcrun altool --validate-app -f {ipa_path}/xxx.ipa -t ios --apiKey "" --apiIssuer "" 
* 上传 xcrun altool --upload-app -f {ipa_path}/xxx.ipa -t ios --apiKey "" --apiIssuer "" 
* appKey apiIssuer 在.sh文件内 且获取后不再可以被获取 要妥善保管
* 选择相应的exportOptionsPlist 进行打包



## 发布后
### 保存符号表 用于bugly排查后续问题 
* Jenkins 内配置Post-build Actions archieve the artifacts 保存指定目录位置的文件至根目录下**jenkins->jobs->{环境}->builds->{构建号}**


## 注意
* 所有基于.plist文件的修改、版本号的修改，在编辑、打包、发布过后请修改回原值，防止与git版本冲突
* 后续版本不在动态修改.plist文件内的数据 保证一版本对应一文件 方式多线程模式下 同时打包引起的混乱


[Git流程规范Url]: https://zhuanlan.zhihu.com/p/66048537