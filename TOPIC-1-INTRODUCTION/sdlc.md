# Software Development Life Cycle (SDLC)

---

## What is SDLC?

SDLC is the **set of standards and steps** followed in the software industry to design, develop, test, deploy, and maintain software systems.

### Main Phases of SDLC

```
Planning (Requirements)
    ↓
Analysis / Specification
    ↓
Design
    ↓
Development
    ↓
Testing
    ↓
Deployment
    ↓
Maintenance
```

---

## Why SDLC is Important

A clear SDLC helps teams:

- Understand what the customer needs
- Reduce errors during development
- Improve software quality
- Deliver products faster and more safely
- Make changes easier in the future

---

## Where DevOps Fits in the SDLC

DevOps improves the later stages of SDLC by making them faster, more automated, and more reliable.

```
Build  →  Test  →  Deploy  →  Monitor
   (continuous and automated)
```

This is where concepts like **CI/CD** become very important.

---

## Building

> Converting source code into a working software program that can run on a machine.

| Step | What Happens |
|------|-------------|
| **1. Compiling** | Human code → machine language the computer understands |
| **2. Linking** | Combines code files and libraries together |
| **3. Packaging** | Bundles everything into one installable file |

---

## Testing

> Checking that the software works correctly before it is released to users. This is the primary role of Quality Engineers (QE).

| Type | Description |
|------|-------------|
| **Unit Testing** | Tests one small piece of code at a time |
| **Integration Testing** | Tests whether different parts work together correctly |
| **Regression Testing** | Checks that old features still work after new changes |
| **System Testing** | Tests the whole application end to end |

---

## Deployment

> Making the software available to users or customers.

In modern DevOps, deployment is usually done automatically using tools such as:

- Jenkins
- GitHub Actions
- GitLab CI/CD
- Azure DevOps

---

## CI/CD in SDLC

**CI** means Continuous Integration.

- Developers regularly merge their code into a shared repository
- The system automatically builds and tests the code

**CD** means Continuous Delivery or Continuous Deployment.

- The software is delivered quickly and reliably
- Changes can be released to production with less manual effort

This helps teams reduce delays and improve quality.

---

## Feedback and Maintenance

After deployment, the team keeps monitoring the system.

This includes:

- Checking performance
- Detecting bugs
- Monitoring logs and alerts
- Gathering user feedback

That feedback is sent back into the next planning and development cycle.

---

## DevOps Impact

- **Building**, **testing**, and **deployment** are automated
- Delivery becomes faster and more reliable
- Teams can respond to problems quickly
- Quality improves through continuous monitoring and testing

---

## DevOps Also Covers

| Phase | What It Does |
|-------|-------------|
| **Monitor** | Watches the live system for errors, crashes, and slow performance — sending alerts automatically |
| **Planning** | Uses feedback from monitoring and users to improve the next cycle |
| **Automation** | Reduces manual work and makes the process repeatable |
| **Collaboration** | Helps developers and operations teams work together smoothly |
