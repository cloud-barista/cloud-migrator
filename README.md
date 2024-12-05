# Cloud Migration Integration Platform

Integrated mirror for all subsystems in cloud migration integration platform (Cloud-Migrator)

## Introduction

This project develops a cloud migration technology that
facilitates the transfer of your IT assets to the cloud, thereby accelerating your successful SaaS transformation.

Our technology scans all your IT assets, including infrastructure, software, and data, and then
migrates all of these at once.

This repository serves as an integrated archive for major cloud migration features.
The included repositories are listed in the root directory.

The main subsystems and tools we include are:

- **CM-Mayfly**: Provides an operation tool for Cloud-Migrator platform runtime.

  - [Upstream repository](https://github.com/cloud-barista/cm-mayfly)

- **CM-Butterfly**: Provides a web-based GUI portal and a REST-based open interfaces.

  - [Upstream repository](https://github.com/cloud-barista/cm-butterfly)

- **CM-Ant**: Evaluates and predicts cost/performance of target cloud infrastructure.

  - [Upstream repository](https://github.com/cloud-barista/cm-ant)

- **CM-Cicada**: Manages migration workflows to perform automated or optimized migrations.

  - [Upstream repository](https://github.com/cloud-barista/cm-cicada)

- **CM-Damselfly**: Specifies and manages model specifications, versions, and user models for cloud migration.

  - [Upstream repository](https://github.com/cloud-barista/cm-damselfly)

- **CM-Centipede**: Executes data migration onto the target cloud infrasturcuture and manages performance and stability on data migration.

  - [Upstream repository](https://github.com/cloud-barista/cm-centipede)

- **CM-Grasshopper**: Executes software migration onto the target cloud infrastructure and manages software packages.

  - [Upstream repository](https://github.com/cloud-barista/cm-grasshopper)

- **CM-Beetle**: Executes computing infra migration to the target cloud infrastructure and recommends an optimal configuration of the target.

  - [Upstream repository](https://github.com/cloud-barista/cm-beetle)

- **CM-Honeybee**: Collects and aggregates information from source computing environment, including about intrastructure, software, and data.

  - [Upstream repository](https://github.com/cloud-barista/cm-honeybee)

- **CB-Tumblebug**: Manages multi-cloud infrastructures, integrating various heterogeneous cloud infrastructure resources in the form of a single cloud.

  - [Upstream repository](https://github.com/cloud-barista/cb-tumblebug)

- **CB-Spider**: Connects all clouds in a single interface, facilitating dynamic multi-cloud integration. It offers unified APIs for the dynamic connection and control of multi-cloud resources and services.

  - [Upstream repository](https://github.com/cloud-barista/cb-spider)

Note - Source code of CM-Centipede would not be released and archived in this repository for the time being.

## Getting Started

To start with the Cloud-Migrator, follow these detailed steps:

1. **Select the tool you need**:
   - **CM-Mayfly** for up and running and operating the entire Cloud-Migrator platform.
   - **CM-Butterfly** for providing Cloud-Migrator portal and open interface.
   - **CM-Ant** for evaluating target cloud infrastructure.
   - **CM-Cicada** for managing migration workflows.
   - **CM-Damselfly** for managing migration models.
   - **CM-Grasshopper** for migrating softwares.
   - **CM-Beetle** for migrating infrastructure.
   - **CM-Centipede** for migrating data.
   - **CM-Honeybee** for scaning and analyzing IT assets.
   - **CB-Tumblebug** for deploying and managing cloud infrastructures.
   - **CB-Spider** for integrating different cloud platforms into a single interface.
2. **Access the repository**: Navigate to the README.md file of each tool repository, following the the provided upstream repository links.
3. **Explorer the various information**: Take a look at docs, guides, instructions, including prerequisites, dependencies, and step-by-step installation process.

## Contribution

Contributions are always welcome!

To contribute this project, we'd like you to look at the following three things:

1. **Explore issues**: Visit the GitHub Issue of each upstream repository and explore existing issues or feature requests.
2. **Join discussions**: Engage in discussions on existing issues or open new ones to share your ideas and contributions with the developer community.
3. **Propose your ideas**: Create and submit Pull Requests to propose your ideas. Once reviewed and approved, the ideas will be merged into the project.

## Contact

For any questions or feedback regarding the Cloud-Migrator, please visit the GitHub Issues of each upstream repository.

## Update all subsystems

> This script is used for updating Git submodules to a specific tag and optionally committing those changes.
> It reads a list of submodule URLs from an external file, checks each submodule, updates it to a specified tag,
> and then commits the change if the user agrees. - [README in m-cmp/update-submodules.sh](https://github.com/m-cmp/m-cmp/blob/main/update-submodules.sh)

Thanks to [the M-CMP Community](https://github.com/m-cmp) for providing this script

### Update submodules

<details>
  <summary>Click to see prerequisites</summary>

Prerequisite (before running the script):

- All repositories must have a tag.
- `submodules.md` must be updated and committed.

(Optional) Set your user.name and user.email

```bash
# Set git global user email and name
git config --global user.email "you@example.com"
git config --global user.name "Your Name"
```

(Optional) Update `submodules.md` and commit it

```bash
# Open
vim submodules.md

# Modify repository urls and save

# Commit the updates
git add .
git commit -m "Edit `submodules.md`
```

(Optional) Initialize submodules if right after cloning

```
git submodule update --init --recursive
```

</details>

Update all submodules:

```bash
# Download the script quietly and save as update-submodules.sh
wget -q -O update-submodules.sh https://raw.githubusercontent.com/m-cmp/m-cmp/main/update-submodules.sh

# Make the script executable
chmod 775 update-submodules.sh

# Execute the script
./update-submodules.sh
```
