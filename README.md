# decentralizedSubnetRelayNode
A Nostr-based Decentralized Subnet Relay Node

Sure, here's a section you can add to your `README.md` that explains how to set up the `.vscode/settings.json` for your project:

---

## VSCode Configuration

To streamline your development process in Visual Studio Code (VSCode), you can set up a workspace configuration that ensures your Python virtual environment is used automatically. Follow these steps to configure VSCode for your MeshNet project:

### Step 1: Create `.vscode` Directory

1. **Create the `.vscode` Directory:**
   - Navigate to your project root directory and create a `.vscode` folder if it doesn't already exist.

   ```bash
   mkdir -p .vscode
   ```

### Step 2: Create and Configure `settings.json`

1. **Create `settings.json` File:**
   - Inside the `.vscode` directory, create a file named `settings.json`.

   ```bash
   touch .vscode/settings.json
   ```

2. **Edit `settings.json` File:**
   - Open `settings.json` in a text editor (e.g., VSCode).

   ```bash
   code .vscode/settings.json
   ```

3. **Add Configuration Settings:**
   - Add the following configuration settings to `settings.json`:

   ```json
   {
       "python.pythonPath": "${workspaceFolder}/venv/bin/python",
       "python.venvPath": "${workspaceFolder}/venv",
       "terminal.integrated.env.linux": {
           "PATH": "${workspaceFolder}/venv/bin:${env:PATH}"
       },
       "terminal.integrated.env.windows": {
           "PATH": "${workspaceFolder}\\venv\\Scripts;${env:PATH}"
       },
       "terminal.integrated.env.osx": {
           "PATH": "${workspaceFolder}/venv/bin:${env:PATH}"
       }
   }
   ```

### Explanation of Settings

- **`python.pythonPath`**: Specifies the path to the Python interpreter in your virtual environment. This ensures that VSCode uses the correct Python interpreter for your project.

- **`python.venvPath`**: Specifies the path to your virtual environment. This helps VSCode recognize and use your virtual environment correctly.

- **`terminal.integrated.env.linux`**: Configures the PATH environment variable for the integrated terminal in VSCode on Linux. This ensures that the terminal uses the Python interpreter from your virtual environment.

- **`terminal.integrated.env.windows`**: Configures the PATH environment variable for the integrated terminal in VSCode on Windows. This ensures that the terminal uses the Python interpreter from your virtual environment.

- **`terminal.integrated.env.osx`**: Configures the PATH environment variable for the integrated terminal in VSCode on macOS. This ensures that the terminal uses the Python interpreter from your virtual environment.

### Summary

By setting up the `.vscode/settings.json` file, you ensure that VSCode is properly configured to use your project's virtual environment. This setup simplifies your development workflow by making sure that the correct Python interpreter and environment settings are used automatically.

---

This section will help your team members set up their VSCode environments quickly and consistently, ensuring a smoother development experience for your MeshNet project.

# MeshNet Project

This project sets up a decentralized MeshNet environment using Docker, Python, and Ansible. It includes the setup for Nginx, Docker, and a Nostr relay node for communication.

## Prerequisites

- [Docker Desktop](https://www.docker.com/products/docker-desktop) installed and running
- [Python 3.12](https://www.python.org/downloads/) installed
- [Ansible](https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html) installed
- [Homebrew](https://brew.sh/) installed (for macOS)

## Setup Instructions

### Step 1: Clone the Repository

```bash
git clone https://github.com/yourusername/decentralizedSubnetRelayNode.git
cd decentralizedSubnetRelayNode
```

### Step 2: Set Up Python Virtual Environment

```bash
python3 -m venv venv
source venv/bin/activate
pip install -r requirements.txt
```

### Step 3: Install Ansible Collections

```bash
ansible-galaxy collection install community.general
ansible-galaxy collection install community.docker
```

### Step 4: Run Ansible Playbook

```bash
ansible-playbook -i hosts playbook.yml --ask-become-pass
```

### Step 5: Verify Nginx and Docker

1. **Verify Docker Container is Running:**

    ```bash
    docker ps
    ```

    You should see the Nginx container running.

2. **Access Nginx:**

    Open a web browser and navigate to `http://localhost` to see the Nginx welcome page.

3. **Check Nginx Configuration:**

    ```bash
    sudo nginx -t
    sudo nginx -s reload
    ```

### Step 6: Set Up Nostr Relay Node

1. **Create Dockerfile for Nostr Relay:**

    ```Dockerfile
    # Use an official Python runtime as a parent image
    FROM python:3.12-slim

    # Set the working directory in the container
    WORKDIR /app

    # Copy the current directory contents into the container at /app
    COPY . /app

    # Install any needed packages specified in requirements.txt
    RUN pip install --no-cache-dir -r requirements.txt

    # Make port 8080 available to the world outside this container
    EXPOSE 8080

    # Define environment variable
    ENV NAME World

    # Run app.py when the container launches
    CMD ["python", "relay.py"]
    ```

2. **Create `requirements.txt` for Python Dependencies:**

    ```txt
    nostr
    ```

3. **Create `relay.py` for Nostr Relay Server:**

    ```python
    from nostr.relay import Relay

    relay = Relay(host='0.0.0.0', port=8080)
    relay.start()
    ```

4. **Build and Run the Docker Container:**

    ```bash
    docker build -t nostr-relay .
    docker run -d -p 8080:8080 nostr-relay
    ```

### Step 7: Set Up Nostr Client Node

1. **Create `client.py` for Nostr Client:**

    ```python
    from nostr.client import Client
    from nostr.event import Event
    from nostr.key import PrivateKey

    # Connect to the relay
    client = Client("ws://localhost:8080")

    # Generate a private key for signing events
    key = PrivateKey()

    # Create an event
    event = Event(kind=1, content="Hello, MeshNet!", pubkey=key.pubkey)
    event.sign(key.privkey)

    # Publish the event
    client.publish(event)
    ```

2. **Run the Client:**

    ```bash
    python client.py
    ```

### Project Structure

```
MeshNetProject/
├── .vscode/
│   └── settings.json
├── venv/
│   └── (virtual environment files)
├── hosts                # Inventory file for Ansible
├── playbook.yml         # Ansible playbook
├── Dockerfile           # Dockerfile for Nostr relay
├── requirements.txt     # Python dependencies
├── relay.py             # Nostr relay server script
└── client.py            # Nostr client script
```

### Additional Steps

1. **Customize Nginx Configuration:**
   - Edit the Nginx configuration file inside the container and reload Nginx.

    ```bash
    docker exec -it my_nginx /bin/bash
    nano /etc/nginx/nginx.conf
    docker restart my_nginx
    ```

2. **Extend Ansible Playbook:**
   - Add more tasks to automate additional services or applications.

    ```yaml
    - name: Install MySQL on macOS
      community.general.homebrew:
        name: mysql
        state: present
      when: ansible_os_family == "Darwin"

    - name: Start MySQL service on macOS
      shell: "brew services start mysql"
      when: ansible_os_family == "Darwin"
    ```

### Summary

By following these instructions, you will set up a basic MeshNet environment with Nginx, Docker, and a Nostr relay node. This setup provides a foundation for building and scaling your MeshNet.

For further development, consider:
- Adding more relay nodes for better network resilience.
- Implementing security measures such as encryption and authentication.
- Integrating monitoring tools to maintain and manage your MeshNet.

Happy coding!
