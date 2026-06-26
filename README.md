# tomcat_script
tomcat installation script

#!/bin/bash

# Update packages
sudo apt update -y

# Install Java 21
sudo apt install -y openjdk-21-jdk wget

# Verify Java installation
java -version

# Set JAVA_HOME
JAVA_HOME=$(dirname $(dirname $(readlink -f $(which java))))
echo "export JAVA_HOME=$JAVA_HOME" | sudo tee -a /etc/profile
echo "export PATH=\$JAVA_HOME/bin:\$PATH" | sudo tee -a /etc/profile

export JAVA_HOME=$JAVA_HOME
export PATH=$JAVA_HOME/bin:$PATH

# Download Tomcat
wget https://dlcdn.apache.org/tomcat/tomcat-9/v9.0.119/bin/apache-tomcat-9.0.119.tar.gz

# Extract
tar -xzf apache-tomcat-9.0.119.tar.gz

# Configure Tomcat users
sed -i '/<\/tomcat-users>/i\
<role rolename="manager-gui"/>\
<role rolename="manager-script"/>\
<user username="tomcat" password="admin@123" roles="manager-gui,manager-script"/>\
' apache-tomcat-9.0.119/conf/tomcat-users.xml

# Remove localhost restriction
sed -i '/RemoteAddrValve/d' apache-tomcat-9.0.119/webapps/manager/META-INF/context.xml

# Start Tomcat
sh apache-tomcat-9.0.119/bin/startup.sh

# Show status
sleep 5
ps -ef | grep tomcat
