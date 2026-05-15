# OpenLDAP 组件介绍

## 概述

OpenLDAP 是轻型目录访问协议（LDAP）的开源实现，常用于集中管理用户账号、密码及组织架构。在大数据平台中，OpenLDAP 通常作为统一身份认证源，配合 Kerberos 和 Ranger 实现用户集中管理和访问控制。

## 核心特性

- **层次化目录结构**：采用树状 DIT（Directory Information Tree）组织数据
- **高读取性能**：针对读多写少的场景优化
- **标准协议**：兼容 LDAP v3 标准，与各种应用无缝集成
- **丰富的安全机制**：支持 TLS/SSL 加密、SASL 认证

## 核心概念

| 概念 | 说明 |
|------|------|
| DIT | Directory Information Tree，目录的层次结构 |
| DN | Distinguished Name，条目的唯一标识，如 `uid=alice,ou=users,dc=example,dc=com` |
| Entry | 目录中的一条记录 |
| Attribute | 条目的属性，如 uid、cn、mail |
| ObjectClass | 定义条目可以包含哪些属性 |
| LDIF | LDAP Data Interchange Format，用于导入导出数据的文本格式 |
