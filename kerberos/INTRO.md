# Kerberos 组件介绍

## 概述

Kerberos 是一种计算机网络认证协议，它允许通信双方在非安全网络环境下通过票据（Ticket）机制安全地证明身份。在大数据领域，Kerberos 是 Hadoop 生态安全认证的标准方案，为 HDFS、HBase、Hive、Kafka 等组件提供统一的强身份认证。

## 核心特性

- **强身份认证**：防止窃听和重放攻击
- **基于票据（Ticket）**：用户获取 TGT（Ticket-Granting Ticket）后可访问多个服务
- **单点登录（SSO）**：一次认证即可访问所有已授权的服务
- **相互认证**：客户端和服务端双向验证身份

## 核心概念

| 概念 | 说明 |
|------|------|
| KDC | Key Distribution Center，密钥分发中心，Kerberos 的核心服务 |
| Realm | 认证域，如 `EXAMPLE.COM`，所有 Principal 属于某个 Realm |
| Principal | 认证主体，格式为 `name/instance@REALM`，如 `hdfs/node1@EXAMPLE.COM` |
| Keytab | 存储 Principal 密钥的文件，用于服务的无密码认证 |
| TGT | Ticket-Granting Ticket，由 KDC 颁发的初始票据 |
| Service Ticket | 用于访问特定服务的票据 |
