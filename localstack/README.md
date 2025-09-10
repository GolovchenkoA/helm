
# LocalStack Chart
- [Helm LocalStack Git repository](https://github.com/localstack/helm-charts) 
- [Helm LocalStack Documentation](https://docs.localstack.cloud/aws/integrations/containers/kubernetes/)
- [LocalStack SQS Documentation](https://docs.localstack.cloud/aws/services/sqs/)
- [LocalStack on Kubernetes Git xample](https://github.com/localstack/localstack-on-k8s)
- [LocalStack K8s Operator](https://docs.localstack.cloud/aws/enterprise/k8s-operator/)

# How to run SQS
```shell
helm search repo localstack
helm repo add localstack https://localstack.github.io/helm-charts
```

# How to check what parameters are supported for the chart
```shell
helm show values localstack/localstack
```

```shell
helm install localstack localstack/localstack --set startServices=sqs
```

# Get the application URL by running these commands:  
**!!!!!!!!! The command ttp://$NODE_IP:$NODE_PORT returns something like this http://192.168.65.3:31566/"  
But for my k8s cluster URL was http://localhost:31566**

```shell
export NODE_PORT=$(kubectl get --namespace "default" -o jsonpath="{.spec.ports[0].nodePort}" services localstack)
export NODE_IP=$(kubectl get nodes --namespace "default" -o jsonpath="{.items[0].status.addresses[0].address}")
echo http://$NODE_IP:$NODE_PORT
```

# How to create a queue

```shell
aws --endpoint-url=http://localhost:31566 sqs create-queue --queue-name my-queue
```

# How to Get The Queue Attributes
See: https://docs.localstack.cloud/aws/services/sqs/
```shell
aws --endpoint-url=http://localhost:31566 sqs get-queue-attributes \
    --queue-url http://localhost:31566/000000000000/my-queue \
    --attribute-names All
```

# How to Set The Queue Attributes
See: https://docs.localstack.cloud/aws/services/sqs/
```shell
aws --endpoint-url=http://localhost:31566 sqs set-queue-attributes \
    --queue-url http://localhost:31566/000000000000/my-queue \
    --attributes DelaySeconds=10
```

# How to add messages
```shell
for i in {1..5}; do
  aws --endpoint-url=http://localhost:31566 sqs send-message \
    --queue-url http://localhost:31566/000000000000/my-queue \
    --message-body "Test message $i"
done
```

# How to read messages

```shell
aws --endpoint-url=http://localhost:31566 sqs receive-message \
  --queue-url http://localhost:31566/000000000000/my-queue \
  --max-number-of-messages 10
```

# How to purge the queue

```shell
aws --endpoint-url=http://localhost:31566 sqs purge-queue --queue-url http://localhost:31566/000000000000/my-queue
```

# How to read and remove messages from the queue 

## On Linux

```shell
#!/bin/bash

QUEUE_URL="http://localhost:31566/000000000000/my-queue"
ENDPOINT="--endpoint-url=http://localhost:31566"

# Receive messages
RESPONSE=$(aws $ENDPOINT sqs receive-message \
  --queue-url $QUEUE_URL \
  --max-number-of-messages 10 \
  --attribute-names All \
  --message-attribute-names All \
  --output json)

# Parse and delete messages
echo "$RESPONSE" | jq -c '.Messages[]?' | while read -r message; do
  RECEIPT_HANDLE=$(echo "$message" | jq -r '.ReceiptHandle')
  BODY=$(echo "$message" | jq -r '.Body')

  echo "Message: $BODY"
  echo "Deleting message..."

  aws $ENDPOINT sqs delete-message \
    --queue-url $QUEUE_URL \
    --receipt-handle "$RECEIPT_HANDLE"
done

```

## On Windows

```shell
$QueueUrl = "http://localhost:31566/000000000000/my-queue"
$Endpoint = "--endpoint-url=http://localhost:31566"

# Receive messages
$response = aws $Endpoint sqs receive-message `
    --queue-url $QueueUrl `
    --max-number-of-messages 10 `
    --attribute-names All `
    --message-attribute-names All `
    --output json

# Convert JSON to PowerShell object
$messages = ($response | ConvertFrom-Json).Messages

if ($messages) {
    foreach ($message in $messages) {
        $body = $message.Body
        $receiptHandle = $message.ReceiptHandle

        Write-Host "Message Body:`n$body"
        Write-Host "Deleting message with ReceiptHandle: $receiptHandle"

        aws $Endpoint sqs delete-message `
            --queue-url $QueueUrl `
            --receipt-handle "$receiptHandle"
    }
} else {
    Write-Host "No messages received."
}

```
