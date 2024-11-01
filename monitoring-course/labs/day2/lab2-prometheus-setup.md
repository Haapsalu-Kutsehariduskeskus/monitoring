# Requirements

- **Cloud Account**: Ensure you have access to a cloud provider (e.g., DigitalOcean, AWS, Azure).
- **Local PC with the following software**:
  - GitHub Desktop
  - Visual Studio Code (VSCode)
  - Docker Desktop

# Manual for the Lab

 - **Lab 1**: [Prometheus Lab Manual](https://docs.google.com/document/d/1NMjHC8qi0RvVOfqVcUhHdsTeAOQCCoa6FQQJv3Tsz74/edit?usp=sharing).

- **Lab 2 (Extra)**:  [Prometheus Lab 2 Manual](https://docs.google.com/document/d/1CgED8xTrM5uRA5BwcJ5EuOO2Phfcm6Qd4dU28Vpg-As/edit?usp=sharing).
# Repository for Code

Access the code and project files here:
- **Lab1 Repository**: [Prom Stack Project Repository](https://github.com/mashaeg/grafana)
- **Lab2 Repository**: [Lab2 Project Repository](https://github.com/mashaeg/MonCourse2024/tree/lab2)

To launch the stack for Lab1:

- Download the docker-compose file https://github.com/mashaeg/grafana/blob/main/docker/docker-compose.yaml (or clone the entire repository)
- Copy the docker-compose file into a folder where the current logged-in user has write access.
run docker compose up -d
- Access http://localhost:3000 to visit Grafana.
Prometheus, Loki and Tempo are added automatically as Grafana Datasources. Mock data, including the metrics from Shoe Hub company, OpenTelemetry metrics, and traces from microservices, are available.

# Free Cloud Trials

To get started with cloud services, you can take advantage of the following free trials:
- **DigitalOcean Free Trial**: [Sign Up for DigitalOcean Free Trial](https://try.digitalocean.com/freetrialoffer/)
- **AWS Free Tier**: [Explore AWS Free Tier](https://aws.amazon.com/free/)
- **Azure Free Trial**: [Get Started with Azure Free Trial](https://azure.microsoft.com/en-us/pricing/purchase-options/azure-account?icid=portal)

# How to Create a Virtual Machine (VM)

If you need help setting up a VM, follow the step-by-step guide provided here:
- **VM Creation Guide**: [View VM Creation Instructions](https://docs.google.com/document/d/1DJs5zdEUhXc873bpFx8FEHFPeY63xplAqEBh8mMP5fI/edit?usp=sharing)
