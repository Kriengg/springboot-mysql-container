# springboot-mysql-container
#Sample mysql curd project using mysql and java on same host (Ec2 amaozn linux 2 micro)
#!/bin/bash
#Update packages and own opt
sudo yum update -y
sudo chown ec2-user:ec2-user -R /opt
#Install java; corretto has compatibility issues with mvn; use openjdk

sudo yum install java-1.8.0-openjdk-devel -y

export JAVA_HOME=/usr/lib/jvm/java-1.8.0-openjdk
export PATH=$PATH:$JAVA_HOME/bin

#Install maven to compile the java code
#Go here to pick the latest maven version https://maven.apache.org/download.cgi
MVN_VERSION='3.9.4'
wget https://dlcdn.apache.org/maven/maven-3/$MVN_VERSION/binaries/apache-maven-$MVN_VERSION-bin.tar.gz -O apache-maven.tar.gz
tar xvf apache-maven.tar.gz -C /opt
sudo ln -s /opt/apache-maven-$MVN_VERSION /opt/maven
#To access the mvn command systemwide, you need to either set the M2_HOME environment variable or add /opt/maven to the system PATH
echo 'M2_HOME=/opt/maven' | sudo tee -a /etc/profile.d/maven.sh
echo 'export PATH=${M2_HOME}/bin:${PATH}' | sudo tee -a /etc/profile.d/maven.sh
sudo chmod +x /etc/profile.d/maven.sh
source /etc/profile.d/maven.sh
#mvn -version

#Install docker
sudo amazon-linux-extras install docker -y
sudo service docker start
#sudo docker -v
#Get the source code and unzip in opt and build the WAR
sudo usermod -a -G docker ec2-user
#exit and ssh again

mkdir /opt/bezkoder && cd $_


#Sample project for reference
wget https://github.com/bezkoder/docker-compose-spring-boot-mysql/archive/refs/heads/master.zip
unzip master.zip
rm master.zip
mvn package

#docker compose install on amazon linux 

# to get the latest version
sudo curl -L https://github.com/docker/compose/releases/latest/download/docker-compose-$(uname -s)-$(uname -m) -o /usr/local/bin/docker-compose

#Fix permissions after download:

sudo chmod +x /usr/local/bin/docker-compose

#Verify success:

docker-compose version

cd /opt/bezkoder/docker-compose-spring-boot-mysql-master

docker-compose up

http://publicIP:6868/api/tutorials

docker-compose down

If you need to stop and remove all containers, networks, and all images used by any service in docker-compose.yml file, use the command:
docker-compose down --rmi all

