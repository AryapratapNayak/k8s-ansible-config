# Kubernetes GitOps Platform — Ansible Configuration

Ansible configuration repository for the Kubernetes GitOps capstone.

## Repositories

- `k8s-gitops-platform`: application code and Helm charts.
- `k8s-gitops-config`: declarative GitOps application configuration.
- `k8s-ansible-config`: worker-node and monitoring-host configuration.

## Ansible roles

- `node-setup`: containerd, kernel modules, and Kubernetes networking settings.
- `node-exporter`: installs and manages Prometheus Node Exporter.
- `prometheus-config`: scrape configuration and alert rules.
- `alertmanager-config`: alert routing and optional Slack notifications.

## Inventory

Edit `inventory/hosts.yml` to add reachable Linux hosts. Use `ansible_host`
and `ansible_user` variables as appropriate. Keep credentials and private
webhook URLs out of Git.

The inventory groups are intentionally empty until real hosts are supplied.

## Validate

```bash
ansible-inventory --graph
ansible-playbook -i localhost, /tmp/check-alertmanager-config.yml --syntax-check
ansible-playbook --syntax-check playbooks/site.yml
```

Validate individual roles with temporary localhost playbooks before applying
them to real infrastructure.

## Apply to real hosts

After configuring inventory, SSH access, sudo privileges, package repositories,
and network access, review the planned changes before applying:

```bash
ansible-playbook playbooks/site.yml --list-hosts
ansible-playbook playbooks/site.yml --syntax-check
ansible-playbook playbooks/site.yml --check --diff
ansible-playbook playbooks/site.yml
```

The check mode is a preview and cannot predict every effect of commands,
service restarts, or package installation.

## Monitoring

Prometheus configuration expects reachable scrape targets. Kubernetes-specific
metrics require their exporters and scrape targets to be configured. HTTP error
alerts require applications to expose the expected `http_requests_total`
metric and labels.

Set `SLACK_WEBHOOK_URL` securely in the execution environment before enabling
Slack notifications. Never commit a real webhook URL.

## Limitations

This repository does not itself provision AWS EKS or create worker nodes.
Terraform, EKS networking/IAM, actual host inventory, ArgoCD application
configuration, and live monitoring deployment must be validated separately.
