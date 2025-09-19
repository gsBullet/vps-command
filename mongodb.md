# mongobd install
    sudo apt install software-properties-common gnupg apt-transport-https ca-certificates -y
    curl -fsSL https://pgp.mongodb.com/server-7.0.asc |  sudo gpg -o /usr/share/keyrings/mongodb-server-7.0.gpg --dearmor
    echo "deb [ arch=amd64,arm64 signed-by=/usr/share/keyrings/mongodb-server-7.0.gpg ] https://repo.mongodb.org/apt/ubuntu jammy/mongodb-org/7.0 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-7.0.list
    deb [ arch=amd64,arm64 signed-by=/usr/share/keyrings/mongodb-server-7.0.gpg ] https://repo.mongodb.org/apt/ubuntu jammy/mongodb-org/7.0 multiverse
    sudo apt update
    sudo apt install mongodb-org -y
    mongod --version
   
# Check if MongoDB is running on the server
        sudo systemctl status mongod
# If it’s not running:
    sudo systemctl start mongod
# Bind IP configuration localhost (127.0.0.1).
    sudo nano /etc/mongod.conf

# Look for:
    net:
      port: 27017
      bindIp: 127.0.0.1

# Change it to allow remote connections:
    net:
    port: 27017
    bindIp: 0.0.0.0
# sudo systemctl restart mongod
    sudo systemctl restart mongod

# Open firewall / security group
    sudo ufw allow 27017/tcp
# Test connectivity
    telnet <IP ADDRESS> 27017
# if fails to connect then NEXT
# Verify MongoDB is listening on 27017
      sudo ss -tulnp | grep 27017

# Save and restart:
    sudo systemctl restart mongod
# Open firewall / security group
    sudo ufw allow 27017/tcp
# test again
    telnet 52.63.7.154 27017
# Verify it’s listening on all IPs
    sudo ss -tulnp | grep 27017
<p>
    you should see, and its ok
    
    LISTEN 0 4096 0.0.0.0:27017  0.0.0.0:* users:(("mongod",pid=xxxx,fd=14))
</p>

# Steps to Create an Admin User in MongoDB
        mongosh

# Create a MongoDB user: solve security warning
    use admin
    db.createUser({ user: "myUser", pwd: "myPass", roles:[{role:"root",db:"admin"}] })
# Enable authentication in MongoDB config:
    sudo nano /etc/mongod.conf
# ecurity: add:
    security:
      authorization: enabled
# Connect with
    mongodb://myUser:myPass@52.63.7.154:27017/admin


