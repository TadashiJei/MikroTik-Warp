# MikroTik-Warp
MikroTik Warp empowers you to take complete control over your WireGuard VPN on MikroTik routers with a user-friendly web interface.  Effortlessly add new clients, manage access permissions, and gain real-time insights into your VPN connections - all from a centralized dashboard.

| Login | Users | Connection |
| --- | --- | --- |
![login](https://raw.githubusercontent.com/TadashiJei/MikroTik-Warp/main/assets/326798218-3ea6b5b0-b9ca-4b1b-a546-955724d5bedf.png) | ![user](https://github.com/TadashiJei/MikroTik-Warp/assets/7698065/4864029e-c176-4577-96f1-20bf3e982b53) | ![connection](https://github.com/TadashiJei/MikroTik-Warp/assets/7698065/1f44b3b7-f4c6-4bd1-819a-b1e19fdf619c)

## Table of Contents

**Getting Started with MikroTik Warp**

* [Features](#features)  - Discover the benefits of MikroTik Warp.
* [Requirements](#requirements)  - Ensure your system meets the needs of MikroTik Warp.
* **Configuration**
    * [Adding Users](#adding-a-user-to-mikrotik-for-mikroguard)  - Set up user accounts for MikroTik access.
    * [Creating a WireGuard Interface](#creating-a-road-warrior-wireguard-interface-for-mikroguard)  - Configure the WireGuard connection on your MikroTik router.
    * [Server Installation (Optional)](#server-installation)  - Install the MikroTik Warp server software (if applicable).
* [Local Development (Optional)](#local-development-and-testing-with-docker-compose)  - Run MikroTik Warp for testing purposes using Docker Compose.
* [Troubleshooting](#troubleshooting)  - Find solutions to common issues encountered with MikroTik Warp.
* [Contributing](#contributing)  - Learn how to contribute to the development of MikroTik Warp.
* [License](#license)  - Understand the licensing terms for using MikroTik Warp.

## Features

* **Simplified Client Management:** Effortlessly add, edit, and manage WireGuard clients from a centralized dashboard on your MikroTik router.
* **Real-time Monitoring:** Gain real-time insights into VPN connections, including connected clients, data usage, and connection status.
* **Granular Access Control:** Easily grant or revoke VPN access for individual clients, ensuring optimal security.
* **Google SSO (Optional):** Enhance security and user experience with secure sign-in using Google's Single Sign-On system (optional).

## Requirements

* MikroTik router running RouterOS version 7.0 or later.
* Docker (version 19.03.0 or later) for running the MikroTik Warp server.

## Security

For transparency, MikroTik Warp interacts with your MikroTik router through the following PHP files:
|api/app/RouterOS/WireGuard.php
|api/app/RouterOS/IPAddress.php

These files contain the specific commands sent to the router. It's important to note that:

* No user input is ever directly passed to the router.
* Only pre-defined values you set as an administrator through environment variables are used.

We recommend reviewing these files if you'd like a deeper understanding of how MikroTik Warp interacts with your router. For more comprehensive security information, please refer to our separate security documentation.

## Setup

### Adding a User to MikroTik for MikroGuard

**Using Winbox:**

1. Log in to your MikroTik router using Winbox.
2. Navigate to the **System** menu and select **Users**.
3. Click on the **Groups** tab.
4. Click the **Add New** button to create a new user group.
5. Enter a name for the group, such as "MikroGuard-group," and click **OK**.
6. In the **Permissions** tab, select the desired permissions for the group. For MikroGuard, the user group should have **read, write, and API** access.
7. Click **Apply** to save the changes.
8. Click on the **Users** tab.
9. Enter the user's details, such as their name and password.

> **Warning:** Always enter the correct local subnet into the allowed address. If unsure about the configuration, seek expert advice.

10. In the **Groups** tab, select the "MikroGuard-group" group you just created.
11. Click **Apply** to save the changes.

**Using Command Line:**

To set up a new user group and user, input the following commands:

```sh
/user group add name=MikroGuard-group policy=local,read,write,test,api,winbox,password

# Replace "username" and "userpassword" with your desired credentials
/user add name=username group=MikroGuard-group password=userpassword
```

### Server Installation

**Prerequisites:**

1. Ensure Docker is up and running on your server.

**MikroGuard Deployment:**

1. **Generate an App Key:** You will need a secure app key for environment variables. We recommend using a dedicated password generator like [this generator](https://generate-random.org/password-generator). 

2. **Choose Deployment Method:** MikroGuard offers two deployment options:

    * **With Google Single Sign-On (SSO):** This option leverages Google SSO for user authentication.
    * **Without Google Single Sign-On:** This option uses traditional username and password authentication.

**Deployment Commands:**

The following commands demonstrate deploying MikroGuard with both options.  **Choose the appropriate set of commands based on your preferred authentication method.**

**With Google Single Sign-On:**

**Docker Command**
````bash
docker run -d
--name MikroWarp
-p 8000:8000
-v /path/to/data:/opt/app/storage
-e APP_KEY=
-e GOOGLE_CLIENT_ID=
-e GOOGLE_CLIENT_SECRET=
-e GOOGLE_REDIRECT_URL='https://my.public.address/api/auth/oauth/google/callback'
-e ROUTEROS_HOST='192.168.0.1'
-e ROUTEROS_PORT='8728'
-e ROUTEROS_USER='wireguard'
-e ROUTEROS_PASS='wireguard pass'
-e ROUTEROS_WIREGUARD_INTERFACE='wireguard' #wireguard interface name 
-e ROUTEROS_WIREGUARD_ENDPOINT='192.168.0.1:13231' #ip:port for wireguard interface
-e APP_URL='https://my.public.address'
ghcr.io/TadashiJei/MikroTik-Warp:latest
````

**Docker Compose**:
```yml
version: '3.8'
services:
  MikroGuard:
    image: ghcr.io/TadashiJei/MikroTik-Warp:latest
    container_name: MikroWarp
    restart: always
    ports:
      - 8000:8000
    volumes:
      - /path/to/data:/opt/app/storage
    environment:
      - APP_KEY=
      - GOOGLE_CLIENT_ID=
      - GOOGLE_CLIENT_SECRET=
      - GOOGLE_REDIRECT_URL=https://my.public.address/api/auth/oauth/google/callback
      - ROUTEROS_HOST=192.168.0.1
      - ROUTEROS_PORT=8728
      - ROUTEROS_USER=wireguard
      - ROUTEROS_PASS=wireguard pass
      - ROUTEROS_WIREGUARD_INTERFACE=wireguard
      - ROUTEROS_WIREGUARD_ENDPOINT=192.168.0.1:13231
      - APP_URL=https://my.public.address

```
**Environment Varibles**:
This is a list of the most useful environment variables. To find all available look in api/config files

| Variable | Description | Default |
| --- | --- | --- |
| APP_KEY* | App key used mainly for encryption, set using [this generator](https://generate-random.org/laravel-key-generator) |  |
| APP_URL* | The url this instance will be accessed from, can be localhost, an IP address or a domain eg. http://localhost:8000 | http://localhost:8000 |
| GOOGLE_CLIENT_ID | Google OAuth Client ID (Only required if using google auth) |  |
| GOOGLE_CLIENT_SECRET | Google OAuth Client Secret (Only required if using google auth) |  |
| GOOGLE_REDIRECT_URL | Google OAuth Redirect Url, eg. https://my.public.address/api/auth/oauth/google/callback (Only required if using google auth) |  |
| ROUTEROS_HOST* | IP address of your MikroTik router |  |
| ROUTEROS_PORT* | API port to access the router |  |
| ROUTEROS_USER* | User to log into the router |  |
| ROUTEROS_PASS* | Password to use to log into the router |  |
| ROUTEROS_WIREGUARD_INTERFACE* | WireGuard interface name, must match the wireguard interface name created on the MikroTik router eg. wireguard Road Warrior |  |
| ROUTEROS_WIREGUARD_ENDPOINT* | Your public IP clients use to connect to your WireGurad server on your MikroTik Router including the port eg. 123.123.123.123:12345 |  |
| ROUTEROS_WIREGUARD_SERVER_NAME | Default server name given to clients, can be anything | WireGuard Server |
| ROUTEROS_WIREGUARD_DNS | Client DNS server to use | 1.1.1.1 |
| ROUTEROS_WIREGUARD_ALLOWED_IPS | Client IPs to forward, Defaults to everything | 0.0.0.0/0 |


*Required

## Initial User
   To create the initial user run: (replace: admin@xterm.me with your email)
   ```bash
   docker compose exec Mikro-Warp php artisan app:create-user admin@tadashijei.com admin 
   ```
