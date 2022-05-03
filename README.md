# Ansible deployment of .net on ubuntu server 20.04

## Initial settings

Define the variables on inventory.yml as per your needs

> Maintain the current indentation.

Change variables on inventory.yml
```bash
ansible_host: '<hostname>'
username: '<username>'
app_env: Development
telemetry_message: true
project_name: <project-name>
root_path: <root-path>
```

Ansible connection can be set as local or ssh
```bash
ansible_connection: '<connection>'
```

SSH private key file needs to be defined when using ssh connection
```bash
ansible_ssh_private_key_file: /path/to/your/ssh-key
```

Install ansible in your system
```bash
sudo apt -y install ansible
```
> Required ansible version 2.9.6 or greater

## Usage and information

Install .net in your local ubuntu desktop 20.04
```bash
ansible-playbook -i inventory.yml dotnet.yml
```
> Add flag `-K` in case user has credentials

Copy your source code to the project folder created in user directory and create links to config service and nginx files.
```bash
sudo ln -s <root-path>/nginx.conf /etc/nginx/site-enabled/your-config-name.conf
sudo ln -s <root-path>/kestrel.service /etc/systemd/system/kestrel.service
```

Enable and start kestrel and nginx services
```bash
# enable and start kestrel service
sudo systemctl enable kestrel
sudo service kestrel start

# reload and restart nginx service
sudo service nginx reload
sudo service nginx restart
```
> You may prefer to move the config service and nginx files to it's location, using `sudo mv source destination`.

### Tags

Using tags helps to define which roles will be selected or skipped

Run only tags with tags `common` and `ufw`
```bash
ansible-playbook -i inventory.yml dotnet.yml --tags "common,ufw"
```

Run all tasks except those with the tags `dotnet` and `nginx`
```bash
ansible-playbook -i inventory.yml dotnet.yml --skip-tags "dotnet,nginx"
```

## Create a simple application

```bash
dotnet new webapp -o <project-name>
```
## Publish a release project

```bash
dotnet publish -c Release -o <root-path>
```
