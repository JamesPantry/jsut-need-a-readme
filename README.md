**Student Number:** **664398**

---

## Configuration Implemented

### User Accounts
- Created users: marketing, design, and audit.
- Removed the sysadmin account and its home directory.
- Left maintenance unchanged.

### SSH Access
- All users can gain access to the system with the given ssh key from the sdi-client.
- `design` and `marketing` have normal shell access.
- `audit` is restricted to SFTP-only.

### Permissions and Access Control
- ACLs have been used to help manage permissions specified in the ACW:
  - **audit** has read-only access to `/home/marketing`, `/home/design`, and `/srv/www`.
  - **marketing** has write access to `/srv/www` for website uploads.
  - **design** has write access only within `/home/design`.
- Ensures separation of roles and prevents unauthorized access.

### SFTP Configuration
The following SSH match block restricts the audit account to SFTP only:

```
Match User audit
    ForceCommand internal-sftp
    PermitTTY no
    AllowTcpForwarding no
```

### Design Directory Structure
For `design`, required directories were created under `/home/design`:

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


- Accessible at: `http://stu-664398-vm1.net.dcs.hull.ac.uk/student/` with `text/plain` MIME type.
- The default site is served for both:
  - The VM IP address
  - `stu-664398-vm1.net.dcs.hull.ac.uk`

---

## Docker Application and Reverse Proxy

- The SDI application from `sbrl/SDI-Docker` was built into the image **sdi-app**.
- A container named **sdi-app** runs the service on port **3000** internally.
- Restart policy set to `unless-stopped` to ensure automatic start on boot.
- Nginx reverse proxy configuration:
  - **Static site** for VM IP and `stu-664398-vm1.net.dcs.hull.ac.uk`
  - **Docker application** served via `docker.stu-664398-vm1.net.dcs.hull.ac.uk` and proxied to `http://172.20.0.10:3000`.

---

## Firewall

- UFW enabled.
- Allowed: **22/tcp** (SSH), **80/tcp** (HTTP).
- Denied and allowed different ports as specified from Lab 3.

---

## Common Maintenance Tasks

### System Updates
```
sudo apt update && sudo apt upgrade
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

### User Operations
- **Marketing** uploads website content to `/srv/www`.
- **Design** works solely within `/home/design`.
- **Audit** performs read-only SFTP checks.

### File Permissions
- Adjust ACLs when necessary using `setfacl`.
- Keep `/srv/www` owned by root to prevent unauthorized modifications.

---
---
