
## 🏗️ Architecture Diagram

This project provisions Azure infrastructure using reusable Terraform modules across three isolated environments: Dev, Test, and Preprod.

```mermaid
flowchart TB
    USER(["👨‍💻 DevOps Engineer"])

    subgraph REPO["📦 GitHub Repository"]
        direction TB
        GIT["Git Repository"]
        ENV["📁 Environments<br/>Dev | Test | Preprod"]
        MOD["🧩 Reusable Terraform Modules<br/>Resource Group | VNet | Subnet<br/>Public IP | NIC | Linux VM"]
        GIT --> ENV
        ENV --> MOD
    end

    USER --> GIT

    MOD --> TF["⚙️ Terraform<br/>Init | Validate | Plan | Apply"]

    subgraph STATE["☁️ Azure Remote Terraform State"]
        direction TB
        SA["Azure Storage Account"]
        CONT["Blob Container"]
        DEVSTATE["dev.tfstate"]
        TESTSTATE["test.tfstate"]
        PRESTATE["preprod.tfstate"]
        SA --> CONT
        CONT --> DEVSTATE
        CONT --> TESTSTATE
        CONT --> PRESTATE
    end

    TF <-->|State Read / Write| SA

    subgraph AZURE["☁️ Microsoft Azure Cloud — Central India"]
        direction TB

        subgraph DEV["🟢 DEV Environment — 10.10.0.0/16"]
            direction TB
            DRG["Resource Group<br/>rg-ashish-dev-ci"]
            DVNET["VNet<br/>vnet-ashish-dev-ci"]
            DS1["Frontend Subnet<br/>10.10.1.0/24"]
            DS2["Backend Subnet<br/>10.10.2.0/24"]
            DPIP["Public IP"]
            DN1["Frontend NIC"]
            DN2["Backend NIC"]
            DVM1["Frontend Linux VM"]
            DVM2["Backend Linux VM"]

            DRG --> DVNET
            DVNET --> DS1
            DVNET --> DS2
            DS1 --> DN1
            DS2 --> DN2
            DPIP --> DN1
            DN1 --> DVM1
            DN2 --> DVM2
        end

        subgraph TEST["🟡 TEST Environment — 10.20.0.0/16"]
            direction TB
            TRG["Resource Group<br/>rg-ashish-test-ci"]
            TVNET["VNet<br/>vnet-ashish-test-ci"]
            TS1["Frontend Subnet<br/>10.20.1.0/24"]
            TS2["Backend Subnet<br/>10.20.2.0/24"]
            TPIP["Public IP"]
            TN1["Frontend NIC"]
            TN2["Backend NIC"]
            TVM1["Frontend Linux VM"]
            TVM2["Backend Linux VM"]

            TRG --> TVNET
            TVNET --> TS1
            TVNET --> TS2
            TS1 --> TN1
            TS2 --> TN2
            TPIP --> TN1
            TN1 --> TVM1
            TN2 --> TVM2
        end

        subgraph PRE["🔵 PREPROD Environment — 10.30.0.0/16"]
            direction TB
            PRG["Resource Group<br/>rg-ashish-preprod-ci"]
            PVNET["VNet<br/>vnet-ashish-preprod-ci"]
            PS1["Frontend Subnet<br/>10.30.1.0/24"]
            PS2["Backend Subnet<br/>10.30.2.0/24"]
            PPIP["Public IP"]
            PN1["Frontend NIC"]
            PN2["Backend NIC"]
            PVM1["Frontend Linux VM"]
            PVM2["Backend Linux VM"]

            PRG --> PVNET
            PVNET --> PS1
            PVNET --> PS2
            PS1 --> PN1
            PS2 --> PN2
            PPIP --> PN1
            PN1 --> PVM1
            PN2 --> PVM2
        end
    end

    TF --> DRG
    TF --> TRG
    TF --> PRG

    classDef user fill:#172554,stroke:#60A5FA,color:#FFFFFF,stroke-width:2px
    classDef repo fill:#312E81,stroke:#A78BFA,color:#FFFFFF,stroke-width:2px
    classDef terraform fill:#5B21B6,stroke:#C4B5FD,color:#FFFFFF,stroke-width:3px
    classDef state fill:#064E3B,stroke:#34D399,color:#FFFFFF,stroke-width:2px
    classDef dev fill:#064E3B,stroke:#4ADE80,color:#FFFFFF,stroke-width:2px
    classDef test fill:#713F12,stroke:#FACC15,color:#FFFFFF,stroke-width:2px
    classDef pre fill:#1E3A8A,stroke:#60A5FA,color:#FFFFFF,stroke-width:2px

    class USER user
    class GIT,ENV,MOD repo
    class TF terraform
    class SA,CONT,DEVSTATE,TESTSTATE,PRESTATE state
    class DRG,DVNET,DS1,DS2,DPIP,DN1,DN2,DVM1,DVM2 dev
    class TRG,TVNET,TS1,TS2,TPIP,TN1,TN2,TVM1,TVM2 test
    class PRG,PVNET,PS1,PS2,PPIP,PN1,PN2,PVM1,PVM2 pre
```

### 📌 Infrastructure Overview

| Component | Dev | Test | Preprod |
|---|---:|---:|---:|
| Resource Groups | 1 | 1 | 1 |
| Virtual Networks | 1 | 1 | 1 |
| Subnets | 2 | 2 | 2 |
| Public IPs | 1 | 1 | 1 |
| Network Interfaces | 2 | 2 | 2 |
| Linux Virtual Machines | 2 | 2 | 2 |

### 🚀 Key Features

- **Reusable Terraform Modules:** Common infrastructure code for all environments.
- **Environment Isolation:** Separate Dev, Test and Preprod networks and resource groups.
- **Remote State Management:** Azure Blob Storage with separate state files.
- **Infrastructure as Code:** Repeatable Azure resource provisioning.
- **Network Segmentation:** Separate Frontend and Backend subnets.
- **Public and Private VM Access:** Public IP attached only to the Frontend VM; Backend VM has no public IP.

### 🔄 Terraform Deployment Workflow

1. Engineer updates Terraform code.
2. Code is stored in GitHub.
3. Select the target environment.
4. Terraform initializes the backend.
5. Terraform validates the configuration.
6. Terraform generates an execution plan.
7. Terraform applies the changes to Azure.
8. Terraform stores environment-specific state in Azure Blob Storage.
