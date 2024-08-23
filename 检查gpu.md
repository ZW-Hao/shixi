

```shell
#!/bin/bash

# 配置参数
EXPECTED_GPUS=8
LOG_DIR="/var/log/gpuLog"
REMOTE_USER="root"
REMOTE_HOST="10.9.10.220"
REMOTE_DIR="/root/gpuLog"
SSH_PRIVATE_KEY="/root/infra.key"

# 获取本地服务器的主IP地址
LOCAL_IP=$(hostname -I | cut -d' ' -f1)

# 当前时间戳，用于创建唯一的日志文件名
TIMESTAMP=$(date +"%Y%m%d-%H%M%S")
LOG_FILE="${LOG_DIR}/${LOCAL_IP}_gpu_alert_${TIMESTAMP}.log"


# 检查GPU数量，并写入具有时间戳和IP地址的日志文件
GPU_COUNT=$(nvidia-smi -L | grep -c "GPU")
if [[ "$GPU_COUNT" -lt "$EXPECTED_GPUS" ]]; then
    echo "$(date) [ALERT] Server IP: $LOCAL_IP, Expected $EXPECTED_GPUS GPUs, found only $GPU_COUNT." | tee -a "$LOG_FILE"
    
    # 将日志文件传输到远程服务器
    scp -i $SSH_PRIVATE_KEY $LOG_FILE $REMOTE_USER@$REMOTE_HOST:$REMOTE_DIR
    
    
fi
```





```shell
#!/bin/bash

# 配置参数
EXPECTED_GPUS=8
LOG_DIR="/var/log/gpu_alerts"
REMOTE_USER="remoteuser"
REMOTE_HOST="remote.server.com"
REMOTE_DIR="/path/to/remote/logs"

# 获取本地服务器的主IP地址
IP_ADDRESS=$(hostname)

# 当前时间戳，用于创建唯一的日志文件名
TIMESTAMP=$(date +"%Y%m%d-%H%M%S")
LOG_FILE="$LOG_DIR/gpu_alert_$TIMESTAMP.log"

# 检查GPU数量，并写入具有时间戳和IP地址的日志文件
GPU_COUNT=$(nvidia-smi -L | grep -c "GPU")
if [[ "$GPU_COUNT" -lt "$EXPECTED_GPUS" ]]; then
    # 如果GPU数量不符合预期，则记录在日志文件中
    echo "$(date) [ALERT] Server IP: $IP_ADDRESS, Expected $EXPECTED_GPUS GPUs, found only $GPU_COUNT." | tee -a "$LOG_FILE"
    
    # 将日志文件传输到远程服务器
    scp -i "/root/key/infra.key" "$LOG_FILE" "$REMOTE_USER@$REMOTE_HOST:$REMOTE_DIR"
fi


```

```shell
#!/bin/bash

EXPECTED_GPUS=8
LOG_FILE="/var/log/gpu_alert.log"

GPU_COUNT=$(nvidia-smi -L | grep -c "GPU")

if [[ "$GPU_COUNT" -lt "$EXPECTED_GPUS" ]]; then
    echo "$(date) [ALERT] Expected $EXPECTED_GPUS GPUs, found only $GPU_COUNT." | tee -a "$LOG_FILE"
fi

```



```shell
#!/bin/bash

# 预期GPU数量
EXPECTED_GPUS=8

# 使用 nvidia-smi 获取当前GPU数量
GPU_COUNT=$(nvidia-smi --query-gpu=gpu_uuid --format=csv,noheader | wc -l)

# 飞书机器人的Webhook URL
FEISHU_WEBHOOK_URL="https://www.feishu.cn/flow/api/trigger-webhook/57e303b382eac3f14efa0c4f847f2dd2"

# 创建发送给飞书的消息内容
ALERT_MESSAGE="GPU Alert: Detected only $GPU_COUNT GPUs, which is less than the expected $EXPECTED_GPUS."

# 检查GPU数量是否小于预期
if [[ "$GPU_COUNT" -lt "$EXPECTED_GPUS" ]]; then
    # 如果GPU数量小于预期，使用curl发送告警到飞书群组
    curl -X POST -H "Content-Type: application/json" -d "{\"msg_type\":\"text\",\"content\":{\"text\":\"$ALERT_MESSAGE\"}}" $FEISHU_WEBHOOK_URL
fi

```





```shell
#!/bin/bash

# 预期GPU数量
EXPECTED_GPUS=8

# 获取当前时间戳
TIMESTAMP=$(date "+%Y-%m-%d %H:%M:%S")

# 获取服务器IP地址，这里以常见的eth0接口为例，请根据实际情况替换
LOCAL_IP=$(hostname -I | awk '{print $1}')

# 使用 nvidia-smi 获取当前GPU数量
GPU_COUNT=$(nvidia-smi --query-gpu=gpu_uuid --format=csv,noheader | wc -l)

# 飞书机器人的Webhook URL
FEISHU_WEBHOOK_URL="https://www.feishu.cn/flow/api/trigger-webhook/57e303b382eac3f14efa0c4f847f2dd2"

# 创建发送给飞书的消息内容，包括时间、IP地址和GPU数量
ALERT_MESSAGE="[$TIMESTAMP] GPU Alert on $LOCAL_IP: Detected only $GPU_COUNT GPUs, which is less than the expected $EXPECTED_GPUS."

# 检查GPU数量是否小于预期
if [[ "$GPU_COUNT" -lt "$EXPECTED_GPUS" ]]; then
    # 如果GPU数量小于预期，使用curl发送告警到飞书群组
    curl -X POST -H "Content-Type: application/json" \
        -d "{\"msg_type\":\"text\",\"content\":{\"text\":\"$ALERT_MESSAGE\"}}" \
        $FEISHU_WEBHOOK_URL
fi


#curl -X POST -H "Content-Type: application/json" -d "{\"msg_type\":\"text\",\"content\":{\"text\":\"GPU掉卡告警：机器IP $LOCAL_IP ，检测到的GPU数量少于预期。\"}}" $FEISHU_WEBHOOK_URL

```





```shell
#!/bin/bash

# 预期GPU数量
EXPECTED_GPUS=8

# 获取当前时间戳
TIMESTAMP=$(date "+%Y-%m-%d %H:%M:%S")

# 获取服务器IP地址，这里以常见的eth0接口为例，请根据实际情况替换
LOCAL_IP=$(hostname -I | awk '{print $1}')

# 使用 nvidia-smi 获取当前GPU数量
GPU_COUNT=$(nvidia-smi --query-gpu=gpu_uuid --format=csv,noheader | wc -l)

# 飞书机器人的Webhook URL
FEISHU_WEBHOOK_URL="https://open.feishu.cn/open-apis/bot/v2/hook/ecfed511-daa3-43d2-9673-f8807b274e1a"

# 创建发送给飞书的消息内容，包括时间、IP地址和GPU数量
ALERT_MESSAGE="[$TIMESTAMP] GPU Alert on $LOCAL_IP: Detected only $GPU_COUNT GPUs, which is less than the expected $EXPECTED_GPUS."

# 检查GPU数量是否小于预期
if [[ "$GPU_COUNT" -lt "$EXPECTED_GPUS" ]]; then
    # 如果GPU数量小于预期，使用curl发送告警到飞书群组
    curl -X POST -H "Content-Type: application/json" \
        -d "{\"msg_type\":\"text\",\"content\":{\"text\":\"GPU掉卡告警：机器IP $LOCAL_IP ，检测到的GPU数量少于预期。$ALERT_MESSAGE\"}}" \
        $FEISHU_WEBHOOK_URL
fi
echo "GPU掉卡告警：机器IP $LOCAL_IP ，检测到的GPU数量少于预期。"
```



```shell
#!/bin/bash

# 预期GPU数量
EXPECTED_GPUS=8

# 获取当前时间戳
TIMESTAMP=$(date "+%Y-%m-%d %H:%M:%S")

# 获取服务器IP地址，这里以常见的eth0接口为例，请根据实际情况替换
LOCAL_IP=$(hostname -I | awk '{print $1}')

# 使用 nvidia-smi 获取当前GPU数量
GPU_COUNT=$(nvidia-smi --query-gpu=gpu_uuid --format=csv,noheader | wc -l)

# 飞书机器人的Webhook URL
FEISHU_WEBHOOK_URL="https://open.feishu.cn/open-apis/bot/v2/hook/ecfed511-daa3-43d2-9673-f8807b274e1a"

# 创建发送给飞书的消息内容，包括时间、IP地址和GPU数量
ALERT_MESSAGE="[$TIMESTAMP] GPU Alert on $LOCAL_IP: Detected only $GPU_COUNT GPUs, which is less than the expected $EXPECTED_GPUS."

# 检查GPU数量是否小于预期
if [[ "$GPU_COUNT" -lt "$EXPECTED_GPUS" ]]; then
    # 如果GPU数量小于预期，使用curl发送告警到飞书群组
    curl -X POST -H "Content-Type: application/json" \
        -d "{\"msg_type\":\"text\",\"content\":{\"text\":\"GPU掉卡告警：机器IP $LOCAL_IP ，检测到的GPU数量少于预期。$ALERT_MESSAGE\"}}" \
        $FEISHU_WEBHOOK_URL
fi


```





```shell
#!/bin/bash

# 预期GPU数量
EXPECTED_GPUS=8

# 获取当前时间戳
TIMESTAMP=$(date "+%Y-%m-%d %H:%M:%S")

# 获取服务器IP地址，这里以常见的eth0接口为例，请根据实际情况替换
LOCAL_IP=$(hostname -I | awk '{print $1}')

# 使用 nvidia-smi 获取当前GPU数量
GPU_COUNT=$(nvidia-smi --query-gpu=gpu_uuid --format=csv,noheader | wc -l)

# 飞书机器人的Webhook URL
FEISHU_WEBHOOK_URL="https://open.feishu.cn/open-apis/bot/v2/hook/ecfed511-daa3-43d2-9673-f8807b274e1a"

# 创建发送给飞书的消息内容，包括时间、IP地址和GPU数量
ALERT_MESSAGE="[$TIMESTAMP] GPU Alert on $LOCAL_IP: Detected only $GPU_COUNT GPUs, which is less than the expected $EXPECTED_GPUS."

# 检查GPU数量是否小于预期
if [[ "$GPU_COUNT" -lt "$EXPECTED_GPUS" ]]; then
    # 如果GPU数量小于预期，使用curl发送告警到飞书群组
    curl -X POST -H "Content-Type: application/json" \
        -d "{\"msg_type\":\"text\",\"content\":{\"text\":\"GPU掉卡告警：机器IP $LOCAL_IP ，检测到的GPU数量少于预期。$ALERT_MESSAGE\"}}" \
        $FEISHU_WEBHOOK_URL
fi

```









```shell
#!/bin/bash

# 预期的GPU数量
EXPECTED_GPUS=8

# 服务器列表文件路径
SERVERS_LIST="servers.txt"

# 统一的 SSH 私钥文件路径
SSH_PRIVATE_KEY="infra.key"

# 飞书机器人的Webhook URL
FEISHU_WEBHOOK_URL="https://open.feishu.cn/open-apis/bot/v2/hook/ecfed511-daa3-43d2-9673-f8807b274e1a"

# 默认SSH用户名称
#SSH_USER="your_ssh_username" # 替换为实际在远程服务器上使用的用户名

# 逐行读取服务器列表
while  read -r server_ip; do
    # 通过SSH连接到远程服务器并执行nvidia-smi命令
    GPU_COUNT=$(ssh -o StrictHostKeyChecking=no -i "$SSH_PRIVATE_KEY" "${server_ip}" nvidia-smi --query-gpu=gpu_uuid --format=csv,noheader | wc -l)
    
    # 如果GPU数量小于预期，则发送告警信息到飞书群组
    if [[ "$GPU_COUNT" -lt "$EXPECTED_GPUS" ]]; then
        ALERT_MESSAGE="GPU alert on $server_ip: Detected only $GPU_COUNT GPUs, which is less than the expected $EXPECTED_GPUS."
        curl -X POST -H "Content-Type: application/json" \
             -d "{\"msg_type\":\"text\",\"content\":{\"text\":\"$ALERT_MESSAGE\"}}" \
             $FEISHU_WEBHOOK_URL
    fi
done < "$SERVERS_LIST"






while read -r server_ip || [[ -n "$server_ip" ]]; do
    
    # 打印当前正在检查的服务器IP
    echo "Checking server: $server_ip"

    # 尝试通过SSH连接到远程服务器并执行nvidia-smi命令
    GPU_COUNT=$(ssh -o BatchMode=yes -o ConnectTimeout=5 -i "$SSH_PRIVATE_KEY" "${server_ip}" nvidia-smi --query-gpu=gpu_uuid --format=csv,noheader | wc -l)

    # 如果GPU数量小于预期，则发送告警信息到飞书群组
    if [[ "$GPU_COUNT" -lt "$EXPECTED_GPUS" ]]; then
        ALERT_MESSAGE="GPU alert on $server_ip: Detected only $GPU_COUNT GPUs, which is less than the expected $EXPECTED_GPUS."
        curl -X POST -H "Content-Type: application/json" \
             -d "{\"msg_type\":\"text\",\"content\":{\"text\":\"$ALERT_MESSAGE\"}}" \
             $FEISHU_WEBHOOK_URL
    else
        echo "Server $server_ip is OK with $GPU_COUNT GPUs detected."
    fi

done < "$SERVERS_LIST"

```



# 最终版本

```shell
#!/bin/bash

# 预期的GPU数量
EXPECTED_GPUS=8

# 服务器列表文件路径
SERVERS_LIST="servers.txt"

# 统一的 SSH 私钥文件路径
SSH_PRIVATE_KEY="infra.key"

# 飞书机器人的Webhook URL
FEISHU_WEBHOOK_URL="https://open.feishu.cn/open-apis/bot/v2/hook/ecfed511-daa3-43d2-9673-f8807b274e1a"



# 逐行读取服务器列表
while IFS=' ' read -r server_ip || [[ -n "$server_ip" ]]; do
    # 通过SSH连接到远程服务器并执行nvidia-smi命令
    GPU_COUNT=$(ssh -o StrictHostKeyChecking=no -o BatchMode=yes -o ConnectTimeout=5 -i "$SSH_PRIVATE_KEY" "${server_ip}" nvidia-smi --query-gpu=gpu_uuid --format=csv,noheader < /dev/null | wc -l)
    
    # 如果GPU数量小于预期，则发送告警信息到飞书群组
    if [[ "$GPU_COUNT" -lt "$EXPECTED_GPUS" ]]; then
        ALERT_MESSAGE="GPU alert on $server_ip: Detected only $GPU_COUNT GPUs, which is less than the expected $EXPECTED_GPUS."
        curl -X POST -H "Content-Type: application/json" \
             -d "{\"msg_type\":\"text\",\"content\":{\"text\":\"$ALERT_MESSAGE\"}}" \
             $FEISHU_WEBHOOK_URL
    fi
done < "$SERVERS_LIST"

```

