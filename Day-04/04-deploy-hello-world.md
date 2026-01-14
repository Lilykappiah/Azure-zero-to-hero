# Deploy Your First Application on Azure VM - Hello World

This guide will walk you through deploying a simple "Hello World" web application on an Azure Virtual Machine.

## Prerequisites

- An Azure account
- An Azure Virtual Machine (Ubuntu/Linux) created and running
- SSH access to your VM

## Step 1: Connect to Your Azure VM

Connect to your VM using SSH:

```bash
ssh adminuser@<your-vm-public-ip>
```

Replace `<your-vm-public-ip>` with your actual VM's public IP address.

## Step 2: Update System Packages

Once connected, update your system packages:

```bash
sudo apt update
sudo apt upgrade -y
```

## Step 3: Install Python and Pip

Install Python 3 and pip if not already installed:

```bash
sudo apt install python3 python3-pip -y
```

## Step 4: Create Application Directory

Create a directory for your application:

```bash
mkdir ~/hello-world-app
cd ~/hello-world-app
```

## Step 5: Create the Application Files

Create the Python application file:

```bash
nano app.py
```

Copy the following code into `app.py`:

```python
#!/usr/bin/env python3
from flask import Flask, jsonify

app = Flask(__name__)

@app.route('/')
def hello_world():
    return '''
    <!DOCTYPE html>
    <html lang="en">
    <head>
        <meta charset="UTF-8">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>Hello World - Azure</title>
        <style>
            body {
                font-family: Arial, sans-serif;
                display: flex;
                justify-content: center;
                align-items: center;
                height: 100vh;
                margin: 0;
                background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            }
            .container {
                text-align: center;
                background: white;
                padding: 50px;
                border-radius: 10px;
                box-shadow: 0 10px 25px rgba(0,0,0,0.2);
            }
            h1 {
                color: #333;
                margin-bottom: 20px;
            }
            p {
                color: #666;
                font-size: 18px;
            }
        </style>
    </head>
    <body>
        <div class="container">
            <h1>Hello World!</h1>
            <p>Welcome to your first application on Azure VM</p>
            <p>🎉 Congratulations on deploying to Azure! 🎉</p>
        </div>
    </body>
    </html>
    '''

@app.route('/health')
def health():
    return jsonify({'status': 'healthy'}), 200

if __name__ == '__main__':
    app.run(host='0.0.0.0', port=5000, debug=False)
```

Save and exit (Ctrl+X, then Y, then Enter).

Create a requirements file:

```bash
nano requirements.txt
```

Add the following content:

```
Flask==3.0.0
Werkzeug==3.0.1
```

Save and exit.

## Step 6: Install Dependencies

Install the required Python packages:

```bash
pip3 install -r requirements.txt
```

## Step 7: Configure Firewall Rules

Allow traffic on port 5000:

```bash
sudo ufw allow 5000
sudo ufw enable
```

**Important:** Also configure the Network Security Group (NSG) in Azure Portal to allow inbound traffic on port 5000.

### Azure Portal Steps:
1. Go to your VM in Azure Portal
2. Navigate to "Networking" under Settings
3. Click "Add inbound port rule"
4. Set the following:
   - Source: Any
   - Source port ranges: *
   - Destination: Any
   - Service: Custom
   - Destination port ranges: 5000
   - Protocol: TCP
   - Action: Allow
   - Priority: 1000
   - Name: AllowPort5000
5. Click "Add"

## Step 8: Run the Application

Start your Hello World application:

```bash
python3 app.py
```

You should see output similar to:
```
 * Running on all addresses (0.0.0.0)
 * Running on http://127.0.0.1:5000
 * Running on http://<private-ip>:5000
```

## Step 9: Access Your Application

Open a web browser and navigate to:

```
http://<your-vm-public-ip>:5000
```

You should see your "Hello World" application with a beautiful gradient background!

## Step 10: Run Application in Background (Optional)

To keep the application running after you disconnect from SSH, use `nohup`:

```bash
nohup python3 app.py > app.log 2>&1 &
```

To stop the application later:

```bash
pkill -f app.py
```

## Troubleshooting

### Application not accessible from browser?

1. Check if the application is running: `ps aux | grep app.py`
2. Verify NSG rules in Azure Portal allow port 5000
3. Check VM firewall: `sudo ufw status`
4. Verify application is listening: `netstat -tuln | grep 5000`

### Connection refused?

- Make sure the application is running with `host='0.0.0.0'` not `localhost`
- Verify your VM's public IP address is correct
- Check both Azure NSG and VM firewall rules

## Next Steps

Now that you've successfully deployed your first application:
- Try modifying the HTML to customize the message
- Add more routes to your Flask application
- Learn about deploying with systemd for production
- Explore using Nginx as a reverse proxy

Congratulations on deploying your first application on Azure! 🎉
