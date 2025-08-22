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

    run_remote_command(host, port, username, password, command)
