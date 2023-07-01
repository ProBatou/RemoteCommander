# RemoteCommander
## Description

RemoteCommander is a Bash script that facilitates the management of remote servers by enabling file transfer and remote command execution. It simplifies the configuration process by allowing users to easily add new servers to a list and perform actions on them efficiently.

The script utilizes the SSH method to connect to remote servers, using sshpass to automate password authentication. It also checks if hosts are present in the known hosts list and adds them if necessary to facilitate future connections.

## Dependencies
- sshpass (installed on the system)

## Setup

1. Install sshpass using the appropriate command for your operating system (e.g., on Ubuntu, use `apt install sshpass`).
2. Download the `remote_commander.sh` script from the GitHub repository: [remote_commander.sh](https://github.com/ProBatou/RemoteCommander/blob/main/remote_commander.sh)
3. Make the script executable using the command `chmod +x remote_commander.sh`.
4. You can now run the script using the command `./remote_commander.sh`.

To obtain the RemoteCommander script, please visit the GitHub repository: [RemoteCommander](https://github.com/ProBatou/RemoteCommander)
