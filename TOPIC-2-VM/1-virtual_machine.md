# Virtual Machines

---

## Virtual Machine (VM)

A virtual machine is a **fake computer created by software** inside a real computer. It behaves exactly like a real computer — it has its own operating system, CPU allocation, RAM, and storage — but it is not physical.

It exists only as software. You can create 5 virtual machines inside one physical machine, and each one thinks it is a real standalone computer.

> Developers use VMs to test software without needing multiple physical machines.

---

## Server

A server is any computer that **provides a service** to other computers over a network. It does not have to be a big machine — even your laptop can act as a server.

What makes it a server is its **role**, not its size.

| Type | What It Does |
|------|-------------|
| Web server | Serves web pages |
| Database server | Serves data |
| File server | Serves files |

---

## Physical Server

A physical server is a **real, actual computer** — a big box you can physically touch and see. It has a CPU, RAM, hard disk, and network ports just like your laptop, but it is much more powerful.

- Runs 24 hours a day without stopping
- Found in **data centres** — large rooms full of machines stacked in racks

> Example: the computer that actually runs Google's website somewhere in a building.

---

## Virtual Server

A virtual server is simply a **virtual machine being used as a server**.

- **Virtual** = not physical hardware — software pretending to be a machine
- **Server** = providing a service — hosting a website, running a database, handling emails

> Virtual server = a VM doing server work

Cloud providers like AWS and Azure sell virtual servers so companies do not need to buy physical hardware.

---

## Hypervisor

A hypervisor is the **software that makes virtual machines possible**. It sits between the physical hardware and the virtual machines.

Its job is to divide the real physical resources — CPU, RAM, storage — and share them among multiple VMs. Each VM thinks it owns the hardware, but the hypervisor is managing everything in the background.

> Think of the hypervisor as a **landlord** who owns a building and divides it into separate apartments for tenants.

### Types of Hypervisors

| Type | Description | Examples |
|------|-------------|---------|
| **Type 1** | Runs directly on hardware (bare metal) | VMware ESXi, Hyper-V |
| **Type 2** | Runs inside an existing OS | VirtualBox, VMware Workstation |
