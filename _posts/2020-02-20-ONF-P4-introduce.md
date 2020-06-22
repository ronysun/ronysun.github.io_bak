---
layout: post
title: "P4 introduce"
categories:
  - OpenNetworking
---

# P4 introduce
## 摘要
开发环境：windows10 virtualBox vagrant  
[官方文档](https://www.opennetworking.org/p4/)  
[代码库](https://github.com/p4lang)

## P4中的一些概念
1. P4是协议不相关的，所谓协议相关就是只能根据现有的协议来进行编程，例如OpenFlow1.0时有12个字段（IP，MAC等），但这明显是远远不够的。那么P4就解决了这个问题
1. P4可以定义自己的匹配字段和动作（action），从而定义流表，进而形成流水线
1. PISA（Protocol Independent Switch Architecture），一种可编程网络芯片架构
1. P4 runtime的定位与OpenFlow一致，与P4配合使用
1. P4c是P4的编译器项目
1. behavioral-model（bmv2）是一个支持P4的软交换机version2
1. PI是P4 runtime的实现，用于控制面对数据面的控制
1. mininet可以构建虚拟网络，用于学习和测试

P4  target architecture
![P4 V1model](https://build-a-router-instructors.github.io/images/V1Model.png)
The V1Model consists of six P4 programmable components:
- Parser
- Checksum verification control block
- Ingress Match-Action processing control block
- Egress Match-Action processing control block
- Checksum update control block
- Deparser

## 开发环境部署
[Get Start](https://p4.org/p4/getting-started-with-p4.html)  
[官方提供的p4开发虚机镜像](https://drive.google.com/uc?id=1lYF4NgFkYoRqtskdGTMxy3sXUV0jkMxo&export=download)  
[国内某开发者的虚机镜像](https://share.weiyun.com/581m3WN)  
由于P4开发环境各个组件之间有依赖，且兼容性做的不好，所以不要单独升级某一组件到最新版，可能与其他组件不兼容
1. virtualbox创建Ubuntu16.04，desktop版，官方建议的版本，其他版本未经测试
1. 执行root-bootstrap.sh和user-bootstrap.sh，代码全部修改为gitee上的代码
1. 修改grpc中的submodule为gitee中的地址。修改.gitmodule，执行git submodule sync

## Firsh program  
参考 https://github.com/p4lang/tutorials/tree/master/exercises/basic
1. 在P4 tutorial的exercises/中有多个实例， 下是一个需要编程题（编写TODO部分），其子目录solution中的*.p4是添加了TODO部分的参考答案。
1. 在该basic目录下执行make run会执行p4c编译代码，并加载到ovs中，进入mininet进行测试。
1. 当basic.p4替换为solution中的文件时，各个host就可以ping通了。

## 参考资料
1. https://p4.org/p4-spec/docs/P4-16-v1.2.0.html
1. https://www.sdnlab.com/22466.html
1. https://build-a-router-instructors.github.io/deliverables/p4-mininet/
1. https://www.eefocus.com/fpga/453687
