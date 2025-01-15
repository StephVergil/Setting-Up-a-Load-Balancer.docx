# Setting Up a Load Balancer: A Comprehensive Guide

This guide provides an in-depth walkthrough for configuring web servers and a load balancer using **Windows IIS**, **Ubuntu Apache**, and **HAProxy** on pfSense. It explains the concepts, purpose, and practical steps required to create a scalable, reliable load-balanced environment.

---

## Project Link

[Setting Up a Load Balancer](https://github.com/StephVergil/Setting-Up-a-Load-Balancer.docx/blob/main/Setting%20Up%20a%20Load%20Balancer.docx)

---

## Overview

**What is Load Balancing?**

Load balancing is a method of distributing incoming traffic across multiple servers to:
- Prevent any single server from becoming overwhelmed.
- Ensure high availability and reliability.
- Optimize performance and scalability of services.

**Lab Objectives:**
1. Configure backend web servers (Windows IIS and Ubuntu Apache).
2. Set up **HAProxy** in pfSense as the load balancer.
3. Validate the load balancer with **round-robin traffic distribution**.

---

## Prerequisites

- Access to **WinOS**, **UbuntuSRV**, and **Kali VM**.
- Admin credentials for each server.
- Basic understanding of IIS, Apache, and HAProxy.

---

## Step-by-Step Guide

### 1. Setting Up Backend Web Servers

#### **1.1 Configuring IIS on Windows Server**
1. **Install IIS**:
   - Open **Server Manager**.
   - Click **Manage** → **Add Roles and Features**.
   - Select **Web Server (IIS)** → Click **Install**.
   - Wait for the installation to complete.

   **Purpose**: IIS allows Windows to host web content and handle HTTP requests.

2. **Create an Index Page**:
   - Open **Notepad** as an administrator.
   - Add the following content:
     ```html
     Hello, this is WinOS server!
     ```
   - Save as `index.html` in `C:\inetpub\wwwroot\`.

   **Purpose**: This page verifies the IIS server's functionality.

3. **Verify IIS**:
   - Open a browser and navigate to `http://localhost`.
   - Confirm that the page displays: `Hello, this is WinOS server!`.

---

#### **1.2 Configuring Apache on Ubuntu**
1. **Prepare the Environment**:
   - Navigate to `/var/www/html`:
     ```bash
     cd /var/www/html
     ```
   - Remove default files:
     ```bash
     sudo rm index.html index.nginx-debian.html
     ```
   - Stop Nginx:
     ```bash
     sudo service nginx stop
     ```

   **Purpose**: Prepares the environment for Apache by removing conflicting services and files.

2. **Create an Index Page**:
   - Create a new file:
     ```bash
     sudo nano index.html
     ```
   - Add the content:
     ```html
     Hello, this is the UbuntuSRV server!
     ```
   - Save (`Ctrl+S`) and exit (`Ctrl+X`).

   **Purpose**: This page confirms the Apache server's operation.

3. **Start Apache**:
   - Enable Apache:
     ```bash
     sudo service apache2 start
     ```

   **Verify**:
   - Open a browser and navigate to `http://localhost`.
   - Confirm that the page displays: `Hello, this is the UbuntuSRV server!`.

---

### 2. Configuring HAProxy for Load Balancing

#### **2.1 Backend Configuration**
1. **Access pfSense**:
   - Open a browser and navigate to `http://172.16.1.1`.
   - Log in using the provided credentials.

   **Purpose**: pfSense is the interface for managing HAProxy.

2. **Define Backend Servers**:
   - Navigate to **Services** → **HAProxy** → **Backend** → **Add**.
   - Add servers:
     - **WinOS**:
       - Address: `192.168.0.50`
       - Port: `80`
       - Weight: `50`
     - **UbuntuSRV**:
       - Address: `172.16.1.10`
       - Port: `80`
       - Weight: `50`
   - Select **Round Robin** as the balancing method.

   **Purpose**: Backend servers are the destinations for distributed traffic.

---

#### **2.2 Frontend Configuration**
1. **Create a Frontend Entry**:
   - Navigate to **Frontend** → **Add**.
   - Configure:
     - Name: `WebServerFrontend`
     - Default Backend: `WebServerBackendPool`.

   **Purpose**: The frontend defines the external access point for users.

2. **Activate HAProxy**:
   - Go to **Settings**.
   - Enable **HAProxy** and set the maximum connections to `10`.

   **Purpose**: Activating HAProxy begins load balancing operations.

---

### 3. Testing the Load Balancer

1. **Validate Functionality**:
   - Open a private browser window.
   - Navigate to the external address: `http://203.0.113.1`.
   - Refresh the page multiple times.
   - Confirm alternating messages:
     - `Hello, this is WinOS server!`
     - `Hello, this is the UbuntuSRV server!`

   **Purpose**: This demonstrates successful traffic distribution.

2. **Understand Sticky Sessions**:
   - Without sticky sessions, user sessions may switch servers upon refresh.
   - Sticky sessions ensure continuity, critical for applications like e-commerce.

   **Purpose**: Sticky sessions bind users to a single server for session persistence.

---

## Key Takeaways

- **Efficient Traffic Management**: HAProxy distributes requests across servers to balance resource usage.
- **Scalability**: Adding more servers increases capacity without downtime.
- **Customization**: Explore advanced features like sticky sessions and SSL termination.

---

## Resources

- **Web Servers**:
  - [IIS Documentation](https://learn.microsoft.com/en-us/iis/)
  - [Apache HTTP Server Documentation](https://httpd.apache.org/docs/)
- **Load Balancers**:
  - [HAProxy Documentation](http://www.haproxy.org/#docs)
  - [pfSense Documentation](https://docs.netgate.com/pfsense/en/latest/)
- **Further Learning**:
  - [Load Balancing with HAProxy](https://www.haproxy.com/documentation/)

---

### Disclaimer
This project was conducted in a controlled environment. Unauthorized use of these techniques or tools outside such an environment may violate ethical guidelines and legal regulations.

---

> **Author**: Stephanie Vergil
