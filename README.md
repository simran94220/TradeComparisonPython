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


--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

RunEODBatch.py FXO HBAP 18-08-2025             create a folder named files under project and place file names FXO_HBAP containing all list of commands---------------------------------------------------------------
# Run EOD batch method with 3 parameters 1. asset class 2. region 3. date

import sys
import datetime as dt
#import subprocess
#import paramiko
import time
import os


# --- Date handling ---
def parse_process_date(date_text: str) -> str:
    """
    Accepts either 'YYYY-MM-DD' or 'dd-mm-YYYY' (also tolerates '/' separators).
    Returns date string formatted as 'dd-mm-YYYY'.
    """
    date_text = date_text.strip()
    tried = []
    for fmt in ("%Y-%m-%d", "%d-%m-%Y", "%Y/%m/%d", "%d/%m/%Y"):
        try:
            d = dt.datetime.strptime(date_text, fmt).date()
            return d.strftime("%d-%m-%Y")  # final required format
        except ValueError:
            tried.append(fmt)
            continue
    raise ValueError(
        f"Incorrect date format. Try one of: {', '.join(tried)}. "
        "Example: 2025-08-24 or 24-08-2025"
    )
##replace placeholder {{process_date}} in text file function
def replace_process_date(file_path, process_date):
    """
    Replace placeholder {{PROCESS_DATE}} in the text file with actual process_date.
    Returns the updated list of commands.
    """
    with open(file_path, "r") as file:
        commands = file.readlines()

    # Replace placeholder
    updated_commands = [cmd.replace("{{PROCESS_DATE}}", str(process_date)) for cmd in commands]

    # Optionally overwrite the file (or keep as temp)
    with open(file_path, "w") as file:
        file.writelines(updated_commands)

    return updated_commands


def get_command_file(asset_class, region, process_date):
    """
    Chose the right command file based on asset class and region.
    
    """
    print(f"\n🚀 Starting EOD Batch Execution...")
    print(f"Asset Class: {asset_class}")
    print(f"Region: {region}")
    print(f"Date: {process_date}")

    # Example: Map jobs based on asset class & region will pick up file from files folder
    job_map = {
        ("FXO", "HBAP"): "FXO_HBAP.txt",
        ("FXO", "HBEU"): "FXO_HBEU.txt",
        ("FXO", "APAC"): "FXO_APAC.txt",
    }
    #filename is from files folder where job scripts are stored
    file_name = job_map.get((asset_class, region))
    if not file_name:
        print("⚠️ No batch defined for this combination.")
        return
    
    # Path to commands file
    file_path = os.path.join("files", file_name)
    return file_path

   # # Run external script (example: shell script / SQL job)
    #try:
     #   subprocess.run(["bash", job, str(process_date)], check=True)
      #  print("✅ Batch executed successfully.")
    #except subprocess.CalledProcessError as e:
     #   print(f"❌ Error running batch: {e}")

def execute_commands_over_ssh(host, username, password, commands, wait_time=5):
    """
    Connect to SSH and execute commands sequentially.
    """
    print(f"🔑 Connecting to {host}...")
    #ssh = paramiko.SSHClient()
    #ssh.set_missing_host_key_policy(paramiko.AutoAddPolicy())

    #ssh.connect(host, username=username, password=password)
    print("✅ Connected.")

    for i, cmd in enumerate(commands, start=1):
        cmd = cmd.strip()
        if not cmd:
            continue

        print(f"\n➡️ Executing command {i}: {cmd}")
       # stdin, stdout, stderr = ssh.exec_command(cmd)

        # Print command output
      #  output = stdout.read().decode()
       # error = stderr.read().decode()
        #if output:
         #   print(f"--- Output ---\n{output}")
        #if error:
         #   print(f"--- Error ---\n{error}")

        # Wait before next command
        time.sleep(wait_time)

    #ssh.close()
    print("🔒 SSH connection closed.")


def main():
    if len(sys.argv) != 4:
        print("Usage: python RunEODBatch.py <AssetClass> <Region> <DD-MM-YYYY>")
        sys.exit(1)

    asset_class = sys.argv[1]
    region = sys.argv[2]
    process_date_str = parse_process_date(sys.argv[3])  # -> dd-mm-YYYY
    filePath=get_command_file(asset_class, region, process_date_str)
    if not filePath:
        sys.exit(1)
#pass the process date to replace placeholder in the file of  commands
    listOfCommands = replace_process_date(filePath, process_date_str)
    print(f"Commands to execute:\n{''.join(listOfCommands)}")
    if not listOfCommands:
        print("No commands to execute.")
        sys.exit(1)

    # SSH connection details (example values)
     # Step 2: SSH Details (replace with env vars / secrets in production!)
    host = "unix_server_ip_or_hostname"
    username = "your_username"
    password = "your_password"

    #  Execute commands sequentially
    execute_commands_over_ssh(host, username, password, listOfCommands, wait_time=10)

if __name__ == "__main__":
    main()
