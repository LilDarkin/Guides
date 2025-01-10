# Creating and Managing EC2 Users with SSH Access

## Prerequisite:
1. 2 Running Instances (Portal Instance and Main Instance)

## Steps:

### Step 1: Get the Public IP and Key Pairs of the 2 Instances

### Step 2: Generate an SSH Key Pair on Local Machine
```bash
ssh-keygen -t rsa -b 2048 -f ~/.ssh/id_rsa
```
This will generate:
- **A private key (`id_rsa`)** – Keep this secure on the user’s machine.
- **A public key (`id_rsa.pub`)** – This will be added to the EC2 instance.

### Step 3: Log in to the Portal Instance as the Admin User (ec2-user)

### Step 4: Add the User’s Public Key to Portal Instance
```bash
cd ~ec2-user/.ssh
nano authorized_keys
```
Paste the user’s public key (the contents of the `id_rsa.pub` file) into this file.
```bash
chmod 700 ~/.ssh
chmod 600 ~/.ssh/authorized_keys
```

### Step 5: Create a System User in Portal Instance
```bash
sudo useradd -m "ec2-bon"  # You can change the name
sudo mkdir /home/ec2-bon/.ssh
sudo chown ec2-bon:ec2-bon /home/ec2-bon/.ssh
sudo chmod 700 /home/ec2-bon/.ssh
```

### Step 6: Add the User’s Public SSH Key to the New User
```bash
sudo nano /home/ec2-bon/.ssh/authorized_keys
```
You can now try using:
```bash
ssh -i "ec2-bon@public-ip"
```

## For the User to Login to Another Instance Using the Portal Instance

### Step 7: In Portal Instance, Create a Configuration
```bash
cd ~/.ssh/
nano config
```
Add the following configuration:
```bash
Host main
    HostName 123.45.67.89  # Public IP of your Main instance
    User ec2-user
    IdentityFile ~/.ssh/pem_files/main-instance.pem  # Path of the Main Instance Pem File
```

Create a directory (`pem_files`) and file `main-instance.pem`.
The `main-instance.pem` must contain the key pair of the main instance.

```bash
chmod 600 ~/.ssh/config
chmod 400 ~/.ssh/pem_files/main-instance.pem
```

You can now try using:
```bash
ssh main
```
