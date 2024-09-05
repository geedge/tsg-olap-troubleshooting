# TSG OLAP TROUBLESHOOTING

## End-to-End Test

### 概述

支持对Logs，Metrics和Files进行端到端业务自检。
数据流：`Smaple Datasets -> Kafka Topic -> FLINK ETL -> Storage DB -> QGW API`。

### 环境依赖

- `e2e-mockdata-generator`样例数据生成工具，需JDK11环境
- `Newman` 使用参考[帮助文档](https://learning.postman.com/docs/collections/using-newman-cli/newman-options/)

### 命令详解

`./e2e_test.sh -h`

```shell
Usage: ./e2e_test.sh [options]

Options:
  -g <type>           Generate data (logs, metrics, files)
  -d <type>           Run diagnostic report (logs, metrics, files)
  -c                  Clear test data(Only support clear files)
  -a                  Perform all operations: generate data, run diagnostics, and clear data
  -i <key=value,...>  Set environment variable (data_center, hos_ip)
  -v                  Enable verbose reporting
  -e                  Enable emojitrain reporting
  -h                  Show this help message

Examples:
  ./e2e_test.sh -a -e                                   Perform all operations and enable emojitrain reporting
  ./e2e_test.sh -g logs -i data_center=my_data_center   Generate log data at my_data_center
  ./e2e_test.sh -d logs -v                              Run diagnostics on logs data with verbose reporting
  ./e2e_test.sh -d metrics -v                           Run diagnostics on metrics data with verbose reporting
  ./e2e_test.sh -g logs                                 Generate log data use default data center
  ./e2e_test.sh -c                                      Clear test data
  
```
### 使用步骤
#### Step1: 修改`enviroment.json`
Newman CLI使用QGW HTTP REST API 输出诊断报告,需要添加访问IP。
```json
[
  {
    "key": "qgw_ip",
    "value": "127.0.0.1",
    "type": "default",
    "enabled": true
  },
  {
    "key": "hos_token",
    "value": "c21f969b5f03d33d43e04f8f136e7682",
    "type": "secret",
    "enabled": true
  }
]
```
#### Step2: 修改`e2e_test.sh`

- 增加每个分中心的kafka broker地址

```shell
  # [data_center_name]:kafka_server_list
  declare -A KAFKA_SERVERS=(
    ["tsg_olap_dc_a"]="192.168.44.11:9092"
    ["tsg_olap_dc_b"]="192.168.44.11:9092"
  )
```
- 增加每个分中心的HOS访问地址
```shell
  # [data_center_name]:hos_endpoint_uri
  declare -A HOS_ENDPOINTS=(
    ["tsg_olap_dc_a"]="192.168.44.11"
    ["tsg_olap_dc_b"]="192.168.44.11"
  )
```



## Function Test
### 概述
支持对QGW API进行功能验证，包括：查询、数据集、数据库、工具、故障诊断、HOS、管理等。

### 环境依赖
- `Newman` 使用参考[帮助文档](https://learning.postman.com/docs/collections/using-newman-cli/newman-options/)

### 命令详解

`./function_test.sh -h`

```shell
Usage: function_test.sh [-f FOLDER_NUMBER] [-a] [-v] [-e]

Options:
  -f FOLDER_NUMBER  Specify a single folder to run from a collection:
                    1. Query
                    2. Dataset
                    3. Database
                    4. Util
                    5. Troubleshooting
                    6. HOS
                    7. Knowledge Base File
  -a                Run all options [1-7]
  -v                Enable verbose reporting
  -e                Enable emojitrain reporting
  -h, --help        Show this help message and exit
```


