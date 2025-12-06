### alembic
```bash
sudo apt install mysql-server

alembic init alembic

# 打开 alembic.ini 文件，找到 sqlalchemy.url 配置项，将其修改为你的 PostgreSQL 数据库连接字符串。例如：
sqlalchemy.url = mysql+pymysql://username:password@localhost/db_name

# 在 alembic/env.py 文件中，找到 target_metadata 变量，并将其设置为你的 SQLAlchemy 模型的 Base.metadata
from db_config import Base
target_metadata = Base.metadata

# 在修改了 SQLAlchemy 模型后，你可以使用以下命令创建一个新的迁移脚本：
alembic revision --autogenerate -m "your migration message"

alembic history

alembic upgrade head

alembic downgrade <revision_id>

alembic current

```


### mysql 防止暴力破解
```bash

iptables -A INPUT -p tcp --dport 3306 -m state --state NEW -m recent --set

iptables -A INPUT -p tcp --dport 3306 -m state --state NEW -m recent --update --seconds 60 --hitcount 5 -j DROP

iptables -D INPUT -p tcp --dport 3306 -m state --state NEW -m recent --set
iptables -D INPUT -p tcp --dport 3306 -m state --state NEW -m recent --update --seconds 60 --hitcount 5 -j DROP

sudo iptables -A INPUT -p tcp --dport 3306 -m state --state NEW ! -s 127.0.0.1 -m recent --set
sudo iptables -A INPUT -p tcp --dport 3306 -m state --state NEW ! -s 127.0.0.1 -m recent --update --seconds 60 --hitcount 5 -j DROP

sudo iptables -L INPUT -v -n --line-numbers

```

```sql
mysql -u root -p

SHOW GRANTS FOR 'wenhao'@'%';

GRANT PROCESS ON *.* TO 'wenhao'@'%';
FLUSH PRIVILEGES;

```

从你提供的信息来看，用户 `wli` 确实存在于 MySQL 用户表中，并且可以从 `%` 和 `localhost` 连接。为了确保权限正确分配，你可以按照以下步骤进行操作：

### 1. 删除现有用户
首先，删除现有的用户 `wli`，包括从 `%` 和 `localhost` 连接的用户：

```sql
SELECT user, host FROM mysql.user;
SHOW GRANTS FOR 'wenhao'@'118.250.176.65';
RENAME USER 'wenhao'@'118.250.176.65' TO 'wenhao'@'%';

DROP USER 'wli'@'%';
DROP USER 'wli'@'localhost';
```

### 2. 创建新用户
然后，重新创建用户 `wli`，并设置密码：

```sql
CREATE USER 'wli'@'%' IDENTIFIED BY 'your_password';

RENAME USER 'wenhao'@'%' TO 'wenhao'@'118.250.176.65';
```

### 3. 赋予权限
赋予用户 `wli` 对 `miniprogram` 数据库的 `SELECT` 和 `INSERT` 权限：

```sql
GRANT SELECT, INSERT ON miniprogram.* TO 'wli'@'%';
```

### 4. 刷新权限
刷新权限以确保更改生效：

```sql
FLUSH PRIVILEGES;
```

### 5. 检查权限
最后，检查用户 `wli` 的权限是否正确分配：

```sql
SHOW GRANTS FOR 'wli'@'%';

SELECT user, host FROM mysql.user;
```

### 完整命令示例

```sql
-- 删除现有用户
DROP USER 'wli'@'%';
DROP USER 'wli'@'localhost';

-- 创建新用户
CREATE USER 'wli'@'%' IDENTIFIED BY 'your_password';

-- 赋予权限
GRANT SELECT, INSERT ON miniprogram.* TO 'wli'@'%';
GRANT UPDATE ON miniprogram.user TO 'wli'@'%';

-- 刷新权限
FLUSH PRIVILEGES;

-- 检查权限
SHOW GRANTS FOR 'wli'@'%';
```

### 解释
- `DROP USER 'wli'@'%';` 和 `DROP USER 'wli'@'localhost';`: 删除从 `%` 和 `localhost` 连接的用户 `wli`。
- `CREATE USER 'wli'@'%' IDENTIFIED BY 'your_password';`: 创建一个新的用户 `wli`，并设置密码。
- `GRANT SELECT, INSERT ON miniprogram.* TO 'wli'@'%';`: 赋予用户 `wli` 对 `miniprogram` 数据库的 `SELECT` 和 `INSERT` 权限。
- `FLUSH PRIVILEGES;`: 刷新权限，使更改立即生效。
- `SHOW GRANTS FOR 'wli'@'%';`: 检查用户 `wli` 的权限是否正确分配。

通过这些步骤，你应该能够确保用户 `wli` 具有正确的权限，并且可以从任何主机连接到 MySQL 服务器，只能够查看和插入 `miniprogram` 数据库中的数据。
