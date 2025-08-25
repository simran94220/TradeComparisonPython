# ComparisonTrade through python
# VS code IDE


def create_temp_commands(file_path: str, process_date: str) -> str:
    """Create temp file with {{PROCESS_DATE}} replaced"""
    with open(file_path, "r", encoding="utf-8") as f:
        commands = f.readlines()

    updated = [line.replace("{{PROCESS_DATE}}", process_date) for line in commands]

    now_str = dt.datetime.now().strftime("%Y%m%d_%H%M%S")
    base, ext = os.path.splitext(os.path.basename(file_path))
    temp_file = os.path.join(os.path.dirname(file_path), f"{base}_{process_date}_{now_str}{ext}")

    with open(temp_file, "w", encoding="utf-8") as f:
        f.writelines(updated)

    print(f"📝 Created temp commands file: {temp_file}")
    return temp_file

def wait_for_prompt(chan, prompt=PROMPT, timeout=30):
    """Read from channel until prompt string appears or timeout."""
    buffer = ""
    start = time.time()
    while True:
        if chan.recv_ready():
            buffer += chan.recv(4096).decode(errors="replace")
            if prompt in buffer:
                break
        if time.time() - start > timeout:
            raise TimeoutError(f"Prompt '{prompt}' not detected in time.\nBuffer so far:\n{buffer}")
        time.sleep(0.2)
    return buffer

def execute_interactive_over_ssh(host, username, password, file_path):
    """Run commands sequentially in interactive shell, waiting for prompt each time."""
    with open(file_path, "r", encoding="utf-8") as f:
        commands = [c.strip() for c in f if c.strip() and not c.startswith("#")]

    print(f"🔑 Connecting to {host}...")
    ssh = paramiko.SSHClient()
    ssh.set_missing_host_key_policy(paramiko.AutoAddPolicy())
    ssh.connect(host, username=username, password=password)
    print("✅ Connected.")

    chan = ssh.invoke_shell()
    chan.settimeout(5)

    # Step 1: Run dzdo to switch user
    print("\n➡️ Executing: dzdo -H -u dtpusup /bin/bash")
