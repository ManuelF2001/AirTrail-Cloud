# AirTrail on Google Kubernetes Engine, automated with Ansible

Group project for the **Cloud Computing Applications and Services** course (MSc in Software Engineering, University
of Minho, 2025/26).

The goal: install, configure, scale, monitor and load-test [AirTrail](https://github.com/johanohly/AirTrail), a web
app for tracking flights (Node/Bun + PostgreSQL), on Google Cloud, **fully automated** with Ansible.

## What it does

- **Cluster lifecycle:** `gke-cluster-create.yml` / `gke-cluster-destroy.yml` create and delete a GKE cluster from a service account.
- **Deployment on Kubernetes:** PostgreSQL with a persistent volume, the AirTrail app with CPU and memory requests and
  limits and a health-check readiness probe, exposed through a Service (`templates/`).
- **Autoscaling:** a Horizontal Pod Autoscaler scales the app from 1 to 5 replicas at 75% average CPU.
- **Monitoring:** GKE system, workload, storage and pod monitoring and logging switched on by `monitoring-setup.yml`.
- **Load testing:** `run-benchmark.yml` installs JMeter, finds the app's IP and runs three scenarios (login, flight
  search, mixed load) at 10, 50 or 100 concurrent users, producing HTML reports.
- **Automated tests:** `test-all.yml` deploys the app, checks it responds, creates an account and an API key, and
  creates and reads flights through the API.

In the light scenario (10 users), the mixed-load test served 2,211 requests with **0% errors**, a mean response time
of 166 ms and about 37 requests per second (`checkpoints/resultados_light.tar.gz`).

## Run

```bash
ansible-playbook -i inventory/gcp.yml gke-cluster-create.yml
ansible-playbook -i inventory/gcp.yml airtrail-deploy.yml
ansible-playbook -i inventory/gcp.yml monitoring-setup.yml
ansible-playbook -i inventory/hosts run-benchmark.yml -e "benchmark_scenario=medium"
ansible-playbook -i inventory/gcp.yml gke-cluster-destroy.yml
```

Set your GCP project ID in `inventory/gcp.yml` and put the service-account key file next to it as `chave.json`
(ignored by git). More detail on benchmarking (Portuguese): [`docs/BENCHMARKING.md`](docs/BENCHMARKING.md).

## Layout

- `roles/`: one Ansible role per step (cluster, database, app, monitoring, JMeter, tests)
- `templates/`: Kubernetes manifests (Deployments, Services, HPA, PVC)
- `manual-implementation/`: Vagrant VM and the manual installation steps we automated (add your SSH keys next to the Vagrantfile)
- `checkpoints/`: script and VM used to validate the playbooks end to end

## Team (Group 11)

Ana Margarida Pires, Diana Dinis, Francisco Mota, João Silva, Manuel Fernandes.
