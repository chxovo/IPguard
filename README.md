IPGuard 🛡️
 
一个轻量级 IP 封禁列表仓库，用于收集和共享已知恶意 IP 地址，帮助服务器管理员快速抵御暴力破解、端口扫描等攻击行为。
 
项目说明
 
本仓库仅用于存储 IP 黑名单文本文件（iptxt），所有文件均为纯文本格式，每行一个 IP 地址（可附带注释说明），方便直接导入防火墙或安全工具进行批量封禁。
 
- 文件格式：纯文本  .txt ，每行格式为  IP地址 # 注释说明 （例如  204.76.203.83 # SSH暴力破解攻击源 ）
- 使用场景：适用于  iptables 、 ufw 、 fail2ban  等防火墙/安全工具的批量导入
- 数据来源：个人服务器安全日志、主动攻击记录、公开恶意 IP 情报
 
快速使用
 
1. 下载黑名单
 
bash
  

# 克隆仓库到本地
git clone https://github.com/chxovo/IPguard.git
cd IPguard
 
 
2. 批量封禁（以 iptables 为例）
 
bash
  

# 读取 txt 文件并批量添加封禁规则（忽略注释行）
while IFS= read -r line; do
  ip=$(echo "$line" | grep -E '^[0-9.]+' | awk '{print $1}')
  if [ -n "$ip" ]; then
    sudo iptables -A INPUT -s "$ip" -j DROP
  fi
done < blacklist.txt

# 保存规则（避免重启后失效）
sudo iptables-save > /etc/iptables/rules.v4
 
 
3. 批量封禁（以 ufw 为例）
 
bash
  

while IFS= read -r line; do
  ip=$(echo "$line" | grep -E '^[0-9.]+' | awk '{print $1}')
  if [ -n "$ip" ]; then
    sudo ufw deny in from "$ip" to any
  fi
done < blacklist.txt
sudo ufw reload
 
 
协议说明
 
本项目所有 IP 黑名单数据遵循 Creative Commons Attribution 4.0 International (CC BY 4.0) 协议。
 
协议核心要求
 
- ✅ 任何人可以自由共享、改编、使用本仓库数据（包括商业用途）
- ✅ 必须保留原作者信息：使用时需注明来源为  https://github.com/chxovo/IPguard 
- ✅ 必须保留数据注释：不得删除或修改 IP 地址对应的攻击类型、来源等注释信息
- ✅ 若对外发布衍生数据，需同样以 CC BY 4.0 协议共享，并标注修改内容
 
协议全文
 
Creative Commons Attribution 4.0 International
 
注意事项
 
- 本项目仅为个人安全辅助工具，不保证覆盖所有恶意 IP，建议结合  fail2ban  等动态防护工具使用
- 封禁前请确认 IP 无误，避免误封正常业务流量
- 定期更新黑名单文件，同步最新的恶意 IP 数据
- 若发现误封或需要补充数据，欢迎提交 Issue 或 Pull Request
