![](https://raw.githubusercontent.com/SergeyMi37/appmsw-dbdeploy/master/doc/Screenshot_2.png)
## appmsw-dbdeploy

[![license](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

An example of deploying a solution with databases from the archive. (Can be expanded without source codes).

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

### To create database, you need to run:

```
docker-compose exec iris iris session iris
```

 ###You can protect your solution by deleting the source code:
 `USER>do ##class(appmsw.sys.dbdeploy).MakeClassDeployed("apptools.core","PERMISSION")`

 ###Create an archive for database deployment and move it outside the container:
 `USER>do ##class(appmsw.sys.dbdeploy).CreateTGZ("permission","/irisdev/app/db-tgz/")`
 
 ###In a new instance, you can create a database from the archive with a new name.:
 `USER>do ##class(appmsw.sys.dbdeploy).CreateDbFromTgz("permission","newpermiss")`
  

![](https://raw.githubusercontent.com/SergeyMi37/appmsw-dbdeploy/master/doc/Screenshot_3.png)
