```scss
// 定義主要流程圖樣式
.flowchart {
  font-family: monospace;
  white-space: pre;
  color: #0d6efd; // 設定主要字體顏色
  background: #f8f9fa;
  padding: 15px;
  border-radius: 5px;
  display: inline-block;
  font-size: 14px;
}

// 主要框架設計
.flowchart::before {
  content: "
  +----------------------+
  |  單台 API 伺服器異常排查 |
  +----------------------+
        |
        v
  +------------------+
  |  1. 檢查系統資源   |
  +------------------+
        |
        |-- CPU / 記憶體 (top, free -m)
        |-- 磁碟 I/O (iostat)
        |
        v
  +------------------+
  |  2. 檢查應用層     |
  +------------------+
        |
        |-- API 日誌 (tail -f)
        |-- 服務健康狀態 (curl /health)
        |
        v
  +------------------+
  |  3. 檢查網路      |
  +------------------+
        |
        |-- 檢查 ELB (describe-instance-health)
        |-- 檢查 Security Group
        |
        v
  +------------------+
  |  4. 檢查依賴服務   |
  +------------------+
        |
        |-- 資料庫 (mysql -h)
        |-- Redis (redis-cli ping)
        |-- S3 (aws s3 ls)
  ";
}
```
```d2
# 單台 API 伺服器異常排查流程圖
A: "API 伺服器異常"
B: "1. 檢查系統資源"
C: "2. 檢查應用層"
D: "3. 檢查網路"
E: "4. 檢查依賴服務"
F: "問題修復"

# 檢查系統資源的細項
B1: "CPU / 記憶體過高？"
B2: "磁碟 I/O 過載？"
B3: "是否有異常進程？"

# 檢查應用層的細項
C1: "查看 API 日誌 (tail -f)"
C2: "API 服務健康狀態 (curl /health)"
C3: "是否有大量 500 錯誤？"

# 檢查網路的細項
D1: "ELB 健康檢查 (describe-instance-health)"
D2: "Security Group / VPC ACL 是否異常？"
D3: "機器是否可連線 (ping / curl)"

# 檢查依賴服務的細項
E1: "資料庫 RDS 是否異常？"
E2: "Redis 連線超時？"
E3: "S3 是否讀寫慢？"

# 連接流程
A -> B -> B1 -> B2 -> B3 -> C
C -> C1 -> C2 -> C3 -> D
D -> D1 -> D2 -> D3 -> E
E -> E1 -> E2 -> E3 -> F
```


