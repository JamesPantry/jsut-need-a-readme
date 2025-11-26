**Student Number:** **664398**

---

## Configuration Implemented

### User Accounts
- Created users: **marketing**, **design**, and **audit**.
- Removed the **sysadmin** account and its home directory.
- Left the **maintenance** account unchanged.

### SSH Access
- All users can use SSH key authentication with the keys provided by the SDI-client.
- `marketing` and `design` have normal shell access.
- `audit` is restricted to SFTP-only and cannot obtain a login shell.

### Permissions and Access Control
- ACLs were used to implement the access rules specified in the ACW:
  - **audit** has read-only access to `/home/marketing`, `/home/design`, and `/srv/www`.
  - **marketing** has write access to `/srv/www` for website uploads.
  - **design** can write only within `/home/design`.
- This prevents unauthorised access between accounts.

### SFTP Configuration
The following SSH match block enforces SFTP-only access for the audit user:

```
Match User audit
    ForceCommand internal-sftp
    PermitTTY no
    AllowTcpForwarding no
```

### Design Directory Structure
The required directory structure for the design account was created:

```
project_rocket/cad
project_rocket/render
project_cheese/research
project_cheese/tests
```

---

## Web Server

- Nginx installed and configured to serve static files on port **80**.
- Web root set to `/srv/www`.
- Created `/srv/www/student/index.txt` containing the required student number: `664398`.
- The file is accessible at:  
  `http://stu-664398-vm1.net.dcs.hull.ac.uk/student/` with MIME type `text/plain`.
- The default static site is served for:
  - `stu-664398-vm1.net.dcs.hull.ac.uk`

---

## Docker Application and Reverse Proxy

- Built the SDI application from `sbrl/SDI-Docker` into the image **sdi-app**.
- A container named **sdi-app** runs internally on port **3000**.
- Restart policy set to `unless-stopped` so the service starts automatically on boot.
- Nginx reverse proxy configuration:
  - **Docker application** served via `docker.stu-664398-vm1.net.dcs.hull.ac.uk` and proxied to `http://172.20.0.10:3000`.

---

## Firewall

- UFW enabled.
- Allowed ports: **22/tcp** (SSH), **80/tcp** (HTTP).
- Additional allow/deny rules applied as configured during Lab 3.

---

## Common Maintenance Tasks

### System Updates
```
sudo apt update
sudo apt upgrade
```

### SDI-Client
```
sudo sdi-client update
```
### Service Status Checks

- Check Nginx status:
  ```
  systemctl status nginx
  ```

- Check Docker service status:
  ```
  systemctl status docker
  ```

### Service Management
- Restart Nginx:
  ```
  sudo systemctl restart nginx
  ```
- Restart Docker:
  ```
  sudo systemctl restart docker
  ```
- Check Docker status:
  ```
  sudo docker ps
  sudo docker logs sdi-app
  ```

---
