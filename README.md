# JENKINS SLAVE

*Powered by Jenkins Swarm Plugin and Windows Services Warpper:*

*https://plugins.jenkins.io/swarm/*

*https://github.com/winsw/winsw/tree/master*


### Features

- Installs Zulu Java with Open JDK
- Installs wildcard certificates (blabla, blabladevo) into Java cacerts store
- Installs code signing certificate (blabla) into current user personal store
- Installs Jenkins Swarm client as service and connects to Jenkins master
- Optionally uninstalls existing Oracle Java installations


### Package content

| Item  | Description |
| ------------- | ------------- |
| setup.ps1  | Main script |
| dependencies.json  | Dependencies metadata file. Used for downloads of binaries/certificates |
| node.properties  | Jenkins slave configuration file. Use for custom configurations |
| config_templates/jenkins-slave.xml | Windows Service Wrapper template |
| config_templates/node_types | Predefined node configuration templates directory |


### Usage

1. Log in to the machine that you want to connect to Jenkins as a slave (**with the user that you want jenkins service to be installed with**)
2. Clone, or download and unzip this repository
3. Update *dependencies.json* file if necessary
4. If you want to provide custom node configuration, update *node.properties* file
5. Run *setup.ps1* script in the project root directory (see commandline arguments below)


### Jenkins slave configuration

> Note: Most of the node configuration parameters are taken from *node.properties* file or predefined templates. If template is used (see -node_type below), then node.properties file is ignored.

> Note: Sensitive-value parameters (e.g. passwords, API tokens, etc.) must be specified as command line arguments and should not be included into node.properties file or templates

##### node.properties file

| Parameter | Required? | Default value | Description | Example |
| ------------- | ------------- | ------------- | ------------- | ------------- |
| master_url | yes |  | Full Jenkins master URL with trailing slash | https://jenkins01.blabladevo.com/ |
| master_user | yes |  | Jenkins user that is used to connect to master. Must have the following permissions: Agent/Configure, Agent/Connect, Agent/Create, Overall/Read | svsuser |
| node_name | no | current machine name | Node name as it appears on Jenkins master (UID will be automatially appended to it) | jenkins-slave03 |
| node_labels | no | default | Whitespace-separated list of labels to be assigned to this node on master | build_dotnet package |
| number_of_executors | no | 1 | Number of executor for this jenkins slave | 5 |
| usage_mode | no | normal | Can be either 'normal' (use this node as much as possible) or 'exclusive' (only build jobs with label expressions matching this node) | exclusive |
| service_domain | no | current user domain | Domain name for the user that the slave service will run with | blabladevo.com |
| service_user | no | current user name | Name of the user that the slave service will run with | svsuser |
| slave_root_dir | no | current directory | Jenkins slave root directory | E:\jenkins |


##### predefined templates

Predefined templates can be used for standard node types (i.e. *build_type_a*). In such cases node.properties file in the root directory is ignored and no further configuration is required. To use templates, specify commandline argument *-node_type* with template name (see *commandline arguments* below).


##### commandline arguments

| Parameter  | Mandatory? | Default value | Description |
| ------------- | ------------- | ------------- | ------------- |
| -master_user_api_token | yes |  | Api key for Jenkins user that is used to connect to master |
| -service_user_passw | yes |  | Password for the user that the slave service will run with |
| -node_type | no |  | Name of the predefined node configuration template (e.g. *build_type_a*) |
| -node_config_file_path | no | node.properties | Path to the node.properties config file |
| -java_certs_extension | no | cer | Extension for certificate files to be used with JRE cacerts keystore |
| -codesign_cert_extension | no | pfx | Extension for codesigning certificate files |
| -codesign_cert_passw | no |  | Password for codesigning certificate. **If not provided, codesigning certificate installation will be skipped** |
| -force_download | no |  | Flag; forces download of dependencies even if they found locally |
| -force_java_certs_replace | no |  | Flag; forces reinstallation of Java certificates even if they are found in JRE cacerts |
| -force_jenkins_slave_service_replace | no |  | Flag, forces removal and reinstallation of existing Jenkins salve if found |
| -uninstall_oracle_java | no |  | Flag; triggers removal of Oracle Java installation if found
| -verbose | no |  | Flag; enables verbose messages |

###### example commandline:
`$ .\setup.ps1 -master_user_api_token <token> -service_user_passw <spassw> -codesign_cert_passw <cpassw> -node_type build_type_a -force_jenkins_slave_service_replace -Verbose`
# jenkins_slave
