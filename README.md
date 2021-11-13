![](https://raw.githubusercontent.com/SergeyMi37/appmsw-docbook/master/doc/Screenshot_1.png)
## appmsw-docbook

[![license](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

An application for installation into your instance of the DoсBook database, which is no longer supplied with IRIS, but sometimes it is very necessary, at least as a local documentation.
An example of deploying a solution with databases from the archive. (Can be expanded without source codes).

## Installation with ZPM

If ZPM the current instance is not installed, then in one line you can install the latest version of ZPM.
```
set $namespace="%SYS", name="DefaultSSL" do:'##class(Security.SSLConfigs).Exists(name) ##class(Security.SSLConfigs).Create(name) set url="https://pm.community.intersystems.com/packages/zpm/latest/installer" Do ##class(%Net.URLParser).Parse(url,.comp) set ht = ##class(%Net.HttpRequest).%New(), ht.Server = comp("host"), ht.Port = 443, ht.Https=1, ht.SSLConfiguration=name, st=ht.Get(comp("path")) quit:'st $System.Status.GetErrorText(st) set xml=##class(%File).TempFilename("xml"), tFile = ##class(%Stream.FileBinary).%New(), tFile.Filename = xml do tFile.CopyFromAndSave(ht.HttpResponse.Data) do ht.%Close(), $system.OBJ.Load(xml,"ck") do ##class(%File).Delete(xml)
```
If ZPM is installed, then `appmsw-docbook` can be set with the command
```
zpm:USER>install appmsw-docbook
```
## Installation with Docker

## Prerequisites
Make sure you have [git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git) and [Docker desktop](https://www.docker.com/products/docker-desktop) installed.

## Installation 
Clone/git pull the repo into any local directory

```
git clone https://github.com/SergeyMi37/appmsw-docbook.git
```

Open the terminal in this directory and run:

```
docker-compose build
```

3. Run the IRIS container with your project:

```
docker-compose up -d
```

### To create DOCBOOK18 database, you need to run:

```
docker-compose exec iris iris session iris


Node: ea68ea560856, Instance: IRIS

USER>do ##class(appmsw.util.database).CreateDbFromZip("docbook18","docbook")

...
Load http://ea68ea560856:52773/csp/docbook18/DocBook.UI.Page.cls


```
### Load documentation:
![](https://raw.githubusercontent.com/SergeyMi37/appmsw-docbook/master/doc/Screenshot_2.png)


### To create SAMPLES2 database, you need to run:

```
...
USER>do ##class(appmsw.util.database).CreateDbFromZip("samples2","samples")

...
Load http://ea68ea560856:52773/csp/samples2/ZENDemo.Home.cls
```
### Load samples:
![](https://raw.githubusercontent.com/SergeyMi37/appmsw-docbook/master/doc/Screenshot_3.png)

