- Hostname: ci-server
- IP: 192.168.122.207
- OS: Ubuntu 22.04
- Disk: 20GB

[200~## Installation Steps
1. Expanded disk from 2GB to 20GB
2. Fixed network connectivity (switched to default libvirt network)
3. Installed Java 17 (required for Jenkins 2.492.2)
4. Downloaded Jenkins .deb directly (bypassed expired GPG key)
5. Fixed dependencies with `apt install -f`
6. Configured Java 17 as default
7. Started Jenkins service

## Commands Used
```bash
# Disk expansion on HOST
qemu-img resize ci-server.img 20G
sudo growpart /dev/sda 1
sudo resize2fs /dev/sda1
```

# Java installation
sudo apt install openjdk-17-jdk -y

# Jenkins installation
wget https://pkg.jenkins.io/debian-stable/binary/jenkins_2.492.2_all.deb
sudo dpkg -i jenkins_2.492.2_all.deb
sudo apt install -f -y

# Jenkins configuration
sudo systemctl edit jenkins
# Added: Environment="JAVA_HOME=/usr/lib/jvm/java-17-openjdk-amd64"

# Jenkins Access

  - URL: http://192.168.122.207:8080

  - Initial password: sudo cat /var/lib/jenkins/secrets/initialAdminPassword
