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

<!-- [![LinkedIn][linkedin-shield]][linkedin-url] -->




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

Straight forward guide to setup a pseudo-distributed hadoop installation preferably for educational purposes and not to be used in a production environment due to lack of experience of this guide's writer.
<br><br> The best documentation can be always found at <a href="https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-common/SingleCluster.html">Official Source</a>

<!-- GETTING STARTED -->
## Getting Started

### WARNING
* Only YOU shall be responsible for any damages that are caused while getting things done. Any contributers of this guide shall not be liable for careless actions of user.<br>
* This may contain incorrect commands, insecure methods, hacky fixes,or may satisy your requirements partially or not at all.<br>
* DO NOT FOLLOW BLINDLY and proceed at your own RISK.
<br><br>
If you find correct mistakes or would like to improve any part see <a href="#contributing"> Contribution</a> section.

### Prerequisites

* Working GNU/Linux installation.
* root privilege.
* Internet connection.
<br>
I shall be using Debian 12 XFCE in VM installed using VirtualBox.

<p align="right">(<a href="#readme-top">back to top</a>)</p>

# Installation

## Installing SSH
To install SSH on Debian-based distributions.
 ```bash
sudo apt install ssh pdsh openssh-server openssh-client  
 ```
## Installing Java
* One way to install java is from official repos of your distribution. <br>
* On Debian 12, OpenJDK and JRE can be installed through the meta packages. This will install version 17 of respective packages.

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
* Put image here <br><br>

* Now the procedure to install Java 8 will vary depending upon your distribution. For example, on Ubuntu (<a href="https://ubuntu.com/tutorials/install-jre#2-installing-openjdk-jre">official guide</a>)
```bash
sudo apt install openjdk-8-jdk openjdk-8-jre
```
will get the job done. <br><br>

But in my case, as I am using Debian 12 (Bookworm), let us check the <a href="https://wiki.debian.org/Java">official docs</a> for Java.

<br><br>Put image here<br><br>

It can be seen that the current default version is 17 whereas Java 8 was default in Debian Stretch.<br>
One way to solve this is to add backports repository and then install in Ubuntu way but I could not get that to work.<br><br>
So I settled for a hacky fix for this after refering to this <a href="https://stackoverflow.com/questions/57031649/how-to-install-openjdk-8-jdk-on-debian-10-buster">stackoverflow answer</a><br>

```bash
Go to the Eclipse Termurin Project website and choose OS type, Architecture and Java version
```
* <a href="https://adoptium.net/temurin/releases/?version=8&os=linux&arch=x64">https://adoptium.net/temurin/releases/?version=8&os=linux&arch=x64</a> and download both JDK and JRE.
* Unpack the files to get folders, rename to something sane and then copy them to desired location as these will act as JDK installation for us.
```bash
sudo cp <folder-name> /usr/lib/jvm/
```
* Open .bashrc
```bash
nano ~/.bashrc
```
* Add the following at end of .bashrc (we will be doing this process once again later.)
```bash
export JAVA_HOME=/usr/lib/jvm/<myopenjdk>
export PATH=$PATH:$JAVA_HOME/bin
```
* reopen terminal or ``` source ~/.bashrc ```
With this Java setup is complete.

## Creating new user.
Generally we create a separate user to run daemons as security practise to isolate it from other user data.
Hence we will create another user for hadoop with no root privileges (or you can add that user to sudo group of you wish to do so).

* From main user
```bash
sudo adduser hduser
```



<!-- CONTRIBUTING -->
## Contributing

Contributions are what makes the open source community such an amazing place to learn, inspire, and create. Any contributions you make are **greatly appreciated**.

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

* 1
* 2
* 3
* [The readme that you are currently reading](https://github.com/othneildrew/Best-README-Template)
* [Img Shields](https://shields.io)

<p align="right">(<a href="#readme-top">back to top</a>)</p>



<!-- MARKDOWN LINKS & IMAGES -->
<!-- https://www.markdownguide.org/basic-syntax/#reference-style-links -->
[contributors-shield]: https://img.shields.io/github/contributors/Chimms1/LTE-WiFi-Simulator.svg?style=for-the-badge
[contributors-url]: https://github.com/chimms1/LTE-WiFi-Simulator/graphs/contributors
<!-- [forks-shield]: https://img.shields.io/github/forks/othneildrew/Best-README-Template.svg?style=for-the-badge
[forks-url]: https://github.com/othneildrew/Best-README-Template/network/members -->
[stars-shield]: https://img.shields.io/github/stars/Chimms1/LTE-WiFi-Simulator.svg?style=for-the-badge
[stars-url]: https://github.com/chimms1/LTE-WiFi-Simulator/stargazers

[license-shield]: https://img.shields.io/github/license/Chimms1/LTE-WiFi-Simulator.svg?style=for-the-badge
[license-url]: https://github.com/chimms1/LTE-WiFi-Simulator/blob/main/LICENSE.txt
[linkedin-shield]: https://img.shields.io/badge/-LinkedIn-black.svg?style=for-the-badge&logo=linkedin&colorB=555
[linkedin-url]: https://in.linkedin.com/in/yash-deshpande-410567270



[python.com]: https://img.shields.io/badge/python-3670A0?style=for-the-badge&logo=python&logoColor=ffdd54
[python-url]: https://www.python.org/
