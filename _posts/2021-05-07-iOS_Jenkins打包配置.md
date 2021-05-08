---
layout: post
title: "iOS_Jenkins打包配置"
date: 2021-05-07
categories: iOS
---

#### 入职以来一直负责项目的Jenkins自动化打包机配置 抽空整理一下相关内容 部分内容为前期笔记记录迁移 可能和现有版本有所修改 各有所长

### 自动化打包分为四个环境
* 开发环境
* 测试环境
* 预发布环境
* 正式环境
### Jenkins结构化参数/Git分支配置
* AppChannel 对应四个打包环境
* GitBranch 对应不同分支 （分支详情见 [Git流程规范][https://zhuanlan.zhihu.com/p/66048537 ]）
* 设置BOOL按钮upload 可选 release环境是否上传至appstore -注意如果选择upload则打出的包不是ad-hoc点对点模式，因此发布到蒲公英的无法安装
* 整体编译流程执行build.sh脚本文件







### Post-build Actions archieve the artifacts