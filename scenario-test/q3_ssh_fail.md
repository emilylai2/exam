


```scss
.flowchart {
  font-family: monospace;
  white-space: pre;
  color: #0d6efd; // 設定主要文字顏色
  background: #f8f9fa;
  padding: 15px;
  border-radius: 5px;
  display: inline-block;
  font-size: 14px;
}

.flowchart::before {
  content: "
  +--------------------------+
  |  API 伺服器異常排查流程  |
  +--------------------------+
        |
        v
  +-----------------------+
  |  1. 檢查 AWS EC2 狀態 |
  +-----------------------+
        |
        v
  +---------------------------------+
  |  2. 嘗試使用 EC2 Serial Console |
  +---------------------------------+
        |
        v
  +------------------------------------------------+
  |  3. 如果 Serial Console 失敗，掛載磁碟到新 EC2 |
  +------------------------------------------------+
        |
        v
  +------------------------------------------+
  |  4. 檢查 SSH 設定 (/etc/ssh/sshd_config) |
  +------------------------------------------+
        |
        v
  +--------------------------+
  |  5. 檢查磁碟空間 (df -h) |
  +--------------------------+
        |
        v
  +-------------------------+
  |  6. 檢查 CPU 負載 (top) |
  +-------------------------+
        |
        v
  +--------------------------------------------------------+
  |  7. 修復 SSH Key (/home/ec2-user/.ssh/authorized_keys) |
  +--------------------------------------------------------+
        |
        v
  +----------------------------------+
  |  ✅ 重新啟動 EC2，測試 SSH 連線  |
  +----------------------------------+
  ";
}
```

## 檢查 EC2 的系統健康狀態
- 進入AWS EC2控制台(EC2 Dashboard)，檢查該機器的狀態檢查(Status Checks)：
  * System Reachability check failed → 表示EC2內部系統異常
  * Instance Reachability check failed → 表示OS內部異常，例如SSH服務掛掉
## 使用EC2 Serial Console
- 在 AWS Console 內：
   * 選擇EC2
   * 點擊Connect → EC2 Serial Console
   * 嘗試登入系統(root / ec2-user)
   * 使用systemctl status sshd檢查SSH服務狀態
   *  **如果EC2 Serial Console可用，則問題可能與SSH設定有關**
## 救援模式(Recovery Mode)
- 如果Serial Console不適用，或仍無法登入，可使用"救援模式"掛載磁碟檢查
  * 建立一個新的EC2
  * 停止(Stop)無法SSH的EC2
  * 將該EC2的磁碟(root volume)附加到新的EC2
  * 在新EC2上掛載該磁碟
  * 檢查SSH設定 /etc/ssh/sshd_config
  * 檢查`.bashrc`是否有異常命令

## 可能的SSH無法登入原因

### 1. SSH 服務異常
- SSH 服務 (sshd) 掛掉
- /etc/ssh/sshd_config 設定錯誤
- 系統 iptables 或 firewalld 阻擋 SSH
### 2. EC2 磁碟空間已滿
- 若 EC2 磁碟空間已滿 (100%)，SSH 可能無法啟動
- 清理 /var/log/內的log：
- 釋放 /tmp/ 空間：
### 3. EC2 CPU 過載 (High Load)
- CPU佔用率過高，導致SSH連線超時：
- 嘗試降低負載：
```bash
$ sudo systemctl stop unnecessary-service
```
### 4. SSH 金鑰損壞 / 權限錯誤
- SSH Key被誤刪（或.ssh/authorized_keys錯誤）
- 修復方式:按照救援模式掛載磁碟然後手動新增或修復(cat ~/.ssh/id_rsa.pub 將公鑰貼上)
```bash
$ vim /mnt/recovery/home/ec2-user/.ssh/authorized_keys
```



