# SSH Key Authentication Between VMs

## Goal

Configure passwordless SSH access from:

    ci-server

to:

    app-server

This is important for:
- DevOps automation
- Jenkins deployments
- CI/CD pipelines
- Ansible
- Remote server management

---

# Architecture

HOST MACHINE
│
├── ci-server        (Jenkins / Automation Server)
│      IP: 192.168.122.207
│
└── app-server       (Application Server)
       IP: 192.168.122.93

Communication flow:

    ci-server  ─────SSH────▶  app-server

---

# Step 1 — Verify VM Networking

## On ci-server

Check network information:

```bash
ip a
```
Expected output example:

```bash
inet 192.168.122.93/24
```

# Step 2 - Test Connectivity
## From ci-server
Ping app-server:
```bash
ping 192.168.122.93
```
Expected result:
```
64 bytes from 192.168.122.93
```

## From app-server
Ping ci-server
```bash
ping 192.168.122.207
```

# Step 3 - Test SSH Connection
From ci-server
```bash
ssh app-server@192.168.122.93
```
First connection usually asks:
- Are you sure you want to continue connecting?
Type 'yes'
If successful:
- app-server@app-server:~$
This confirms:
- SSH server is running
- Networing works
- VM communication works

# Step 4 - Generate SSH Keys
## IMPORTANT CONCEPT
SSH Key authentication uses:
| Key Type    | Purpose                   |
| ----------- | ------------------------- |
| Private Key | Stays secret on ci-server |
| Public Key  | Copied to app-server      |
The private key proves identiy.
The public key tells the server:
  "Allow whoever owns the matching private key."

# Generate SSH Key Pair
On ci-server:
```bash
ssh-keygen -t ed25519
```

# Explanation
| Part       | Meaning                     |
| ---------- | --------------------------- |
| ssh-keygen | Creates SSH keys            |
| -t         | Type of key                 |
| ed25519    | Modern secure SSH algorithm |

# Step 5 - Copy Public Key To app-server
On ci-server:
```bash
ssh-copy-id -i ssh_key.pub app-server@192.168.122.93
```
## Explanation
| Part                      | Meaning                            |
| ------------------------- | ---------------------------------- |
| ssh-copy-id               | Copies public key to remote server |
| -i ssh_key.pub            | Public key file                    |
| app-server@192.168.122.93 | Remote user and server             |

## What This Command does
The command copies the public key into:
~/.ssh/authorized_keys
on app-server.
This files controls:
- which SSH keys are allowed
- who can login using keys

# Step 6 - Login Using SSH Key
Since a custom key filename was used, SSH must be told which key to use.
On ci-server:
```bash
ssh -i ssh_key app-server@192.168.122.93
```
## Explanation
| Part                      | Meaning              |
| ------------------------- | -------------------- |
| ssh                       | Start SSH connection |
| -i ssh_key                | Use this private key |
| app-server@192.168.122.93 | Remote server        |


