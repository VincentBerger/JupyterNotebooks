# Ansible playbook to create the directory structure of a workshop with Jupyter Notebooks

Usage: sudo ansible-playbook ansible_copy_folder.yml -e "variable1=value variable2=value"

Works on localhost only

## Playbook variables
    prefix: default is /home
    ref: default is student0
    dir: default is TSS
    workshop: default is OVWorkshop
    capacity: default is 40
    myrange: default is 1 to {{capacity}}

- The playbook looks for the reference notebooks to be copied in {{prefix}}/{{ref}}/{{dir}}/{{workshop}}
- It removes recursively everything under {{prefix}}/student**n**/{{dir}}/{{workshop}} with **n** in {{myrange}}
- Then it copies recursively the reference notebooks (see above) to each student**n** directory
- It looks for all Jupyter notebooks (*.ipynb) under the reference directory, excluding checkpoints
- For each notebook in each student**n** directory, clean up the cells output if present
- For each notebook in each student**n** directory, search for $$I in the notebook and replace that string with n
- For each notebook in each student**n** directory, and for each variable_name found in varname in variables_{{workshop}}.yml, search for $$variable_name in the notebook and replace that string with varsubst for that student number as found in variables_{{workshop}}.yml
- Change the ownership of the copied data back to student**n**. This is why we run the playbook under sudo

Be careful about possible variable name ambiguity. For example if you define a variable varname=ID, $$ID will first match $$I and will be replaced by n in the previous task
