## Deploy Monitoring

```bash
aws cloudformation deploy --template-file=.circleci/files/monitoring.yml --tags project=udapeople --stack-name "udapeople-monitoring"
```

## Updating the inventory from the root of the repo

```bash
echo "[monitor]" > .circleci/ansible/inventory.txt && \
aws ec2 describe-instances \
    --query 'Reservations[*].Instances[*].PublicIpAddress' \
    --filters "Name=tag:Name,Values=udapeople" \
    --filters "Name=tag:app,Values=prometheus" \
    --output text >> .circleci/ansible/inventory.txt
```

## Configure with ansible

```bash
ansible-playbook --private-key udacity.pem -i .circleci/ansible/inventory.txt .circleci/ansible/configure-prometheus.yml
```

## Configure node exporter

```bash
ansible-playbook --private-key udacity.pem -i .circleci/ansible/inventory.txt .circleci/ansible/configure-prometheus-node-exporter.yml
```
