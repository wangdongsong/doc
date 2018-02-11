
## 重写IP
<pre>
scrape_configs:
  - job_name: 'test-server-metrics'
    scrape_interval: 15s
    metrics_path: '/pmetrics'
    static_configs:
      - targets: ['ip']
        labels:
          group: 'test-server-metrics'
    relabel_configs:
		#重新增加ip的label，抓取时instance=ip:port
      - source_labels: [__address__]
        target_label: ip
		#抓取之前将ip增加上端口
      - source_labels: [__address__]
        regex: (.*)
        target_label: __address__
        replacement: ${1}:30074
</pre>


updated
