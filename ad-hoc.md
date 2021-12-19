cat inventory.txt
target1 ansible_host=192.168.1.44 ansible_ssh_pass=Sonelgaz.1

# Copy file from src to dest

ansible all -i inventory.txt -m copy -a "src=/root/test-project/file.txt dest=/root/newfile.txt"

# Create file with content

ansible all -i inventory.txt -m copy -a "content='Test content' dest=/root/testcontent.txt"

# Modify file with backup

ansible all -i inventory.txt -m copy -a "content='Test content with backup' dest=/root/testcontent.txt backup=yes"

# ps: add \n after content value

ansible all -i inventory.txt -m copy -a "content='Test content with backup\n' dest=/root/testcontent.txt

# Fetch file from the target server

ansible all -i inventory.txt -m fetch -a "src=/root/ansible/target.txt dest=/root/"

# Fetch file from the target server and put it in the same dir as mention in dest

# ps add {{inventory_hostname}} when fetch muliple server to not get an error

ansible all -i inventory.txt -m fetch -a "src=/root/ansible/target.txt dest=/root/{{inventory_hostname}}\_target.txt flat=yes"

# Create an empty file with file module

ansible all -i inventory.txt -m file -a "path=/root/ansible/hello.txt state=touch"

# specify the mode

ansible all -i inventory.txt -m file -a "path=/root/ansible/hello.txt state=touch mode=0777"

# Remove a file

ansible all -i inventory.txt -m file -a "path=/root/ansible/target.txt state=absent"

# Create a directory

ansible all -i inventory.txt -m file -a "path=/root/ansible/newdir state=directory"

# execute with root privilages

ansible all -i inventory.txt -m file -a "path=/root/ansible/newdir state=directory" -b

# install package

ansible all -i inventory.txt -m yum -a "name=git state=latest/present"

# Gather information with setup module & use filter

ansible all -i inventory.txt -m setup -a "filter=ansible_mounts"

# Get service version

ansible all -i inventory.txt -m shell -a "git --version"

# Get service status

ansible all -i inventory.txt -m shell -a "systemctl status firewalld"

# Gather custom fact local & remote server

mkdir /etc/ansible/facts.d

vim /etc/ansible/facts.d/script.fact

                #!/bin/bash

                git_ver=$(git --version | awk '{print $3}')
                httpd_ver=$(httpd -version | awk 'NR==1 {print $3}')

                cat << EOF
                { "Git_Version": "$git_ver",
                "httpd_version": "$httpd_ver"
                }
                EOF

chmod +x /etc/ansible/facts.d/script.fact

ansible all -i inventory.txt -m setup -a "filter=ansible_local"

# Use raw module into target without Python preinstalled

ansible all -i inventory.txt -m raw -a "uptime"

# check syntax playbook

ansible-playbook debug.yml --syntax-check
