# Ansible Advanced Topics
- Preparing Windows  Server
- Ansible-Galaxy
- Patterns
- Dynamic Inventory
- Developing Custom Modules

## Preparing Windows
- Ansible Control Machine can only be linux and windows
- Windows Machine can be targets of Ansible and thus be part of automation
- Ansible connect to a windows machine using winrm.
- Requirement:
    - pywinrm module installed on the ansible control machine - pip install "pywinrm >= 0.2.2"
    - Setup WinRM - example/scripts/ConfigureRemotingForAnsible.ps1
    - Different modes to authentication:
        - Basic/Cerficate/Kerberos/CredSSP
