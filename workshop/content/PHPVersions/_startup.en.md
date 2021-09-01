---
title: "Provisioning Resources & Running Benchmarks"
weight: 10
---

Firstly configure the desired instance types in php_graviton/php_bench.py on lines 20 and 21:

```python
# AMIs
ubuntu2004_x86 = ec2.MachineImage.generic_linux({'us-west-2': 'PUT_AMI_ID_HERE'})
ubuntu2004_arm = ec2.MachineImage.generic_linux({'us-west-2': 'PUT_AMI_ID_HERE'})
```

Then deploy the resources using CDK:

```bash
cdk deploy GravitonID-php-bench
```

{{% notice warning %}}
Review the IAM updates and resources to be provisioned (cdk deploy will provide a summary and ask if you're sure), then allow the deployment to carry on, or cancel.
{{% /notice %}}

This deployment will output yaml to the console for use with Ansible.
Copy this yaml and paste it into the ABench/benchmarks/wordpress_nginx/hosts.yaml file.
This is what it should look like (with different hostnames):

```yaml
---
all:
  children:
    db:
      hosts: ec2-54-202-157-178.us-west-2.compute.amazonaws.com
    drv:
      hosts: ec2-34-222-98-131.us-west-2.compute.amazonaws.com
    sut:
      hosts: ec2-54-245-213-130.us-west-2.compute.amazonaws.com
```

Now install Ansible, this package will be used to install all necessary software for the benchmarks to commence:

```bash
pip3 install ansible
```

Now run the following two commands and wait for the benchmark to complete:

```bash
ansible-playbook -i hosts.yaml -u ubuntu --private-key ~/.ssh/id_rsa install-configure.yaml
ansible-playbook -i hosts.yaml -u ubuntu --private-key ~/.ssh/id_rsa benchmark.yaml
```