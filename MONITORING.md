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

This will install prometheus and the alert manager. This is intended to be ran locally to set up the prometheus instance running along side the application deployed from the CircleCI.

```bash
ansible-playbook --private-key udacity.pem -i .circleci/ansible/inventory.txt .circleci/ansible/configure-prometheus.yml
```

## Configure node exporter

Use the email and password from the local file ALERTS_MANAGER_APP_PASSWORD to supply the arguements to `--extra-vars`.

```bash
ansible-playbook --private-key udacity.pem -i .circleci/ansible/inventory.txt .circleci/ansible/configure-prometheus-node-exporter.yml --extra-vars "ALERTS_EMAIL=<EMAIL_HERE> ALERTS_PASSWORD=<PASSWORD_HERE>"
```

## Prometheus Graphs

Here is a function to graph the CPU usage of the target node.

> rate(node_cpu_seconds_total[30s]) * 100
