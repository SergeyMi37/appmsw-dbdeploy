![](https://raw.githubusercontent.com/SergeyMi37/appmsw-dbdeploy/master/doc/Screenshot_4.png)
## appmsw-dbdeploy
[![Gitter](https://img.shields.io/badge/Available%20on-Intersystems%20Open%20Exchange-00b2a9.svg)](https://openexchange.intersystems.com/package/appmsw-dbdeploy)
[![DC](https://img.shields.io/badge/Available%20article%20on-Intersystems%20Community-orange)](https://community.intersystems.com/post/deploying-solutions-without-source-code-zpm)
[![GitHub all releases](https://img.shields.io/badge/Available%20on-GitHub-black)](https://github.com/SergeyMi37/appmsw-dbdeploy)
[![license](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

 [![Gitter](https://img.shields.io/badge/Available%20on-Intersystems%20Open%20Exchange-00b2a9.svg)](https://openexchange.intersystems.com/package/appmsw-dbdeploy)
 [![Quality Gate Status](https://community.objectscriptquality.com/api/project_badges/measure?project=intersystems_iris_community%2Fappmsw-dbdeploy&metric=alert_status)](https://community.objectscriptquality.com/dashboard?id=intersystems_iris_community%2Fappmsw-dbdeploy)
 [![Reliability Rating](https://community.objectscriptquality.com/api/project_badges/measure?project=intersystems_iris_community%2Fappmsw-dbdeploy&metric=reliability_rating)](https://community.objectscriptquality.com/dashboard?id=intersystems_iris_community%2Fappmsw-dbdeploy)

 
<img alt="GitHub last commit" src="https://img.shields.io/github/last-commit/SergeyMi37/appmsw-dbdeploy">

You can protect your solution by supplying it to the customer without source code.
An example of creating a database archives without source code for deploying a solution from a repository with a package manager ZPM.

## Installation with ZPM

If ZPM the current instance is not installed, then in one line you can install the latest version of ZPM.
```
set $namespace="%SYS", name="DefaultSSL" do:'##class(Security.SSLConfigs).Exists(name) ##class(Security.SSLConfigs).Create(name) set url="https://pm.community.intersystems.com/packages/zpm/latest/installer" Do ##class(%Net.URLParser).Parse(url,.comp) set ht = ##class(%Net.HttpRequest).%New(), ht.Server = comp("host"), ht.Port = 443, ht.Https=1, ht.SSLConfiguration=name, st=ht.Get(comp("path")) quit:'st $System.Status.GetErrorText(st) set xml=##class(%File).TempFilename("xml"), tFile = ##class(%Stream.FileBinary).%New(), tFile.Filename = xml do tFile.CopyFromAndSave(ht.HttpResponse.Data) do ht.%Close(), $system.OBJ.Load(xml,"ck") do ##class(%File).Delete(xml)
```
If ZPM is installed, then `appmsw-dbdeploy` can be set with the command
```
zpm:USER>install appmsw-dbdeploy
```
## Installation with Docker

## Prerequisites
Make sure you have [git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git) and [Docker desktop](https://www.docker.com/products/docker-desktop) installed.

## Installation 
Clone/git pull the repo into any local directory

```
git clone https://github.com/SergeyMi37/appmsw-dbdeploy.git
```

Open the terminal in this directory and run:

```
docker-compose build
```

3. Run the IRIS container with your project:

```
docker-compose up -d
```

### First you need to update the ZPM version to the latest 0.3.2
```
docker-compose exec iris iris session iris
USER>zn "%SYS"
%SYS>zpm "install zpm"
%SYS>zpm "ver"
zpm 0.3.2
```
### To create database, you need to run:
```
USER>do ##class(appmsw.sys.dbdeploy).CreateDBNS("LOCKDOWN")
USER>zn "LOCKDOWN"
LOCKDOWN>zpm "install isc-apptools-lockdown"
```

 ### You can protect your solution by deleting the source code:
```
 USER>do ##class(appmsw.sys.dbdeploy).MakeClassDeployed("appmsw.security","LOCKDOWN")
 appmsw.security.lockdown deployed
```

 ### Create an archive for database deployment and move it outside the container:
```
 USER>do ##class(appmsw.sys.dbdeploy).CreateTGZ("lockdown","/irisdev/app/db-tgz/")
 ...
 Create TarGZ /irisdev/app/db-tgz/lockdown.tgz
 
 USER>do ##class(appmsw.sys.dbdeploy).CreateTGZ("lockdown","/irisdev/app/db-tgz/",1) ;,1= including the version in the archive name
 ...
 Create TarGZ /irisdev/app/db-tgz/lockdown=2021-1-(Build-215-3U).tgz
```
 
 ### Create your project based on appmsw-dbdeploy by adding your new archives files to it and deploying new databases. The archives will contain resources that can be implemented as independent modules ZPM:
```
USER>do ##class(appmsw.sys.dbdeploy).CreateDbFromTgz("lockdown","newlock")
```

