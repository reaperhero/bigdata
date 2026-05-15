# OpenLDAP 使用教程

## 常用命令

### 搜索

```bash
# 搜索所有用户
ldapsearch -x -b "ou=users,dc=example,dc=com" "(objectClass=inetOrgPerson)"

# 搜索特定用户
ldapsearch -x -b "dc=example,dc=com" "(uid=alice)"
```

### 添加用户

创建 `user.ldif`：

```ldif
dn: uid=alice,ou=users,dc=example,dc=com
objectClass: inetOrgPerson
objectClass: posixAccount
cn: Alice Smith
sn: Smith
uid: alice
uidNumber: 1001
gidNumber: 1001
homeDirectory: /home/alice
loginShell: /bin/bash
userPassword: {SSHA}encrypted_password
```

```bash
ldapadd -x -D "cn=admin,dc=example,dc=com" -W -f user.ldif
```

### 修改条目

```bash
ldapmodify -x -D "cn=admin,dc=example,dc=com" -W <<EOF
dn: uid=alice,ou=users,dc=example,dc=com
changetype: modify
replace: mail
mail: alice@newdomain.com
EOF
```

### 删除条目

```bash
ldapdelete -x -D "cn=admin,dc=example,dc=com" -W "uid=alice,ou=users,dc=example,dc=com"
```

### 备份

```bash
sudo slapcat -v -l backup.ldif
```

## 与大数据组件集成

- **Ranger**：配置 Ranger UserSync 从 LDAP 同步用户和组
- **Hadoop**：配置 hadoop.security.group.mapping 使用 LDAP 获取用户组信息
- **Hive**：HiveServer2 可配置 LDAP 认证模式

## 可视化管理

推荐使用 **phpLDAPadmin** 或 **LDAP Admin** 等工具进行图形化管理。
