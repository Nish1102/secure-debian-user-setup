
---


# Secure Debian User Setup with SSH Key Authentication

This repository contains detailed steps to create a new user on a Debian server, enable secure SSH key-based login, and disable root login for enhanced security. It also includes steps to copy `/root` files to the new user.

---

## **Steps**

### 1. Create a User on the Debian Server
1. **Log in to the server as root:**
   ```bash
   ssh root@your_server_ip
   ```

2. **Create a new user (`nishant`):**
   ```bash
   adduser nishant
   ```

3. **Grant sudo privileges to the user (optional, for administrative tasks):**
   ```bash
   usermod -aG sudo nishant
   ```

---

### 2. Generate an SSH Key Pair on Your Local Machine
1. **Generate an SSH key pair:**
   ```bash
   ssh-keygen -t rsa -b 4096 -f nishant.pem
   ```

   - This will create:
     - `nishant.pem` (private key)
     - `nishant.pem.pub` (public key)

2. **Secure the private key:**
   ```bash
   chmod 400 nishant.pem
   ```

---

### 3. Add the Public Key to the Server
1. **Log in as root to the server.**

2. **Create the `.ssh` directory for the `nishant` user:**
   ```bash
   mkdir -p /home/nishant/.ssh
   chmod 700 /home/nishant/.ssh
   chown nishant:nishant /home/nishant/.ssh
   ```

3. **Copy the public key (`nishant.pem.pub`) to the server:**

   - Use `scp` to transfer the public key:
     ```bash
     scp nishant.pem.pub root@your_server_ip:/home/nishant/.ssh/authorized_keys
     ```

   - Alternatively, manually add the public key:
     - Open the `authorized_keys` file on the server:
       ```bash
       nano /home/nishant/.ssh/authorized_keys
       ```
     - Paste the contents of `nishant.pem.pub` into the file, save, and exit.

4. **Set permissions for the `authorized_keys` file:**
   ```bash
   chmod 600 /home/nishant/.ssh/authorized_keys
   chown nishant:nishant /home/nishant/.ssh/authorized_keys
   ```

---

### 4. Test Logging in with the `.pem` File
1. **Log out from the server.**

2. **Use the private key to log in as `nishant`:**
   ```bash
   ssh -i nishant.pem nishant@your_server_ip
   ```

---

### 5. Disable Root Password Login (Optional but Recommended)
1. **Edit the SSH configuration file:**
   ```bash
   nano /etc/ssh/sshd_config
   ```

2. **Update the following lines:**
   ```text
   PermitRootLogin no
   PasswordAuthentication no
   ```

3. **Restart the SSH service:**
   ```bash
   systemctl restart sshd
   ```

   Now, you can securely log in as `nishant` using the `.pem` file, and root login via password is disabled for added security.

---

### 6. Steps to Copy `/root` to `nishant`
1. **Switch to Root User:**
   ```bash
   sudo su
   ```

2. **Copy the files:**
   ```bash
   cp -R /root/* /home/nishant/
   ```

3. **Set correct ownership:**
   ```bash
   chown -R nishant:nishant /home/nishant
   ```

4. **Verify the files:**
   - Switch to the `nishant` user:
     ```bash
     su - nishant
     ```
   - Check the files:
     ```bash
     ls -l
     ```

---

## **Testing and Verification**
1. Confirm you can log in using the `.pem` file.
2. Verify root login is disabled by trying to log in as root.
3. Check ownership and accessibility of the `/root` files under the `nishant` user.

---

