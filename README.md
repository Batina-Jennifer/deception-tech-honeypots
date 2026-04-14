# 🍯 Honeypots - Deception Technology 

A walkthrough on the process of deploying honeypots, the most popular forms of deception technologies. <br />
The following are the 3 important parts of this setup: <br/>
 - AWS Ubuntu Server (Server exposed to the internet)
 - Kali Linux Host machine (To connect to honeypot)
 - T-Pot (Honeypot tool)

## 🪛 Tools & Technologies
 - <code> T-Pot <a href="https://github.com/telekom-security/tpotce">(T-Pot Github Repo)</a> </code>
 - <code> Kali Linux </code>
 - <code> AWS EC2 </code>
 - <code> Elastic Stash(ELK), Kibana </code>

 ## ⚙️ Initial Setup
 1. Install a Hypervisor(Hosted Hypervisor for homelab) like VirtualBox, VMWare, etc.
 2. Install the latest Kali Linux disk image and upload it onto the VirtualBox hypervisor. This will be our host machine for connecting to honeypot.
 3. Get the AWS free-tier account and set up an EC2 instance by clicking on "Launch instance". <br />
 The configurations are as follows:
   - Name: abc-honeypot (any name)
   - OS image: Ubuntu
   - Amazon Machine Image(AMI): Ubuntu Server 22.04 (free tier)
   - Instance Type: m7i-flex.large with 8G memory (free tier, 16G is preferred)
   - Keypair: Create a new key pair (important file)
   - Network: Allow SSH from: MyIP
   - Storage: 30GiB recommended
4. Once done configuring the Ubuntu server, launch the instance. <br />

## 💻 The Process Flow
1. Inside Kali Linux, cd into the Downloads folder where the secret key pair file with .pem extension is stored.
2. Change the file permissions to have only read permissions for user, and no permission for group and others(principle of least privilege). <code>chmod 400 (key-file.pem)</code>
3. Remotely connect to the Ubuntu machine via SSH using the public IP of the server. <code>ssh -i (key-file.pem) ubuntu@(server-instance-public-IP)</code>
4. Once inside the Ubuntu machine, escalate privileges. <code>sudo -i</code>
5. With the root access now in place, update the Ubuntu Server to be able to download the lastest packages and install T-Pot. <code>apt update && apt upgrade -y</code>
6. Reboot the machine by running <code>reboot</code>. The connect closes after reboot, SSH again.
7. After reconnecting with the Ubuntu server, cd into "/opt" directory to install optional packages like T-Pot.
8. Once inside the /opt directory, clone T-Pot from Github(refer to the link under "Tools & Technologies") <code>sudo git clone <a href="https://github.com/telekom-security/tpotce">(T-Pot Github Repo)</a></code>
9. Now cd ino the "tpotce/" dir and install the dependencies <code>./install.sh</code>
10. When prompted, choose the install type "h" to install the T-Pot Standard/Hove installation.
11. You will be prompted to provide a web user name and a password which will be used to log into the T-Pot visualizations and dashboards. Keep them safe and available.
12. In the final step, you will be provided with a specific TCP port that you can use to stay connected with the Ubuntu server. Reboot and reconnect on this new TCP port via SSH.
13. First proceed to AWS console, navigate to EC2 --> Security Groups -->Select the recently created security group --> Edit inbound rule --> Add rule --> Custom TCP with port range: (port provided by T-Pot), add MyIP --> Save rules.
14. <code>sudo reboot</code> on Kali and SSH with the newly assigned port. <code>ssh -i (key-file.pem) -p 64295 ubuntu@(server-instance-public-IP)</code>
15. Check the running status of tpot service. <code>systemctl status tpot.service</code>
16. To list the name and status of each container image in Docker, run: <code>docker ps --format "table {{.Name}}\t{{.Status}}"</code>
17. Before connecting to WebUI, find out which port NGINX is at where T-Pot exposes the WebUI <code>docker port nginx</code>. Note down either one of the mentioned TCP ports.
18. Once again edit the inbound rules of our security group on AWS console. Add the custom TCP port of NGINX along with MyIP.
19. For the trap to be successful, expose a bunch of commonly exploitable ports like, HTTP, HTTPS, RDP, SSH, SMB, MYSQL to ANY(0.0.0.0/0) to allow all access. Save the rules.
20. To access the interactive Web UI, open the browser on Kali, and type https://public-IP:nginx-port. Now enter the web username and password when prompted from step:11. Explore the dashboards and insights.

<b>(Be cautious when accessing unknown links/pages. Safely decommision the EC2 instance after use. Beware of the charges you may incur on EC2 instance usage or any other cloud provider charges.)</b>

## 📸 Snapshots & Insights from T-Pot

