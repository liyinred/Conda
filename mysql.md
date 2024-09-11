```sql
SELECT user, host FROM mysql.user WHERE user = 'wli';
```

从你提供的 `SHOW GRANTS FOR 'wli'@'%'` 的结果来看，用户 `wli` 确实已经被赋予了对 `miniprogram` 数据库的 `SELECT` 和 `INSERT` 权限。这意味着权限设置是正确的。

### 可能的原因和解决方法

1. **权限缓存**：有时权限更改可能不会立即生效，确保你已经刷新了权限。你可以再次执行 `FLUSH PRIVILEGES;` 命令。

2. **连接信息**：确保你使用的是正确的用户名和密码连接到 MySQL 服务器。你可以使用以下命令测试连接：

   ```sql
   mysql -u wli -p -h your_mysql_host
   ```

3. **数据库和表**：确保 `miniprogram` 数据库和其中的表确实存在。你可以使用以下命令检查：

   ```sql
   SHOW DATABASES LIKE 'miniprogram';
   USE miniprogram;
   SHOW TABLES;
   ```

4. **用户权限**：确保你当前连接的用户是 `wli`，并且你正在尝试插入数据的表确实在 `miniprogram` 数据库中。

### 检查当前用户
你可以使用以下命令检查当前连接的用户：

```sql
SELECT CURRENT_USER();
```

### 检查数据库和表
确保你正在操作的数据库和表是正确的：

```sql
USE miniprogram;
SHOW TABLES;
```

### 测试插入操作
尝试在 `miniprogram` 数据库中的某个表中插入一行数据，确保权限设置正确：

```sql
USE miniprogram;
INSERT INTO your_table_name (column1, column2) VALUES ('value1', 'value2');
```

### 完整命令示例

```sql
-- 刷新权限
FLUSH PRIVILEGES;

-- 检查当前用户
SELECT CURRENT_USER();

-- 检查数据库和表
SHOW DATABASES LIKE 'miniprogram';
USE miniprogram;
SHOW TABLES;

-- 测试插入操作
INSERT INTO your_table_name (column1, column2) VALUES ('value1', 'value2');
```

### 常见问题
- **权限缓存**：有时权限更改可能不会立即生效，确保你已经刷新了权限。
- **用户名和密码**：确保你使用的是正确的用户名和密码连接到 MySQL 服务器。
- **数据库和表**：确保 `miniprogram` 数据库和其中的表确实存在。

如果你仍然遇到问题，请提供更多的错误信息或日志，以便进一步诊断。
