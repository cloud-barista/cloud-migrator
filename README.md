# Cloud Migration Integration Platform

Integrated mirror for all subsystems in cloud migration integration platform (Cloud-Migrator)

## Introduction

This project develops a cloud migration integration platform technology that facilitates
the transfer of your IT assets to the cloud, thereby accelerating your successful SaaS transformation.
Our technology scans all your IT assets, including infrastructure, software, and data, and then
smoothly migrates them together in an integrated manner.

This repository serves as an integrated archive for major cloud migration features.
The included repositories are listed in the root directory.

The main subsystems and tools we include are:

- **CB-Spider**: Connects all clouds in a single interface, facilitating dynamic multi-cloud integration. It offers unified APIs for the dynamic connection and control of multi-cloud resources and services.
  - [Upstream repository](https://github.com/cloud-barista/cb-spider)

- **CB-Tumblebug**: Manages multi-cloud infrastructures, integrating various heterogeneous cloud infrastructure resources in the form of a single private cloud.
  - [Upstream repository](https://github.com/cloud-barista/cb-tumblebug)

- **CM-Honeybee**: Collects and aggregates information from source computing environment, including about intrastructure, software, and data.
  - [Upstream repository](https://github.com/cloud-barista/cm-honeybee)

- **CM-Beetle**: Executes computing infra migration to the target cloud infrastructure and recommends an optimal configuration of the target.
  - [Upstream repository](https://github.com/cloud-barista/cm-beetle)

- **CM-Grasshopper**: Executes software migration onto the target cloud infrastructure and manages software packages.
  - [Upstream repository](https://github.com/cloud-barista/cm-grasshopper)

- **CM-Centipede**: Executes data migration onto the target cloud infrasturcuture and manages performance and stability on data migration.
  - [Upstream repository](https://github.com/cloud-barista/cm-centipede)

- **CM-Damselfly**: Specifies and manages model specifications, versions, and user models for cloud migration.
  - [Upstream repository](https://github.com/cloud-barista/cm-damselfly)

- **CM-Cicada**: Manages migration workflows to perform automated or optimized migrations. 
  - [Upstream repository](https://github.com/cloud-barista/cm-cicada)

- **CM-Ant**: Evaluates and predicts cost/performance of target cloud infrastructure.
  - [Upstream repository](https://github.com/cloud-barista/cm-ant)

- **CM-Mayfly**: Provides an operation tool for Cloud-Migrator platform runtime.
  - [Upstream repository](https://github.com/cloud-barista/cm-mayfly)

- **CM-Butterfly**: Provides a web-based GUI portal and a REST-based open interfaces.
  - [Upstream repository](https://github.com/cloud-barista/cm-butterfly)


Note - Source code of CM-Centipede would not be released and archived in this repository for the time being.


## Getting Started

To start with the Cloud-Migrator, follow these detailed steps:

1. **Access Repositories**: Navigate to the README.md file of each tool repository by clicking on the provided upstream repository links.
2. **Installation Instructions**: Follow the detailed installation instructions provided in each repository. These instructions cover the prerequisites, dependencies, and step-by-step installation process.
3. **Utilization**:
   - Use **CB-Spider** for integrating different cloud platforms into a single interface.
   - Use **CB-Tumblebug** for deploying and managing cloud infrastructures.
   - Use **CM-Honeybee** for scaning and analyzing IT assets.
   - Use **CM-Beetle** for migrating infrastructure.
   - Use **CM-Grasshopper** for migrating softwares.
   - Use **CM-Centipede** for migrating data.
   - Use **CM-Damselfly** for managing migration models.
   - Use **CM-Cicada** for managing migration workflows.
   - Use **CM-Ant** for evaluating target cloud infrastructure.
   - Use **CM-Mayfly** for up and running and operating the entire Cloud-Migrator platform.
   - Use **CM-Butterfly** for providing Cloud-Migrator portal and open interface.


## Contribution

Contributions are welcome. To contribute:
1. **Explore Issues**: Visit the GitHub page of the respective tool repository and explore open issues or feature requests.
2. **Discuss**: Engage in discussions on existing issues or open new ones to share your ideas and contributions with the developer community.
3. **Submit Changes**: Create and submit Pull Requests with your changes. Once reviewed and approved, they will be merged into the project.


## Contact

If you have any questions or feedback about the Cloud-Migrator, please visit the GitHub Issues Pages in each upstream repository.

## Update all subsystems 

> This script is used for updating Git submodules to a specific tag and optionally committing those changes.
> It reads a list of submodule URLs from an external file, checks each submodule, updates it to a specified tag,
> and then commits the change if the user agrees. - [README in m-cmp/update-submodules.sh](https://github.com/m-cmp/m-cmp/blob/main/update-submodules.sh)

Thanks to [the M-CMP Community](https://github.com/m-cmp) for providing this script 

```bash
# Download the script quietly and save as update-submodules.sh
wget -q -O update-submodules.sh https://raw.githubusercontent.com/m-cmp/m-cmp/main/update-submodules.sh 

# Make the script executable
chmod 775 update-submodules.sh

# Execute the script
./update-submodules.sh
```

