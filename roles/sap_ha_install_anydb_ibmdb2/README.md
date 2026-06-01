<!-- BEGIN Title -->
# sap_ha_install_anydb_ibmdb2 Ansible Role
<!-- END Title -->
![Ansible Lint for sap_ha_install_anydb_ibmdb2](https://github.com/sap-linuxlab/community.sap_install/actions/workflows/ansible-lint-sap_ha_install_anydb_ibmdb2.yml/badge.svg)

## Description
<!-- BEGIN Description -->
The Ansible Role `sap_ha_install_anydb_ibmdb2` is used to install and configure IBM Db2 High Availability using the 'Integrated Linux Pacemaker' feature with HADR (High Availability and Disaster Recovery) for SAP systems on various infrastructure platforms.

**NOTE:** IBM Db2 with 'Integrated Linux Pacemaker' can use two deployment models:
- High Availability and Disaster Recovery (HADR) option for Idle Standby - **Supported by this Ansible Role**
- Mutual Failover option - **Not covered by this Ansible Role**
<!-- END Description -->

<!-- BEGIN Dependencies -->
## Dependencies
None. This role uses IBM Db2's integrated cluster manager functionality.
<!-- END Dependencies -->

<!-- BEGIN Prerequisites -->
## Prerequisites
Infrastructure:
- Cloud platform resources (VIPs, load balancers, etc.) must be created manually or using infrastructure provisioning tools, as this role does not create Cloud platform resources.

Managed nodes:
- IBM Db2 11.5 (certified for SAP) must be installed on both primary and secondary nodes
- IBM Db2 11.5.9 or later is required for full `db2cm` binary compatibility with AWS, GCP, and MS Azure
- Directory with IBM Db2 installation media must be present and accessible
- Operating system has access to all required packages for Pacemaker and cluster management
- All required ports are open (details below)

| IBM Db2 HADR process | Port |
| --- | --- |
| HADR local service<br/><sub>Used for log and data shipping</sub> | 55001/tcp |
| HADR remote service<br/><sub>Used for metadata communication</sub> | 55002/tcp |

| Linux Pacemaker process | Port |
| --- | --- |
| pcsd<br/><sub>Cluster nodes requirement for node-to-node communication</sub> | 2224 (TCP) |
| pacemaker<br/><sub>Cluster nodes requirement for Pacemaker Remote service daemon</sub> | 3121 (TCP) |
| corosync<br/><sub>Cluster nodes requirement for node-to-node communication</sub> | 5404-5412 (UDP) |

Software compatibility:
- This Ansible Role is applicable to IBM Db2 11.5 certified for SAP
- Requires IBM Db2 11.5.9 or later for full cloud platform support
<!-- END Prerequisites -->

## Execution
<!-- BEGIN Execution -->
**:warning: This ansible role will configure IBM Db2 HADR and create a Linux Pacemaker cluster.**</br>
:warning: Ensure IBM Db2 is properly installed and configured before running this role.

### Supported Platforms
| Platform | Status | Notes |
| -------- | --------- | --------- |
| AWS EC2 Virtual Servers | :heavy_check_mark: | Requires AWS credentials and route table IDs |
| Google Cloud Compute Engine Virtual Machine | :heavy_check_mark: | Requires GCP credentials and project ID |
| Microsoft Azure Virtual Machines | :heavy_check_mark: | Requires Azure credentials and load balancer configuration |
| IBM Cloud Virtual Server | :heavy_check_mark: | Requires IBM Cloud API key and region |

### Supported Scenarios
| Scenario | Status |
| -------- | --------- |
| IBM Db2 HADR with Integrated Linux Pacemaker (2 nodes) | :heavy_check_mark: |

<!-- END Execution -->

<!-- BEGIN Execution Recommended -->
### Recommended
It is recommended to execute this role together with other roles in this collection, in the following order:

#### IBM Db2 for SAP cluster
1. [sap_general_preconfigure](https://github.com/sap-linuxlab/community.sap_install/tree/main/roles/sap_general_preconfigure)
2. [sap_install_media_detect](https://github.com/sap-linuxlab/community.sap_install/tree/main/roles/sap_install_media_detect)
3. Install IBM Db2 (using appropriate installation method)
4. *`sap_ha_install_anydb_ibmdb2`*
<!-- END Execution Recommended -->

### Execution Flow
<!-- BEGIN Execution Flow -->
1. Collect required facts and detect target infrastructure platform
2. Validate that IBM Db2 installation media is available
3. Check if IBM Db2 HADR is already activated
4. Configure IBM Db2 HADR on primary and secondary nodes
5. Set up passwordless SSH between nodes
6. Create and configure IBM Db2 'Integrated Linux Pacemaker' cluster
7. Verify cluster configuration and HADR status
<!-- END Execution Flow -->

### Example
<!-- BEGIN Execution Example -->
```yaml
---
- name: Ansible Play for IBM Db2 HADR cluster setup
  hosts: db2_primary, db2_secondary
  become: true
  tasks:
    - name: Execute Ansible Role sap_ha_install_anydb_ibmdb2
      ansible.builtin.include_role:
        name: community.sap_install.sap_ha_install_anydb_ibmdb2
      vars:
        sap_ha_install_anydb_ibmdb2_sid: 'DB1'
        sap_ha_install_anydb_ibmdb2_hostname_primary: 'db2node1'
        sap_ha_install_anydb_ibmdb2_hostname_secondary: 'db2node2'
        sap_ha_install_anydb_ibmdb2_software_directory: '/software/ibmdb2'
        sap_ha_install_anydb_ibmdb2_vip_primary_ip_address: '192.168.1.100'

        # AWS-specific parameters (if deploying on AWS)
        sap_ha_install_anydb_ibmdb2_aws_access_key_id: 'AKIAIOSFODNN7EXAMPLE'
        sap_ha_install_anydb_ibmdb2_aws_secret_access_key: 'wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY'
        sap_ha_install_anydb_ibmdb2_aws_vip_update_rt:
          - 'rtb-12345678'
```
<!-- END Execution Example -->

<!-- BEGIN Role Tags -->
### Role Tags
- `always` - Tasks that always run (fact gathering, variable detection)
- `pre_db2_hadr` - Pre-tasks for IBM Db2 HADR configuration
- `post_db2_hadr` - Post-tasks for verification
<!-- END Role Tags -->

<!-- BEGIN Further Information -->
## Further Information
For more information about IBM Db2 HADR with Pacemaker:
- [IBM Db2 HADR Wiki for SAP](https://ibm.github.io/db2-hadr-wiki/)
- [IBM Db2 Integrated Solution using Pacemaker](https://www.ibm.com/docs/en/db2/11.5?topic=feature-integrated-solution-using-pacemaker)
- [SAP Note 1555903 - DB6: Supported IBM Db2 Database Features](https://launchpad.support.sap.com/#/notes/1555903)
- [SAP Note 3100330 - DB6: Using Db2 HADR with Pacemaker Cluster Software](https://launchpad.support.sap.com/#/notes/3100330)
- [SAP Note 3100287 - DB6: Db2 Support for Pacemaker Cluster Software](https://launchpad.support.sap.com/#/notes/3100287)
<!-- END Further Information -->

## License
<!-- BEGIN License -->
Apache 2.0
<!-- END License -->

## Maintainers
<!-- BEGIN Maintainers -->
- [Sean Freeman](https://github.com/sean-freeman)
<!-- END Maintainers -->

## Role Variables
<!-- BEGIN Role Variables -->
### Required Parameters

#### sap_ha_install_anydb_ibmdb2_sid
- _Type:_ `string`
- _Required:_ `true`

SAP System ID (SID) for the IBM Db2 database instance. Must be exactly 3 characters, uppercase alphanumeric.

#### sap_ha_install_anydb_ibmdb2_hostname_primary
- _Type:_ `string`
- _Required:_ `true`

Hostname of the primary IBM Db2 node. This node will be configured as the HADR primary.

#### sap_ha_install_anydb_ibmdb2_hostname_secondary
- _Type:_ `string`
- _Required:_ `true`

Hostname of the secondary IBM Db2 node. This node will be configured as the HADR standby.

#### sap_ha_install_anydb_ibmdb2_software_directory
- _Type:_ `string`
- _Required:_ `true`

Directory path where IBM Db2 installation media is located. Must contain the `db2installPCMK` script.

#### sap_ha_install_anydb_ibmdb2_vip_primary_ip_address
- _Type:_ `string`
- _Required:_ `true`

Virtual IP address for the IBM Db2 primary instance. This VIP will be managed by the cluster.

### Platform-Specific Parameters

#### AWS EC2 Parameters

##### sap_ha_install_anydb_ibmdb2_aws_access_key_id
- _Type:_ `string`

AWS Access Key ID for API authentication. Required when deploying on AWS EC2.

##### sap_ha_install_anydb_ibmdb2_aws_secret_access_key
- _Type:_ `string`

AWS Secret Access Key for API authentication. Required when deploying on AWS EC2.

##### sap_ha_install_anydb_ibmdb2_aws_vip_update_rt
- _Type:_ `list`

List of AWS route table IDs to update for VIP routing. Required when deploying on AWS EC2.

#### Microsoft Azure Parameters

##### sap_ha_install_anydb_ibmdb2_msazure_tenant_id
- _Type:_ `string`

Azure Tenant ID for authentication. Required when deploying on Microsoft Azure.

##### sap_ha_install_anydb_ibmdb2_msazure_app_client_id
- _Type:_ `string`

Azure Application (Client) ID for authentication. Required when deploying on Microsoft Azure.

##### sap_ha_install_anydb_ibmdb2_msazure_app_client_secret
- _Type:_ `string`

Azure Application Client Secret for authentication. Required when deploying on Microsoft Azure.

##### sap_ha_install_anydb_ibmdb2_msazure_lb_health_check_port
- _Type:_ `int`
- _Default:_ `62700`

Port number for Azure Load Balancer health check.

#### Google Cloud Platform Parameters

##### sap_ha_install_anydb_ibmdb2_gcp_credentials_json_file
- _Type:_ `string`

Path to the Google Cloud service account JSON credentials file. Required when deploying on Google Cloud Platform.

##### sap_ha_install_anydb_ibmdb2_gcp_project
- _Type:_ `string`

Google Cloud Project ID. Required when deploying on Google Cloud Platform.

##### sap_ha_install_anydb_ibmdb2_gcp_lb_health_check_port
- _Type:_ `int`
- _Default:_ `62700`

Port number for Google Cloud Load Balancer health check.

#### IBM Cloud Parameters

##### sap_ha_install_anydb_ibmdb2_ibmcloud_api_key
- _Type:_ `string`

IBM Cloud API Key for authentication. Required when deploying on IBM Cloud.

##### sap_ha_install_anydb_ibmdb2_ibmcloud_region
- _Type:_ `string`

IBM Cloud region where resources are deployed. Required when deploying on IBM Cloud.

##### sap_ha_install_anydb_ibmdb2_ibmcloud_health_check_port
- _Type:_ `int`
- _Default:_ `62700`

Port number for IBM Cloud Load Balancer health check.
<!-- END Role Variables -->
