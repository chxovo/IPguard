# IPGuard 🛡️
一个轻量级 IP 封禁列表仓库，用于收集和共享已知恶意 IP 地址，帮助服务器管理员快速抵御暴力破解、端口扫描等攻击行为。

## 项目说明
本仓库仅用于存储 **IP 黑名单文本文件（iptxt）**，所有文件均为纯文本格式，每行一个 IP 地址（可附带注释说明），方便直接导入防火墙或安全工具进行批量封禁。

- **文件格式**：纯文本 `.txt`，每行格式为 `IP地址 # 注释说明`（例如 `204.76.203.83 # SSH暴力破解攻击源`）
- **使用场景**：适用于 `iptables`、`ufw`、`fail2ban` 等防火墙/安全工具的批量导入
- **数据来源**：个人服务器安全日志、主动攻击记录、公开恶意 IP 情报

## 快速使用

### 1. 下载黑名单
```bash
# 克隆仓库到本地
git clone https://github.com/chxovo/IPguard.git
cd IPguard

### 2.  批量封禁（以 iptables 为例）
# 读取 txt 文件并批量添加封禁规则（自动忽略注释行）
while IFS= read -r line; do
  ip=$(echo "$line" | grep -E '^[0-9.]+' | awk '{print $1}')
  if [ -n "$ip" ]; then
    sudo iptables -A INPUT -s "$ip" -j DROP
  fi
done < blacklist.txt

# 保存规则（避免重启后失效）
sudo iptables-save > /etc/iptables/rules.v4
