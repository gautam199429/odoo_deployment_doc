# Odoo Production Deployment Document

## 1. Odoo Code Setup
1. **Copy the Odoo Community Code**
   - Transfer the Odoo community code to a specific location on the server.

2. **Install Python Dependencies**
   - Run the following command to install all required Python dependencies:
     ```bash
     pip install -r requiremt.txt
     ```

## 2. Create Odoo Service
1. **Navigate to Systemd Directory**
   ```bash
   cd /etc/systemd/system
Create Service File

Use the command to create a service file:
bash
Copy code
nano <yourodooinstancename>.service
Add Service Configuration

Paste the following content into <yourodooinstancename>.service:
ini
Copy code
[Unit]
Description=Odoo Service
After=postgresql.service

[Service]
User=postgres
ExecStart=<odoo-folder-location>/odoo-bin -c <odoo-folder-location>/Debian/odoo.conf
Restart=always

[Install]
WantedBy=multi-user.target
Change File Permissions

bash
Copy code
sudo chmod 755 <yourodooinstancename>.service
Change File Owner

bash
Copy code
sudo chown root: <yourodooinstancename>.service
Update Log Configuration

Open the Odoo configuration file:
bash
Copy code
nano <odoo-folder-location>/Debian/odoo.conf
Find the logfile key and set its value:
bash
Copy code
logfile = /var/log/<yourodooinstancename>/odoo.log
Create Log Folder

bash
Copy code
mkdir -p /var/log/<yourodooinstancename>
Create Log Files

bash
Copy code
cd /var/log/<yourodooinstancename>
touch odoo.log odoo_error.log
Change Log File Permissions

bash
Copy code
sudo chmod 640 odoo.log odoo_error.log
Change Log File Owner

bash
Copy code
sudo chown postgres: odoo.log odoo_error.log
Reload Systemd Daemon

bash
Copy code
sudo systemctl daemon-reload
Start Odoo Service

bash
Copy code
sudo systemctl start <yourodooinstancename>.service
Check Service Status

bash
Copy code
sudo systemctl status <yourodooinstancename>.service
If the service is running, you will see an active response.
Troubleshooting

If the service is not active, check the error log:
bash
Copy code
cat /var/log/<yourodooinstancename>/odoo_error.log
Stop Odoo Service

bash
Copy code
sudo systemctl stop <yourodooinstancename>.service
Enable Service at Boot

bash
Copy code
sudo systemctl enable <yourodooinstancename>.service
3. Implement Log Rotation
Navigate to Logrotate Directory

bash
Copy code
cd /etc/logrotate.d
Create Logrotate File

bash
Copy code
nano <yourodooinstancename>
Add Log Rotation Configuration

Paste the following content into <yourodooinstancename>:
plaintext
Copy code
/var/log/<yourodooinstancename>/*.log {
    daily
    missingok
    rotate 7
    compress
    delaycompress
    notifempty
    create 640 postgres postgres
    sharedscripts
    postrotate
        systemctl reload <yourodooinstancename>.service > /dev/null 2>/dev/null || true
    endscript
}
Test Log Rotation Configuration

Run the command to check the log rotation:
bash
Copy code
sudo logrotate -d /etc/logrotate.d/<yourodooinstancename>
Force Log Rotation Test

bash
Copy code
sudo logrotate -f /etc/logrotate.d/<yourodooinstancename>
Note: Replace <yourodooinstancename> and <odoo-folder-location> with your actual instance name and folder path.
