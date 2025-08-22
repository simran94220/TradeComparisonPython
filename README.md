# ComparisonTrade through python
# VS code IDE




import paramiko

def run_remote_command(host, port, username, password, command):
    try:
        # Create SSH client
        ssh = paramiko.SSHClient()
        ssh.set_missing_host_key_policy(paramiko.AutoAddPolicy())

        # Connect to remote server
        print(f"Connecting to {host}...")
        ssh.connect(hostname=host, port=port, username=username, password=password)

        # Execute command
        print(f"Executing command: {command}")
        stdin, stdout, stderr = ssh.exec_command(command)

        # Read outputs
        output = stdout.read().decode()
        errors = stderr.read().decode()

        # Print results
        if output:
            print("=== OUTPUT ===")
            print(output)
        if errors:
            print("=== ERRORS ===")
            print(errors)

        # Close connection
        ssh.close()

    except Exception as e:
        print(f"Connection failed: {e}")


if __name__ == "__main__":
    # Replace with your server details
    host = "your.unix.server.com"   # or IP like "192.168.1.10"
    port = 22
    username = "your_username"
    password = "your_password"   # Consider using SSH key for security
    command = "ls -l"  # Example command




1...chain cmds


import paramiko
import getpass

def run_remote_commands(host, port, username, password, job_date):
    try:
        ssh = paramiko.SSHClient()
        ssh.set_missing_host_key_policy(paramiko.AutoAddPolicy())

        print(f"Connecting to {host}...")
        ssh.connect(hostname=host, port=port, username=username, password=password)

        # Build your command sequence
        commands = f"""
        cd /home/{username}/jobdir &&
        ./jobstarter.sh {job_date} &&
        echo "Job completed for {job_date}" &&
        ls -l
        """

        print("Executing remote commands...")
        stdin, stdout, stderr = ssh.exec_command(commands)

        output = stdout.read().decode()
        errors = stderr.read().decode()

        if output:
            print("=== OUTPUT ===")
            print(output)
        if errors:
            print("=== ERRORS ===")
            print(errors)

        ssh.close()
    except Exception as e:
        print(f"Error: {e}")


if __name__ == "__main__":
    host = "your.unix.server.com"
    port = 22
    username = "your_username"
    password = getpass.getpass("Enter password: ")   # safer input
    job_date = input("Enter job date (YYYY-MM-DD): ")

    run_remote_commands(host, port, username, password, job_date)




2. ineractive shell

import paramiko
import getpass
import time

def run_interactive_commands(host, port, username, password, job_date):
    ssh = paramiko.SSHClient()
    ssh.set_missing_host_key_policy(paramiko.AutoAddPolicy())
    ssh.connect(host, port, username, password)

    shell = ssh.invoke_shell()

    commands = [
        f"cd /home/{username}/jobdir",
        f"./jobstarter.sh {job_date}",
        "ls -l",
        "echo Done!"
    ]

    for cmd in commands:
        shell.send(cmd + "\n")
        time.sleep(2)  # wait for command to execute
        output = shell.recv(5000).decode()
        print(output)

    ssh.close()


if __name__ == "__main__":
    host = "your.unix.server.com"
    port = 22
    username = "your_username"
    password = getpass.getpass("Enter password: ")
    job_date = input("Enter job date (YYYY-MM-DD): ")

    run_interactive_commands(host, port, username, password, job_date)


    run_remote_command(host, port, username, password, command)
