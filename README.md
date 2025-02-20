# `ubuntu_upgrade` Ansible Role

This Ansible role automates the upgrade process for Ubuntu servers, handling tasks such as stopping Docker containers, purging specific packages, updating and upgrading packages, and performing full distribution upgrades from Ubuntu 20.04 to 22.04, and then to 24.04. The role is designed for minimal manual intervention, accepting default configurations where appropriate.

## Requirements

- Ansible 2.9+.
- A target system running Ubuntu 20.04 or 22.04.
- Network access to fetch packages and perform upgrades.
- Sudo privileges on the target machine.

## Role Variables

| Variable          | Description                                                         | Default Value |
|-------------------|---------------------------------------------------------------------|---------------|
| `upgrade_prompt`  | Determines the upgrade release prompt (e.g., `lts` or `normal`).    | `lts`        |

This variable is set dynamically within the role to align with the specific upgrade being performed.

## Tasks Overview

1. **Docker Container Management**
   - Stops Docker containers gracefully before the upgrade to avoid service interruptions.
   
2. **Purge NVIDIA Drivers**
   - Uninstalls specific NVIDIA driver packages that may cause conflicts during the upgrade.

3. **Package Update and Upgrade**
   - Updates all packages to their latest versions.
   - Performs a full distribution upgrade using `apt-get dist-upgrade`.
   - Removes obsolete packages and dependencies.

4. **Upgrade to 22.04 (if running 20.04)**
   - Ensures the release upgrade prompt is set to `lts` for compatibility.
   - Uses the `do-release-upgrade` tool in non-interactive mode for the upgrade.
   - Reboots the system upon completion of the upgrade to 22.04.

5. **Upgrade to 24.04 (or latest) (if running 22.04)**
   - Configures the prompt to `normal` for the latest non-LTS upgrade.
   - Runs `do-release-upgrade` in non-interactive mode to upgrade from 22.04 to 24.04.
   
6. **End Verification**
   - Confirms and reports the final OS version after the upgrade completes.
   - Instructs users to run the NVIDIA role after the OS upgrade if necessary.

## Example Playbook

Here’s an example of how to use this role in a playbook:

```yaml
- name: Upgrade Ubuntu Servers
  hosts: os_Linux
  gather_facts: yes
  roles:
    - role: ubuntu_upgrade
      tags: ubuntu_upgrade
```

## Usage Notes

- **Interactive Prompts**: The role configures `do-release-upgrade` for non-interactive execution to avoid prompts.
- **NVIDIA Drivers**: The role removes specific NVIDIA drivers, as they may cause issues during upgrades. Ensure to reinstall or update NVIDIA drivers after completing the OS upgrade.
- **Upgrade Sequence**: This role upgrades Ubuntu servers in sequence: from 20.04 to 22.04, and then to 24.04, based on the detected version.
- **Reboots**: The role includes reboots after each major upgrade to ensure system stability.

## Important Files and Directories

- `tasks/do-release-upgrade.yaml`: Task file containing instructions for upgrading any ubuntu version with "do release".
