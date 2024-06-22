You can use Ansible Vault to encrypt your credentials and include them in your playbook. First, create a vault file with your AWS credentials.

Create a Vault File:

sh
Copy code
ansible-vault create aws_creds.yml
Add the following content to the aws_creds.yml file:

yaml
Copy code
aws_access_key: 'your_access_key'
aws_secret_key: 'your_secret_key'
aws_region: 'your_region'
Include the Vault in Your Playbook:
Modify your playbook to use the encrypted variables.

yaml
Copy code
---
- name: Create EC2 instances
  hosts: localhost
  gather_facts: no
  collections:
    - amazon.aws

  vars_files:
    - aws_creds.yml

  vars:
    instance_type: "t2.micro"
    ami_id: "ami-0abcdef1234567890"  # Replace with a valid AMI ID for your region
    key_name: "your-key-pair"        # Replace with your actual key pair name
    security_group: "your-security-group"  # Replace with your actual security group

  tasks:
    - name: Launch EC2 instances
      amazon.aws.ec2_instance:
        name: "MyInstance"
        region: "{{ aws_region }}"
        instance_type: "{{ instance_type }}"
        image_id: "{{ ami_id }}"
        key_name: "{{ key_name }}"
        count: 2
        wait: yes
        security_group: "{{ security_group }}"
        aws_access_key: "{{ aws_access_key }}"
        aws_secret_key: "{{ aws_secret_key }}"
      register: ec2

    - name: Display instance details
      debug:
        var: ec2
Run the Playbook with Vault:
Execute the playbook and provide the vault password when prompted.

sh
Copy code
ansible-playbook create_ec2_instances.yml --ask-vault-pass
