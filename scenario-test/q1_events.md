
#  AWS 上的高可用性 (HA) 和自動擴展 (Auto Scaling)

## 基礎架構
- AWS Auto Scaling(短期水平擴展)
  * EC2：使用 Auto Scaling Group (ASG) 來根據負載動態擴展 Web 伺服器。
  * Fargate/ECS/EKS：如果使用容器化，確保 Kubernetes HPA (Horizontal Pod Autoscaler) 可以根據 CPU/記憶體負載擴展 Pod 數量。
  * Lambda：如果網頁主要是 API 服務，使用 AWS Lambda 無伺服器架構，可以自動應對請求流量。
- 使用 Elastic Load Balancer (ALB/NLB)
  * 使用 AWS Application Load Balancer(ALB)，確保流量能夠自動分配至不同的後端且可以設定根據loading導流。
  * 若API服務需要更高效能，考慮AWS API Gateway+Lambda 或 Network Load Balancer(NLB)。
- CDN 快取 (CloudFront)
  * 假如活動網頁內容是靜態的（如HTML, CSS, JS），將其存放在S3+ CloudFront，以降低對Web伺服器的壓力。
  * CloudFront可以根據全球用戶位置提供最佳化內容，降低伺服器負擔。 
 
 
## 資料庫
- 使用 Amazon RDS + Read Replica
  * 若使用 RDS (MySQL/PostgreSQL)，啟用 Read Replica 來分散讀取流量，確保資料庫不會過載。
  * 啟用 RDS Auto Scaling，讓資料庫能根據需求自動擴展。
- 使用 Amazon Aurora
  * Aurora 具備高可用性，可以自動擴展讀取節點，並在failover時切換主節點，確保資料庫穩定。
- 若使用 NoSQL (DynamoDB)
  * 啟用 DynamoDB Auto Scaling，根據請求速率自動調整吞吐量。
  * 啟用 DAX(DynamoDB Accelerator)來加速查詢。
## 監控與安全
- Amazon `CloudWatch`+`AWS Lambda`
  * 設定CloudWatch指標(CPU,記憶體,RDS連線數)
  * 設定CloudWatch Alarms在負載過高時自動通知團隊
  * 使用Lambda自動擴展EC2/容器數量來處理突發流量
- AWS `WAF`(Web Application Firewall)
  * 若擔心`DDoS`攻擊，啟用AWS WAF來保護API Gateway或ALB。
  * 設定IP限制、速率限制 (Rate Limiting)。

## DR
- Multi-AZ架構
  * `東京`:`ap-northeast-1` 距離最近且有 3 Zone 
  東京為最佳選:
  1.東京 (ap-northeast-1) 是AWS在亞洲最主要的數據中心，有較多的`服務`與`AZs`支援。
  2.東京 (ap-northeast-1) 幾乎支援所有AWS服務，包括最新的 AI/機器學習、Serverless、Edge Computing、Data Lake方案等。
  3.大部分亞太區(APAC)企業，尤其是全球企業(如 Netflix、Airbnb、任天堂)，都將東京作為亞洲的AWS主要區域。
  * `大阪`:`ap-northeast-3` 適合DR，因為只有 1 Zone
  * `新加坡`:`ap-southeast-1` 適合DR 3 Zone
  * `首爾`:`ap-northeast-2` 國際擴展&適合DR 3 Zone
- 備份方案
  * S3/RDS啟用每日備份，確保資料安全。
  * CloudFront+S3讓靜態網站仍可運行即使後端失敗。
```scss
使用者請求 ─→ CloudFront (CDN) ─→ S3 (靜態內容)
                            │
                            ├──→  API Server (EC2/Lambda)  ❌ (若失敗)
                            ├──→  回傳快取內容 ✅
                            └──→  回傳維護頁面 (Custom Error Page)
```
