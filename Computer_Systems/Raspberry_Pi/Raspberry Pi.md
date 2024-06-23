---
tags:
  - Computer_Systems
---
# Core principles
Raspberry Pi is a **Computer system**, i.e. with the corresponding components:
- [[Operating System#CPU|CPU]]
- [[Operating System#Main memory (RAM)|RAM]]
- However, it doesn't come with [[Operating System#Secondary memory (Disk)|Disk]]! You have to supply it (e.g. by plugging a microSD card or USB external storage)
- ...
# Hands on
## Get started
### Raspberry Pi 4 model B
_Refer to \[[[#References|1]]\]_
Follow these steps:
1. [[#Install an Operating System]]
#### Install an [[Operating System]]
##### Ubuntu - What OS should I install?
_You can download Ubuntu for Raspberry Pi \[[[#References|3]]\]. There are three versions: Ubuntu Desktop, Ubuntu Server and Ubuntu Core. We will install Ubuntu **Server** \[[[#References|4]]\]_
The choice between Ubuntu Server, Core, and Desktop for your Raspberry Pi depends on the specific needs of your projects and how you plan to use the device. Here’s a brief overview of each version:
1. **Ubuntu Server**:
    - **Intended Use**: Ideal for server-related projects, such as hosting web servers, file servers, or other network services.
    - **Interface**: Command-line interface only (no graphical user interface by default).
    - **Resource Usage**: Low, as it doesn't include a desktop environment, leaving more resources available for server tasks.
    - **Flexibility**: You can install a desktop environment later if needed.
    - **Recommendation**: Best for headless setups where you access the Pi remotely via SSH.
3. **Ubuntu Core**:
    - **Intended Use**: Designed for IoT (Internet of Things) and embedded systems.
    - **Interface**: Minimal, with a focus on security and robustness.
    - **Resource Usage**: Very low, as it is stripped down to the essentials.
    - **Flexibility**: Limited compared to Server and Desktop, as it’s focused on being lightweight and secure.
    - **Recommendation**: Best for IoT projects where security and minimal resource usage are critical.
4. **Ubuntu Desktop**:
    - **Intended Use**: General-purpose use, similar to a traditional desktop or laptop.
    - **Interface**: Full graphical user interface with access to a wide range of applications.
    - **Resource Usage**: Higher, as it includes a full desktop environment.
    - **Flexibility**: Most flexible and user-friendly for those who need a graphical interface for development or multimedia tasks.
    - **Recommendation**: Best for projects that benefit from a graphical interface, or for users who prefer a traditional desktop experience.

**Which One is More Generic for Projects?**
For most generic project needs, **Ubuntu Server** is typically the best choice:
- **Flexibility**: You can start with a minimal setup and add only the packages and tools you need.
- **Performance**: Lower resource usage compared to Desktop, leaving more CPU and memory available for your projects.
- **Expandability**: If you need a graphical user interface later, you can install it on top of Ubuntu Server.

However, if your projects specifically require a graphical interface, or you prefer working with a GUI, **Ubuntu Desktop** might be a better fit despite its higher resource usage.
##### On microSD or USB (recommended)
Follow these steps:
1. On any computer you have, download and install **Raspberry Pi Imager** \[[[#References|2]]\]
2. Plug the microSD or USB storage in that computer
3. Run **Raspberry Pi Imager**, and can select the [[Operating System]] you want as well as the microSD/USB. You can select _Other general-purpose OS > Ubuntu > Ubuntu Server LTS_
4. Configure hostname, [[SSH]] and user/password

# References
1. [Getting started - Raspberry Pi Documentation](https://www.raspberrypi.com/documentation/computers/getting-started.html)
2. [Raspberry Pi Imager](https://www.raspberrypi.com/software/)
3. [Ubuntu for Raspberry Pi | Ubuntu](https://ubuntu.com/raspberry-pi)
4. [How to install Ubuntu Server on your Raspberry Pi | Ubuntu](https://ubuntu.com/tutorials/how-to-install-ubuntu-on-your-raspberry-pi#1-overview)