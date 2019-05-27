---
title: Настройка MySQL на виртуальной машине Linux в Azure | Документация Майкрософт
description: Узнайте, как установить стек MySQL на виртуальной машине Linux (из семейства ОС Ubuntu или Red Hat) в Azure
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: 153bae7c-897b-46b3-bd86-192a6efb94fa
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/01/2016
ms.author: cynthn
ms.openlocfilehash: 21ad3f9baf4b8e117f881d9a36fc606af04e17a5
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/23/2019
ms.locfileid: "66158437"
---
# <a name="how-to-install-mysql-on-azure"></a>Как установить MySQL в Azure
Из этой статьи вы узнаете, как установить и настроить СУБД MySQL на виртуальной машине Azure под управлением Linux.


> [!NOTE]
> Для выполнения инструкций учебника у вас уже должна быть виртуальная машина Microsoft Azure под управлением Linux. Прежде чем продолжить, создайте и настройте виртуальную машину под управлением Linux с `mysqlnode` в качестве имени виртуальной машины и `azureuser` в качестве пользователя, используя сведения, приведенные в [руководстве по виртуальным машинам Azure под управлением Linux](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
> 
> 

(В качестве порта MySQL используйте порт 3306.)  

В качестве примера в этой статье для установки MySQL5.6 мы будем использовать пакет из репозитория. В настоящее время MySQL5.6 имеет улучшенную производительность по сравнению с MySQL5.5.  Дополнительные сведения см. [здесь](http://www.mysqlperformanceblog.com/2013/02/18/is-mysql-5-6-slower-than-mysql-5-5/).

## <a name="install-mysql56-on-ubuntu"></a>Установка MySQL5.6 в Ubuntu
Мы будем использовать виртуальную Машину Linux под управлением Ubuntu.


### <a name="install-mysql"></a>Установка MySQL

Установка MySQL Server 5.6, переключившись в `root` пользователя:

```bash  
sudo su -
```

Установите mysql-server 5.6:

```bash  
apt-get update
apt-get -y install mysql-server-5.6
```

  
Во время установки вы увидите диалоговое окно отображаться вам необходимо задать пароль пользователя root MySQL ниже, и здесь вам установить пароль.
  
![image](./media/mysql-install/virtual-machines-linux-install-mysql-p1.png)

Для подтверждения введите пароль еще раз.

![image](./media/mysql-install/virtual-machines-linux-install-mysql-p2.png)

### <a name="sign-in"></a>Вход
  
После завершения установки MySQL Server служба MySQL запустится автоматически. Вы можете войти сервер MySQL с помощью `root` пользователя, а затем введите пароль.

```bash  
mysql -uroot -p
```


### <a name="manage-the-mysql-service"></a>Управление службой MySQL

Получение сведений о состоянии службы MySQL

```bash   
service mysql status
```
  
Запустить службу MySQL

```bash  
service mysql start
```
  
Остановить службу MySQL

```bash  
service mysql stop
```
  
Перезапустите службу MySQL

```bash  
service mysql restart
```

## <a name="install-mysql-on-red-hat-os-centos-oracle-linux"></a>Установка MySQL на ОС с Red Hat, CentOS, Oracle Linux
Здесь мы будем использовать виртуальную машину Linux с CentOS или Oracle Linux.

### <a name="add-the-mysql-yum-repository"></a>Добавление репозитория MySQL yum
    
Переключиться в режим `root` пользователя:

```bash  
sudo su -
```

Скачайте и установите пакет выпуска MySQL:

```bash  
wget https://repo.mysql.com/mysql-community-release-el6-5.noarch.rpm
yum localinstall -y mysql-community-release-el6-5.noarch.rpm
```

### <a name="enable-the-mysql-repository"></a>Включение репозитория MySQL
Отредактируйте указанный ниже файл, чтобы разрешить скачивание пакета MySQL5.6 в репозиторий MySQL.

```bash  
vim /etc/yum.repos.d/mysql-community.repo
```

  
Обновите каждое значение этого файла на значения, указанные ниже:

```  
\# *Enable to use MySQL 5.6*
  
[mysql56-community]
name=MySQL 5.6 Community Server
  
baseurl=http://repo.mysql.com/yum/mysql-5.6-community/el/6/$basearch/
  
enabled=1
  
gpgcheck=1
  
gpgkey=file:/etc/pki/rpm-gpg/RPM-GPG-KEY-mysql
```

### <a name="install-mysql"></a>Установка MySQL 

Установка MySQL из репозитория.

```bash  
yum install mysql-community-server
```
  
Будут установлены пакет MySQL RPM и все связанные пакеты.


## <a name="manage-the-mysql-service"></a>Управление службой MySQL
  
Проверьте состояние службы сервера MySQL:

```bash  
service mysqld status\
```
  
Проверьте, работает ли по умолчанию порта сервера MySQL:

```bash  
netstat  –tunlp|grep 3306
```

Для запуска сервера MySQL:

```bash
service mysqld start
```

Для остановки сервера MySQL:

```bash
service mysqld stop
```

Установить MySQL, запускаемых при включении системы:

```bash
chkconfig mysqld on
```

## <a name="install-mysql-on-suse-linux"></a>Установка MySQL в SUSE Linux

Здесь мы будем использовать виртуальную машину Linux с OpenSUSE.

### <a name="download-and-install-mysql-server"></a>Скачивание и установка MySQL Server
  
Переключитесь на пользователя `root` с помощью следующей команды:  

```bash  
sudo su -
```
  
Скачайте и установите пакет MySQL:

```bash  
zypper update
zypper install mysql-server mysql-devel mysql
```

### <a name="manage-the-mysql-service"></a>Управление службой MySQL
  
Проверьте состояние сервера MySQL:

```bash  
rcmysql status
```
  
Проверьте ли порт по умолчанию для сервера MySQL:

```bash  
netstat  –tunlp|grep 3306
```

Для запуска сервера MySQL:

```bash
rcmysql start
```

Для остановки сервера MySQL:

```bash
rcmysql stop
```

Установить MySQL, запускаемых при включении системы:

```bash
insserv mysql
```

## <a name="next-step"></a>Дальнейшие действия
Дополнительные сведения см. в разделе [MySQL](https://www.mysql.com/) веб-сайта.

