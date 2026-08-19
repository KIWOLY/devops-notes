VM Cheat-sheet — Quick reminders

---

What is a VM (need-to-know)
- VM = software "computer" running on a physical host.
- Has guest OS, vCPU, vRAM, virtual disk, virtual NIC.
- Use VMs for isolation, testing, running multiple OS on one host.

Server vs Physical vs Virtual
- Server: role that serves network requests (web, db, file).
- Physical server: real hardware in a datacenter.
- Virtual server: a VM acting as a server (cloud or hypervisor).

Hypervisor (one-line)
- Hypervisor manages VMs and maps virtual resources to physical hardware.
- Type 1: bare-metal (ESXi, Xen, Hyper-V) — better perf for datacenters.
- Type 2: hosted (VirtualBox, VMware Workstation) — good for dev/workstation.

Key VM components (short)
- Guest OS, vCPU(s), vRAM, virtual disk (qcow2/vmdk/vhdx/raw), virtual NIC, device emulation.

VM lifecycle (quick)
- Image/template -> Provision -> Run -> Snapshot/Backup -> Migrate/Clone -> Terminate

Networking modes (desktop vs cloud)
- NAT: host NATs outbound; inbound needs port-forwarding.
- Bridged: VM gets its own LAN IP.
- Host-only: isolated host<->VM network.
- Cloud: VPCs, subnets, security groups, routing tables.

Storage provisioning (reminder)
- Thick: allocate full space upfront.
- Thin: allocate on demand (monitor capacity).
- Keep OS and data disks separate where possible.

VM vs Container (one-liners)
- VM: full OS, stronger isolation, larger resource overhead.
- Container: shares host kernel, lightweight, fast start, ideal for microservices.
- Choose VM when needing kernel-level isolation or different OSes; choose containers for app packaging and scale.

Create EC2 instance (console quick steps)
1. Console → EC2 → Launch Instance
2. Choose AMI, instance type (t2/t3), key pair, security group, storage
3. Launch and note public IP (or use private + SSM)

SSH connect quick steps
- Save/download key pair (.pem).
- Set permissions: `chmod 400 ~/keys/my-key.pem` (SSH will refuse loose perms).
- Example SSH:
  - Amazon Linux: `ssh -i my-key.pem ec2-user@PUBLIC_IP`
  - Ubuntu: `ssh -i my-key.pem ubuntu@PUBLIC_IP`
- Troubleshoot: check security group allows port 22, instance has public IP, use correct username, check `Permission denied (publickey)` (key mismatch or wrong permissions).

AWS Console connect options (short)
- EC2 Instance Connect (browser SSH): simple, requires Instance Connect support and SSH open.
- Session Manager (SSM): works without public IP if SSM Agent + IAM role with SSM permissions.

AWS CLI — quick configure
- Install CLI v2 and run: `aws configure --profile NAME` and enter Access Key ID, Secret, region, output.
- Verify: `aws sts get-caller-identity --profile NAME`
- Secure files: `chmod 600 ~/.aws/credentials ~/.aws/config`

Using root credentials (if you must)
- Console → My Security Credentials → Create access key (copy secret once).
- Configure with `aws configure --profile root` and verify with `aws sts get-caller-identity --profile root`.
- Security: enable MFA on root, delete root keys when finished, prefer IAM users/roles.

SSM & Instance Connect via CLI
- Start SSM session: `aws ssm start-session --target i-0123456789abcdef0`
- EC2 Instance Connect: `aws ec2-instance-connect send-ssh-public-key --instance-id i-... --availability-zone us-east-1a --instance-os-user ec2-user --ssh-public-key file://~/.ssh/id_rsa.pub`

Useful CLI snippets
- Describe public IP: `aws ec2 describe-instances --instance-ids i-... --query "Reservations[0].Instances[0].PublicIpAddress" --output text`
- List instances: `aws ec2 describe-instances --output table`

Best practices (short)
- Use least-privilege IAM roles for daily work.
- Automate with Terraform / Packer for images, cloud-init for first boot.
- Snapshots for quick rollback; backups for DR.
- Monitor CPU steal, I/O wait, datastore capacity (thin provisioning alerting).

Commands to remember (one-liners)
- `chmod 400 key.pem` — fix SSH key perms
- `ssh -i key.pem ubuntu@IP` — SSH connect
- `aws configure --profile myprofile` — setup CLI
- `aws sts get-caller-identity --profile myprofile` — confirm identity
- `aws ssm start-session --target i-...` — SSM connect
- `aws ec2-instance-connect send-ssh-public-key ...` — push temporary key

---
