# Ansible playbook to create the directory structure of a workshop with Jupyter Notebooks

Usage: sudo ansible-playbook ansible_copy_folder.yml -e "variable1=value variable2=value"

Works on localhost only

## Playbook variables
    prefix: default is /home
    ref: default is student0
    dir: default is TSS
    workshop: default is OVWorkshop
    capacity: default is 40
    myrange: default is 1 to {{capacity}}, accepts both 3-17 syntax and 3 for a single student

- The playbook looks for the reference notebooks to be copied in {{prefix}}/{{ref}}/{{dir}}/{{workshop}}
- It removes recursively everything under {{prefix}}/student**n**/{{dir}}/{{workshop}} with **n** in {{myrange}}
- Then it copies recursively the reference notebooks (see above) to each student**n** directory
- It looks for all Jupyter notebooks (*.ipynb) under the reference directory, excluding checkpoints
- For each notebook in each student**n** directory, clean up the cells output if present
- For each notebook in each student**n** directory, search for $$I in the notebook and replace that string with n
- For each notebook in each student**n** directory, and for each variable_name found in varname in variables_{{workshop}}.yml, search for $$variable_name in the notebook and replace that string with varsubst for that student number as found in variables_{{workshop}}.yml
- Change the ownership of the copied data back to student**n**. This is why we run the playbook under sudo

Be careful about possible variable name ambiguity. For example if you define a variable varname=ID, $$ID will first match $$I and will be replaced by n in the previous task

## Examples

    sudo ansible-playbook ansible_copy_folder.yml -e "dir=Discover2020 workshop=WKSHP-Oneview"
copies /home/student0/Discover2020/WKSHP-OneView to /home/student**n**/Discover2020/WKSHP-OneView with n from 1 to 40

    sudo ansible-playbook ansible_copy_folder.yml -e "dir=Discover2020 workshop=WKSHP-Oneview capacity=80"
copies /home/student0/Discover2020/WKSHP-OneView to /home/student**n**/Discover2020/WKSHP-OneView with n from 1 to 80

    sudo ansible-playbook ansible_copy_folder.yml -e "dir=Discover2020 workshop=WKSHP-Oneview myrange=3-7"
copies /home/student0/Discover2020/WKSHP-OneView to /home/student**n**/Discover2020/WKSHP-OneView with n from 3 to 7

    sudo ansible-playbook ansible_copy_folder.yml -e "dir=Discover2020 workshop=WKSHP-Oneview myrange=5"
copies /home/student0/Discover2020/WKSHP-OneView to /home/student**5**/Discover2020/WKSHP-OneView only
