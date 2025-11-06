# Installing the LibreLane Flow

## Requirements

To install the LibreLane Flow, you need the **Nix package manager**, which is available for

- Linux and
- macOS

Windows users have to use the _Windows Subsystem for Linux_ and install the Nix package manager via WSL (Linux).

The following shows the setup process for Windows systems; the steps are similar for macOS and Linux.

### Installing the Windows Subsystem for Linux

#### Installing WSL

1. Installing the Windows Subsystem for Linux (WSL).

    To do this, open a Windows terminal and enter one of the following two commands:

    _wsl --install_

    _wsl.exe –install_

    This installation command installs an Ubuntu Linux distribution version 2 (WSL 2) by default, which is generally the most widely used and best supported.

    ![WSLInstall](Bilder5/WSLInstall.jpg)

2. However, any other Linux distribution can also be installed using the following command:

    _wsl --install -d_ _<distribution name>_

    While you must enter a username and password the first time you start the program (the password is not displayed for security reasons!), there is no limit to the number of different distributions that can be installed.

| **&lt;Distribution&gt;** | **Description** |
| --- | --- |
| Ubuntu | One of the most popular Linux distributions, known for its user-friendliness and large community. |
| Debian | Another popular distribution known for its stability and security. |
| Kali Linux | A distribution specifically designed for security audits and penetration testing. |
| openSUSE | A distribution known for its advanced tooling and customizability. |
| SUSE Linux Enterprise Server | A commercial distribution from SUSE optimized for enterprise use. |
| AlmaLinux | A free, community-supported distribution based on RHEL. |
| Fedora-Remix for WSL | A distribution created by Fedora specifically designed for WSL. |
| Alpine WSL | A lightweight, security-focused distribution. |
| Pengwin Enterprise | A commercial distribution based on OpenSUSE and developed for enterprises. |
| Pengwin on WSL | A Pengwin distribution optimized for use with WSL. |
| Mariner WSL | A distribution developed by Microsoft optimized for use with WSL. |
| Ubuntu-18.04 | An older version of Ubuntu that may be useful for certain applications. |
| Ubuntu-20.04 | An older version of Ubuntu that may be useful for certain applications. |
| Ubuntu-22.04 | An older version of Ubuntu that may be useful for certain applications. |

#### Updates and Version Upgrades for WSL

3. If you already have the Windows Subsystem Linux operating system installed, you can determine which Linux distribution and version is installed. To do this, you can use

    _wsl -l -v_

    The Ubuntu distribution in the WSL 2 version is preferred.
4. You can then either install the desired distribution using the above command, or update existing distributions using the following command, where sudo requires administrator privileges:

    _sudo apt update && sudo apt upgrade_

    You can also upgrade or downgrade to the desired version using the following commands:

    _wsl --set-version <distribution name> 2_

    _wsl --set-version <distribution name> 1_

    In general, however, you will not downgrade WSL 2 to WSL 1.

#### Starting WSL

5. To start the Windows Subsystem for Linux, use

    _wsl -d \<distribution name>_

    _wsl.exe -d \<distribution name>_

    ![WSLStart](Bilder5/WSLStart.jpg)

    Since different distributions can be installed, this opens the desired one.

#### Working in WSL

6. It should be noted that WSL (Windows Subsystem for Linux) itself does not have a "Windows mode." WSL is an environment that allows Linux distributions to run natively on Windows.

    Therefore, it is important to have knowledge of the Linux command register in order to use WSL from the prompt. See:

    https://wiki.ubuntuusers.de/Shell/Befehls%C3%BCbersicht/
7. To demonstrate that Windows Subsystem for Linux (WSL) is a real operating system, we'll show you how to access Microsoft Visual Studio Code (VS Code) as an example.

    If you don't have VS Code installed yet, please do so. The download is available at the following link:

    <https://code.visualstudio.com/download>

    VS Code is a versatile development environment that can be expanded almost infinitely using extensions. After installing it, open VS Code from the WSL terminal with:

    _code ._

    ![VSCode](Bilder5/VSCode1.jpg)

    For example, using the simple markup language Markdown, you can easily generate a professional text set.

    ![VSCode](Bilder5/VSCode2.jpg)
#### Closing WSL

8. To exit WSL, type

_exit_


#### Installing the nix package manager

#### Requirements

Minimum requirements:

- Quad-core CPU with at least 2.0 GHz
- 8 GB RAM

Recommended requirements:

- 6th Generation Intel® Core™ CPU or higher OR AMD Ryzen 1000 Series or higher
- 16 GB RAM
- Ubuntu 22.04

#### Installing the nix package manager

9. Do not install nix with apt, as the nix version provided by apt is often outdated and can cause problems – therefore, use curl.

    To install curl on Ubuntu, please enter the following command:

    _sudo apt-get install -y curl_

10. Then run the following command to install the Nix package manager

    _curl --proto '=https' --tlsv1.2 -sSf -L_ [_https://install.determinate.systems/nix_](https://install.determinate.systems/nix) _| sh -s -- install --no-confirm --extra-conf "_

    _extra-substituters = <https://nix-cache.fossi-foundation.org>_

    _extra-trusted-public-keys = nix-cache.fossi-foundation.org:3+K59iFwXqKsL7BNu6Guy0v+uTlwsxYQxjspXzqLYQs=_

    _"_

Enter your password when prompted. This may take a few minutes.

Afterward, close all terminals.

11. If you already have the Nix package manager installed:

- then you must manually enable LibreLane's Binary Cache using

<https://github.com/fossi-foundation/nix-eda/blob/main/docs/installation.md>

- update the /etc/nix/nix.conf file

    _extra-substituters = <https://nix-cache.fossi-foundation.org>_

    _extra-trusted-public-keys = nix-cache.fossi-foundation.org:3+K59iFwXqKsL7BNu6Guy0v+uTlwsxYQxjspXzqLYQs=_

- und starten Sie den nix-daemon erneut, mit:

    _sudo pkill nix-daemon_
## Installing the LibreLane Flow

During the installation of the LibreLane flow for open-source chip design for the production of digital ASICs,

- all necessary EDA (Electronic Design Automation) tools and
- LibreLane infrastructure libraries

are installed, which are required for a reference flow ("Classic").

### Downloading LibreLane

12. You will need Git to download the LibreLane software package. If necessary, install Git on WSL by first updating the WSL distribution and then installing Git.

    _sudo apt-get update_

    _sudo apt-get install git_

    You can verify the successful installation of Git with the following command:

    _git --version_

13. Once Git is installed, run the following command to download LibreLane:

    _git clone <https://github.com/librelane/librelane/> ~/librelane_
### Installing, Using, and Testing the LibreLane Flow

14. When LibreLane is installed upon first use, two things need to be done during this first use of LibreLane: First, run the nix-shell to make all packages bundled with LibreLane available.

    _nix-shell --pure ~/librelane/shell.nix_

    After downloading some packages from Git Hub, the binaries are retrieved from the cache and the LibreLane environment is installed. This may take a few minutes, after which the terminal prompt should change to: _\[nix-shell:path\]_, and you will have a complete LibreLane environment.

    ![LibreLaneInstall](Bilder5/LibreLaneInstall.jpg)

15. To test the installation, perform a smoke test by using the following command in the nix shell:

    _librelane --log-level ERROR --condensed --show-progress-bar --smoke-test_

    ![Smoketest](Bilder5/SmokeTest.jpg)

You have now completed your first "LibreLane Flow."