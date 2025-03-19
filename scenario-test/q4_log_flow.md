- 前情提要-我也沒有使用過kibana的經驗
透過爬文跟chatgpt學習
`預設服務是Kubernetes`
1.新服務應該<font color=red>輸出</font>`JSON`結構化日誌，方便解析。
2.在Kubernetes內佈署Fluent Bit DaemonSet來<font color=red>收集</font>font日誌。
3.Fluent Bit轉發日誌給Fluentd(<font color=red>解析</font>格式，標準化)，Fluentd<font color=red>轉送</font>Elasticsearch。
4.Elasticsearch會根據索引策略<font color=red>儲存</font>日誌，開發者可以用Kibana搜尋錯誤。
5.Kibana 透過錯誤類型(ERROR，WARN等等)篩選後<font color=red>視覺化</font>，也可建立<font color=red>Dashboard</font>(戰情室)

- 索引管理與查詢示例
`index命名`:`myapp-logs-YYYY.MM.DD`
`錯誤查詢`:`level: ERROR`
`查詢服務`: `kubernetes.namespace_name: asiaYo`

- 建議架構流程
新服務(Pod)─>Fluent Bit (DaemonSet)─>Fluentd─>Elasticsearch─>Kibana(可視化)
- 如果預設產出格式為json可透過fluent bit直接解析(高效)

1.**底層語言**：Fluent Bit是用 `C 語言`開發的，效能比Fluentd（`Ruby`）更優。
2.**低記憶體佔用**：Fluent Bit 記憶體使用率低（適合邊緣設備與大規模 Kubernetes 叢集）。
3.**內建多線程**：支援高併發，能快速處理日誌。轉發至 Fluentd 進一步處理。
