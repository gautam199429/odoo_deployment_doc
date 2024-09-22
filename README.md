Odoo Production Deployment Document
This document outlines the steps necessary to deploy an Odoo instance in a production environment.

Prerequisites
Ensure you have root access to the server.
Install necessary packages (e.g., Python, pip).
Steps to Deploy Odoo
1. Copy Odoo Community Code
Copy the Odoo community code to a specific location on your server.

2. Install Python Dependencies
Run the following command to install all Python dependencies:

bash
Copy code
pip install -r requirements.txt
3. Create a Service for the Odoo Instance
Follow these steps to create a separate service:

a. Navigate to the systemd directory:

bash
Copy code
cd /etc/systemd/system
b. Create a service file:

bash
Copy code
nano <yourodooinstancename>.service
c. Paste the following content into the service file:

ini
Copy code
[Unit]
Description=Odoo <yourodooinstancename> Service
After=postgresql.service

[Service]
User=postgres
ExecStart=<odoo-folder-location>/odoo-bin -c /etc/odoo/odoo.conf
Restart=always

[Install]
WantedBy=multi-user.target
d. Change file permissions:

bash
Copy code
sudo chmod 755 <yourodooinstancename>.service
e. Change file owner:

bash
Copy code
sudo chown root: <yourodooinstancename>.service
f. Update the Odoo configuration file:

Go to <odoo-folder-location>/Debian/odoo.conf and set the logfile key:
ini
Copy code
logfile = /var/log/<yourodooinstancename>/odoo.log
g. Create a log directory:

bash
Copy code
mkdir -p /var/log/<yourodooinstancename>
h. Navigate to the log directory and create log files:

bash
Copy code
cd /var/log/<yourodooinstancename>
touch odoo.log odoo_error.log
i. Change permissions of the log files:

bash
Copy code
sudo chmod 640 odoo.log odoo_error.log
j. Change the owner of the log files:

bash
Copy code
sudo chown postgres: odoo.log odoo_error.log
k. Reload the systemd configuration:

bash
Copy code
sudo systemctl daemon-reload
l. Start the Odoo service:

bash
Copy code
sudo systemctl start <yourodooinstancename>.service
m. Check the service status:

bash
Copy code
sudo systemctl status <yourodooinstancename>.service
If running, you will see a status indicating it's active.
n. If the service is not running, check the error log:

bash
Copy code
cat /var/log/<yourodooinstancename>/odoo_error.log
o. To stop the service, run:

bash
Copy code
sudo systemctl stop <yourodooinstancename>.service
p. Enable the service to start on boot:

bash
Copy code
sudo systemctl enable <yourodooinstancename>.service
q. Implement log rotation for the logs.

Odoo Log Rotation Steps
Navigate to the logrotate directory:
bash
Copy code
cd /etc/logrotate.d
Create a log rotation file:
bash
Copy code
nano <yourodooinstancename>
Paste the following code into the file:
bash
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
        systemctl reload <yourodooinstancename>.service > /dev/null 2>&1 || true
    endscript
}
Test the log rotation configuration:
bash
Copy code
sudo logrotate -d <yourodooinstancename>
Force the log rotation for testing:
bash
Copy code
sudo logrotate -f <yourodooinstancename>
Conclusion
You have successfully deployed and configured your Odoo instance in production. Make sure to monitor the logs for any issues and adjust the configuration as needed.
