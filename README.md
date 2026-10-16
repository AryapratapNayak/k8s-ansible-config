# Kubernetes GitOps Platform — Ansible Configuration

Ansible configuration repository for the Kubernetes GitOps capstone.

## Repositories

- `k8s-gitops-platform`: application code and Helm charts.
- `k8s-gitops-config`: declarative GitOps application configuration.
- `k8s-ansible-config`: worker-node and monitoring-host configuration.

## Ansible Roles

- `node-setup`: containerd, kernel modules, and Kubernetes networking settings.
- `node-exporter`: installs and manages Prometheus Node Exporter.
- `prometheus-config`: Prometheus scrape configuration and alert rules.
- `alertmanager-config`: Alertmanager configuration and optional Slack notifications.

## Inventory

Edit `inventory/hosts.yml` to add reachable Linux hosts. Configure `ansible_host` and `ansible_user` as appropriate. Keep credentials and private webhook URLs out of Git.

The inventory groups are intentionally empty until real hosts are supplied.

## Validate

Run these commands from the repository root:

```bash
ansible-inventory --graph
ansible-playbook playbooks/site.yml --syntax-check
ansible-playbook playbooks/site.yml --list-hosts
```

Syntax checks validate Ansible structure but do not verify live service behavior.

## Apply to Real Hosts

After configuring the inventory, SSH access, sudo privileges, package repositories, and network access, review the planned changes:

```bash
ansible-playbook playbooks/site.yml --list-hosts
ansible-playbook playbooks/site.yml --syntax-check
ansible-playbook playbooks/site.yml --check --diff
```

Apply only after reviewing the inventory and proposed changes:

```bash
ansible-playbook playbooks/site.yml
```

Check mode is a preview and cannot predict every effect of commands, service restarts, or package installation.

## Monitoring

Prometheus configuration expects reachable scrape targets. Kubernetes-specific metrics require the appropriate exporters and scrape targets. HTTP error alerts require applications to expose the expected `http_requests_total` metric and labels.

Set `SLACK_WEBHOOK_URL` securely in the execution environment before enabling Slack notifications. Never commit a real webhook URL.

## Limitations

This repository does not provision AWS EKS or create worker nodes. Terraform, EKS networking and IAM, actual host inventory, ArgoCD application configuration, and live monitoring deployment must be validated separately.
