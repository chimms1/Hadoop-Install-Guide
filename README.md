<!-- Improved compatibility of back to top link: See: https://github.com/othneildrew/Best-README-Template/pull/73 -->
<a name="readme-top"></a>
<!--
*** Thanks for checking out the Best-README-Template. If you have a suggestion
*** that would make this better, please fork the repo and create a pull request
*** or simply open an issue with the tag "enhancement".
*** Don't forget to give the project a star!
*** Thanks again! Now go create something AMAZING! :D
-->



[![MIT License][license-shield]][license-url]
[![Contributors][contributors-shield]][contributors-url]
<!-- [![Forks][forks-shield]][forks-url] -->
[![Stargazers][stars-shield]][stars-url]

[![LinkedIn][linkedin-shield]][linkedin-url]




<!-- TABLE OF CONTENTS -->
<details>
  <summary>Table of Contents</summary>
  <ol>
    <li>
      <a href="#about">About The Project</a>
    </li>
    <li>
      <a href="#getting-started">Getting Started</a>
      <ul>
        <li><a href="#warning">WARNING</a></li>
        <li><a href="#prerequisites">Prerequisites</a></li>
      </ul>
    <li><a href="#installation">Installation</a></li>
    <li><a href="#contributing">Contributing</a></li>
    <li><a href="#license">License</a></li>
    <li><a href="#acknowledgments">Acknowledgments</a></li>
  </ol>
</details>



<!-- ABOUT THE PROJECT -->
## About

Straight forward guide to setting up a pseudo-distributed Hadoop installation preferably for educational purposes and not to be used in a production environment due to lack of experience of this guide's writer.
<br><br> The best documentation can be always found at <a href="https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-common/SingleCluster.html">Official Source</a>

<!-- GETTING STARTED -->
## Getting Started

### WARNING
* Only YOU shall be responsible for any damages that are caused while getting things done. Any contributors to this guide shall not be liable for the careless actions of the user.
* It is recommended to try out this guide in a Virtual Machine first.
* This may contain incorrect commands, insecure methods, hacky fixes, or may satisfy your requirements partially or not at all.<br>
* DO NOT FOLLOW BLINDLY and proceed at your own RISK.
<br><br>
If you find correct mistakes or would like to improve any part see <a href="#contributing"> Contribution</a> section.

### Prerequisites

* Working GNU/Linux installation.
* root privilege.
* Internet connection.
<br>
I shall be using Debian 12 XFCE in a VM installed using VirtualBox. You can use any distribution of your choice (Ubuntu, Linux Mint etc.)

<p align="right">(<a href="#readme-top">back to top</a>)</p>

# Installation

## Installing SSH
To install SSH on Debian-based distributions.
 ```bash
sudo apt install ssh pdsh openssh-server openssh-client  
 ```
## Installing Java
* One way to install java is from official repos of your distribution. <br>
* On Debian 12, OpenJDK and JRE can be installed through the meta-packages. This will install version 17 of the respective packages.

```bash
sudo apt install default-jdk default-jre
```
* To verify installation
```bash
java -version
```
```bash
javac -version
```
<br>
But, as of August 2023, Hadoop version 3.3.6 supports only Java 8 <a href="https://cwiki.apache.org/confluence/display/HADOOP/Hadoop+Java+Versions">(check here)</a><br><br>

![javaversion](https://github.com/chimms1/Hadoop-Install-Guide/assets/109859262/37328f6f-05f5-46fb-ab4b-0d11f9e8f162)

* Now the procedure to install Java 8 will vary depending on your distribution. For example, on Ubuntu (<a href="https://ubuntu.com/tutorials/install-jre#2-installing-openjdk-jre">official guide</a>)
```bash
sudo apt install openjdk-8-jdk openjdk-8-jre
```
will get the job done. <br><br>

But in my case, as I am using Debian 12 (Bookworm), let us check the <a href="https://wiki.debian.org/Java">official docs</a> for Java.

![javadebian](https://github.com/chimms1/Hadoop-Install-Guide/assets/109859262/e421ba41-70bf-407c-90e6-9a66967ac4f3)

It can be seen that the current default version is 17 whereas Java 8 was the default in Debian Stretch.<br>
One way to solve this is to add a backports repository and then install it in Ubuntu way but I could not get that to work.<br><br>
So I settled for a hacky fix for this after referring to this <a href="https://stackoverflow.com/questions/57031649/how-to-install-openjdk-8-jdk-on-debian-10-buster">stackoverflow answer</a><br>

```bash
Go to the Eclipse Termurin Project website and choose OS type, Architecture, and Java version
```
* <a href="https://adoptium.net/temurin/releases/?version=8&os=linux&arch=x64">https://adoptium.net/temurin/releases/?version=8&os=linux&arch=x64</a> and download both JDK and JRE.<br>
![javaadoptium](https://github.com/chimms1/Hadoop-Install-Guide/assets/109859262/47eeecf0-6917-4635-b8bc-757a082fff6a)<br>
* Unpack the files to get folders, rename them to something sane, and then copy them to the desired location as these will act as JDK installation for us. Execute command for both jdk and jre folders.
```bash
sudo cp <folder-name> /usr/lib/jvm/
```
* To find jdk folder, do ``` ls /usr/lib/jvm/ ```
* The folder present here, either named by you or named something like java-1.17.0-openjdk-amd64 is your jdk folder.
* Open .bashrc using nano or gedit
```bash
nano ~/.bashrc
```
* Add the following at the end of .bashrc (we will be doing this process once again later.)
* __ENTER CORRECT FOLDER NAME__.
```bash
export JAVA_HOME=/usr/lib/jvm/<myopenjdk-foldername>
export PATH=$PATH:$JAVA_HOME/bin
```
* reopen terminal or ``` source ~/.bashrc ```
With this Java setup is complete.

<p align="right">(<a href="#readme-top">back to top</a>)</p>

## Creating a new user.
Generally, we create a separate user to run daemons as a security practice to isolate it from other user data.
Hence we will create another user for Hadoop with no root privileges (or you can add that user to the sudo group if you wish to do so).

* From *main user*
```bash
sudo adduser hduser
```
* (optionally) provide sudo permission to hduser
```bash
sudo adduser hduser sudo
```
* Remember this command to switch to another user
```bash
su - hduser
```
* Verify
```bash
whoami
```
Note that your name in the terminal should be changed to ``` hduser@hostname ```

<p align="right">(<a href="#readme-top">back to top</a>)</p>

## Configure SSH
* We will configure this for passwordless ssh login.<br>
These commands should be executed from the *hadoopuser* which was created earlier.
```bash
ssh-keygen -t rsa -P '' -f ~/.ssh/id_rsa
```
* Copy the public keys from id_rsa.pub to authorized_keys
```bash
cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys
```
* Setting the permissions
```bash
chmod 600 ~/.ssh/authorized_keys
```
It is very important to set appropriate permissions, as these are ssh keys, by using 600 we will allow read and write ability to only the OWNER.
* Now try to connect
```bash
ssh localhost
```
* You may get a prompt asking to add this machine to known hosts. Answering 'yes' will accept the connection request and add localhost to the list of known hosts.<br>
Note that this prompt appears only if there is a change in ssh configuration.<br>

<p align="right">(<a href="#readme-top">back to top</a>)</p>

## Hadoop Setup
* Download hadoop tar file from <a href="https://hadoop.apache.org/releases.html">official website</a>.
* I have downloaded version 3.3.6 which is the latest stable version as of August 2023.
* __Make sure to download the binary (~690 MB) and not the source, *unless you wish to compile hadoop from scratch*__
* Open the file manager and extract the tar file present in the Downloads folder.
* Now it is time to move the hadoop folder to the hduser home directory. Execute commands from *main user*
```bash
sudo mv /home/<main-user>/Downloads/hadoop-3.3.6/ /home/hduser/
```
* Change ownership to hduser.
```bash
chown -R hduser /home/hduser
```
* Rename hadoop folder. Execute command from *hadoop user*
```bash
mv hadoop-3.3.6 hadoop
```
* While in hadoop user home directory. Open .bashrc
```bash
nano ~/.bashrc
```
* Add the environment variables at the end
```bash
export HADOOP_HOME=/home/hduser/hadoop
export HADOOP_INSTALL=$HADOOP_HOME
export HADOOP_MAPRED_HOME=$HADOOP_HOME
export HADOOP_COMMON_HOME=$HADOOP_HOME
export HADOOP_HDFS_HOME=$HADOOP_HOME
export YARN_HOME=$HADOOP_HOME
export HADOOP_COMMON_LIB_NATIVE_DIR=$HADOOP_HOME/lib/native
export PATH=$PATH:$HADOOP_HOME/sbin:$HADOOP_HOME/bin
export HADOOP_OPTS="-Djava.library.path=$HADOOP_HOME/lib/native"
```
* It is also time to add another variable after this. We will add JAVA_HOME to *hadoop user* which we had previously added to *main user*.
* __ENTER CORRECT FOLDER NAME__.
```bash
export JAVA_HOME=/usr/lib/jvm/<myopenjdk-foldername>
export PATH=$PATH:$JAVA_HOME/bin
```
* Now reopen terminal or ```source ~/.bashrc``` . If you reopen the terminal then remember to switch to hadoop user.
* Add JAVA_HOME in *hadoop-env.sh*
```bash
nano $HADOOP_HOME/etc/hadoop/hadoop-env.sh
```
* Scroll and find JAVA_HOME comment in *hadoop-env.sh*, uncomment and (__ENTER CORRECT FOLDER NAME__.) change to:
```bash
export JAVA_HOME=/usr/lib/jvm/<myopenjdk-foldername>
```
* Now we will add content in the configuration files of Hadoop. <a href="https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-common/SingleCluster.html">(Official Docs)</a>
* Open core-site.xml to make changes in-between configuration.
```bash
nano $HADOOP_HOME/etc/hadoop/core-site.xml
```
```xml
<configuration>
<property>
  <name>fs.default.name</name>
  <value>hdfs://localhost:9000</value>
</property>
</configuration>
```

* Open hdfs-site.xml. namenode and datanode will be created in /home/hduser/hadoopdata
```bash
nano $HADOOP_HOME/etc/hadoop/hdfs-site.xml
```
```xml
<configuration>
<property>
 <name>dfs.replication</name>
 <value>1</value>
</property>

<property>
  <name>dfs.name.dir</name>
  <value>file:///home/hduser/hadoopdata/hdfs/namenode</value>
</property>

<property>
  <name>dfs.data.dir</name>
  <value>file:///home/hduser/hadoopdata/hdfs/datanode</value>
</property>
</configuration>
```

* Add the following configuration to change the default MapReduce framework name value to yarn:
```bash
nano $HADOOP_HOME/etc/hadoop/mapred-site.xml
```
```xml
<configuration>
 <property>
  <name>mapreduce.framework.name</name>
  <value>yarn</value>
 </property>
<property>
  <name>yarn.app.mapreduce.am.env</name>
  <value>HADOOP_MAPRED_HOME=${HADOOP_HOME}</value>
</property>
<property>
  <name>mapreduce.map.env</name>
  <value>HADOOP_MAPRED_HOME=${HADOOP_HOME}</value>
</property>
<property>
  <name>mapreduce.reduce.env</name>
  <value>HADOOP_MAPRED_HOME=${HADOOP_HOME}</value>
</property>
</configuration>
```

* Adding configurations for the Node Manager, Resource Manager, Containers, and Application Master.
```bash
nano $HADOOP_HOME/etc/hadoop/yarn-site.xml
```
```xml
<configuration>
<property>
  <name>yarn.nodemanager.aux-services</name>
  <value>mapreduce_shuffle</value>
</property>
<property>
  <name>yarn.nodemanager.aux-services.mapreduce.shuffle.class</name>
  <value>org.apache.hadoop.mapred.ShuffleHandler</value>
</property>
<property>
  <name>yarn.resourcemanager.resource-tracker.address</name>
  <value>localhost:8025</value>
</property>
<property>
  <name>yarn.resourcemanager.scheduler.address</name>
  <value>localhost:8030</value>
</property>
<property>
  <name>yarn.resourcemanager.address</name>
  <value>localhost:8050</value>
</property>
</configuration>
```

* Now it is time to format the NameNode. It is necessary to format NameNode every time you change configuration files. <br>
However, if you change only the *hadoop-env.sh*, then it might not be required to reformat.
* The following command will create namenode and datanode in /home/hduser/hadoopdata.
```bash
cd ~/hadoop/sbin
hdfs namenode -format
```
* While testing setup if you get any errors with datanode after changing any config file, You can delete /hadoopdata folder (losing the data) and reformat.

<p align="right">(<a href="#readme-top">back to top</a>)</p>

## Start Hadoop Cluster
* Navigate to ```~/hadoop/sbin``` . Execute commands from *hadoop user*.
```bash
./start-dfs.sh
```
```bash
./start-yarn.sh
```
#### Error
If you get an error saying connection refused or permission denied then as explained in this <a href="https://unix.stackexchange.com/questions/76722/pdsh-command-failed-with-connection-refused">answer</a>:
* Add ```export PDSH_RCMD_TYPE=ssh``` in .bashrc file, reopen or source and try once again. If you reopen terminal then remember to switch to *hadoop user*.

* Check the processes started with the *jps* command
```
jps
```

![jps](https://github.com/chimms1/Hadoop-Install-Guide/assets/109859262/b655aba9-901d-4bc9-bd84-f57096b1da90)


* To stop the processes use:
```bash
./stop-dfs.sh
```
```bash
./stop-yarn.sh
```
or
```bash
./stop-all.sh
```

<p align="right">(<a href="#readme-top">back to top</a>)</p>

## Accessing Hadoop frontends
* Hadoop NameNode: [http://localhost:9870/](http://localhost:9870/)
* Cluster Information: [http://localhost:8042/](http://localhost:8042/)
* Node Information: [http://localhost:9864/](http://localhost:9864/)

<!-- CONTRIBUTING -->

## Contributing

Contributions are what makes the open-source community such an amazing place to learn, inspire, and create. Any contributions you make are **greatly appreciated**.

If you have a suggestion that would make this better, please fork the repo and create a pull request.
Don't forget to give the project a star! Thanks again!

1. Fork the Project
2. Create your Feature Branch
3. Commit your Changes
4. Push to the Branch
5. Open a Pull Request

<p align="right">(<a href="#readme-top">back to top</a>)</p>


<!-- LICENSE -->
## License

Distributed under the MIT License. See `LICENSE.txt` for more information.

<!-- <p align="right">(<a href="#readme-top">back to top</a>)</p> -->


<!-- ACKNOWLEDGMENTS -->
## Acknowledgments

Useful Resources

* [Official Documentation](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-common/SingleCluster.html)
* [Guide by Bharat Choudhary](https://medium.com/thecyphy/hadoop-installation-in-linux-457f91cb1a80)
* [The readme that you are currently reading](https://github.com/othneildrew/Best-README-Template)
* [Img Shields](https://shields.io)

<p align="right">(<a href="#readme-top">back to top</a>)</p>



<!-- MARKDOWN LINKS & IMAGES -->
<!-- https://www.markdownguide.org/basic-syntax/#reference-style-links -->
[contributors-shield]: https://img.shields.io/github/contributors/chimms1/Hadoop-Install-Guide.svg?style=for-the-badge
[contributors-url]: https://github.com/chimms1/Hadoop-Install-Guide/graphs/contributors
<!-- [forks-shield]: https://img.shields.io/github/forks/othneildrew/Best-README-Template.svg?style=for-the-badge
[forks-url]: https://github.com/othneildrew/Best-README-Template/network/members -->
[stars-shield]: https://img.shields.io/github/stars/chimms1/Hadoop-Install-Guide.svg?style=for-the-badge
[stars-url]: https://github.com/chimms1/Hadoop-Install-Guide/stargazers

[license-shield]: https://img.shields.io/github/license/chimms1/Hadoop-Install-Guide.svg?style=for-the-badge
[license-url]: https://github.com/chimms1/Hadoop-Install-Guide/blob/main/LICENSE.txt
[linkedin-shield]: https://img.shields.io/badge/-LinkedIn-black.svg?style=for-the-badge&logo=linkedin&colorB=555
[linkedin-url]: https://in.linkedin.com/in/yash-deshpande-410567270



[python.com]: https://img.shields.io/badge/python-3670A0?style=for-the-badge&logo=python&logoColor=ffdd54
[python-url]: https://www.python.org/
