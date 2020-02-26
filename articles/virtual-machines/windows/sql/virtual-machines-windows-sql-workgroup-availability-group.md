---
title: Настройка группы доступности рабочей группы, не зависящей от домена
description: Узнайте, как настроить независимую от домен Active Directory группу доступности Always On на SQL Server виртуальной машине в Azure.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
editor: ''
tags: azure-service-management
ms.assetid: 53981f7e-8370-4979-b26a-93a5988d905f
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/29/2020
ms.author: mathoma
ms.openlocfilehash: 72c04cf5e3e5fbdeac2d267dfc7b2703bd37a1c2
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/11/2020
ms.locfileid: "77122679"
---
# <a name="configure-a-workgroup-availability-group"></a>Настройка группы доступности рабочей группы 

В этой статье описываются шаги, необходимые для создания Active Directory независимого от домена кластера с группой доступности Always On. Это также называется кластером Рабочей группы. В этой статье рассматриваются действия, относящиеся к подготовке и настройке группы рабочей группы и доступности, а также приведены пошаговые инструкции, которые описаны в других статьях, например создание кластера или развертывание группы доступности. 


## <a name="prerequisites"></a>предварительные требования

Чтобы настроить группу доступности рабочей группы, вам потребуется следующее:
- По крайней мере две виртуальные машины Windows Server 2016 (или более поздней версии), которые работают SQL Server 2016 (или более поздней версии), развернуты в одной группе доступности или разных зонах доступности, используя статические IP-адреса. 
- Локальная сеть, в которой не менее 4 бесплатных IP-адресов в подсети. 
- Учетная запись на каждом компьютере в группе администраторов, которая также имеет права системного администратора в SQL Server. 
- Открытые порты: TCP 1433, TCP 5022, TCP 59999. 

Для справки в этой статье используются следующие параметры, но их можно изменить по мере необходимости: 

| **Название** | **Параметр** |
| :------ | :---------------------------------- |
| **NODE1**   | AGNode1 (10.0.0.4) |
| **NODE2**   | AGNode2 (10.0.0.5) |
| **Имя кластера** | АГВГАГ (10.0.0.6) |
| **Средство прослушивания** | Аглистенер (10.0.0.7) | 
| **DNS-суффикс** | ag.wgcluster.example.com | 
| **Имя рабочей группы** | агворкграуп | 
| &nbsp; | &nbsp; |

## <a name="set-dns-suffix"></a>Задать DNS-суффикс 

На этом шаге настройте DNS-суффикс для обоих серверов. Например, `ag.wgcluster.example.com`. Это позволяет использовать имя объекта, к которому нужно подключиться, как полный адрес в сети, например `AGNode1.ag.wgcluster.example.com`. 

Чтобы настроить DNS-суффикс, выполните следующие действия.

1. Подключитесь к первому узлу по протоколу RDP и откройте диспетчер сервера. 
1. Выберите **локальный сервер** , а затем выберите имя виртуальной машины в поле **имя компьютера**. 
1. Выберите **изменить...** в разделе **, чтобы переименовать этот компьютер..** . 
1. Измените имя рабочей группы так, чтобы оно было понятным, например `AGWORKGROUP`: 

   ![Изменить имя рабочей группы](media/virtual-machines-windows-sql-workgroup-availability-group/1-change-workgroup-name.png)

1. Выберите **другие...** , чтобы открыть диалоговое окно **DNS-суффикс и имя компьютера NetBIOS** . 
1. В поле **основной DNS-суффикс этого компьютера**введите имя DNS-суффикса, например `ag.wgcluster.example.com`, а затем нажмите кнопку **ОК**. 

   ![Добавить DNS-суффикс](media/virtual-machines-windows-sql-workgroup-availability-group/2-add-dns-suffix.png)

1. Убедитесь, что **полное имя компьютера** теперь отображает DNS-суффикс, а затем нажмите кнопку **ОК** , чтобы сохранить изменения: 

   ![Добавить DNS-суффикс](media/virtual-machines-windows-sql-workgroup-availability-group/3-confirm-full-computer-name.png)

1. Перезагрузите сервер при появлении соответствующего запроса. 
1. Повторите эти действия на всех других узлах, которые будут использоваться для группы доступности. 

## <a name="edit-host-file"></a>Изменить файл узла

Поскольку Active Directory нет, проверить подлинность подключений Windows невозможно. Таким образом, назначьте доверие, отредактировав файл узла в текстовом редакторе. 

Чтобы изменить файл узла, выполните следующие действия.

1. Подключитесь к виртуальной машине по протоколу RDP. 
1. Используйте **проводник** для перехода к `c:\windows\system32\drivers\etc`. 
1. Щелкните правой кнопкой мыши файл **hosts** и откройте его в **блокноте** (или любом другом текстовом редакторе).
1. В конце файла добавьте запись для каждого узла, группы доступности и прослушивателя в форме `IP Address, DNS Suffix #comment` например: 

   ```
   10.0.0.4 AGNode1.ag.wgcluster.example.com #Availability group node
   10.0.0.5 AGNode2.ag.wgcluster.example.com #Availability group node
   10.0.0.6 AGWGAG.ag.wgcluster.example.com #Cluster IP
   10.0.0.7 AGListener.ag.wgcluster.example.com #Listener IP
   ```
 
   ![Добавление записей для IP-адреса, кластера и прослушивателя в файл узла](media/virtual-machines-windows-sql-workgroup-availability-group/4-host-file.png)

## <a name="set-permissions"></a>Установка разрешений

Поскольку нет Active Directory для управления разрешениями, для создания кластера необходимо вручную разрешить невстроенную учетную запись локального администратора. 

Для этого выполните следующий командлет PowerShell в административном сеансе PowerShell на каждом узле: 

```PowerShell

new-itemproperty -path HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System -Name LocalAccountTokenFilterPolicy -Value 1
```

## <a name="create-the-failover-cluster"></a>Создание отказоустойчивого кластера.

На этом шаге вы создадите отказоустойчивый кластер. Если вы не знакомы с этими действиями, вы можете следовать инструкциям из [учебника по отказоустойчивому кластеру](virtual-machines-windows-portal-sql-create-failover-cluster.md#step-2-configure-the-windows-server-failover-cluster-with-storage-spaces-direct).

Важные различия между учебником и действиями, которые необходимо выполнить для кластера рабочей группы:
- Снимите флажок **хранилище**и **Локальные дисковые пространства** при выполнении проверки кластера. 
- При добавлении узлов в кластер Добавьте полное имя, например:
   - `AGNode1.ag.wgcluster.example.com`
   - `AGNode2.ag.wgcluster.example.com`
- Снимите флажок **Добавить все подходящие хранилища в кластер**. 

После создания кластера Назначьте статический IP-адрес кластера. Для этого выполните следующие действия.

1. На одном из узлов откройте **Диспетчер отказоустойчивости кластеров**, выберите кластер, щелкните правой кнопкой мыши **Имя: \<клустернам >** в разделе **ресурсы ядра кластера** , а затем выберите пункт **Свойства**. 

   ![Запуск свойств для имени кластера](media/virtual-machines-windows-sql-workgroup-availability-group/5-launch-cluster-name-properties.png)

1. Выберите IP-адрес в списке **IP-адреса** и нажмите кнопку **изменить**. 
1. Выберите **использовать статический**, укажите IP-адрес кластера, а затем нажмите кнопку **ОК**. 

   ![Укажите статический IP-адрес для кластера](media/virtual-machines-windows-sql-workgroup-availability-group/6-provide-static-ip-for-cluster.png)

1. Убедитесь, что параметры правильно отображаются, а затем нажмите кнопку **ОК** , чтобы сохранить их:

   ![Проверка свойств кластера](media/virtual-machines-windows-sql-workgroup-availability-group/7-verify-cluster-properties.png)

## <a name="create-a-cloud-witness"></a>Создание облака-свидетеля 

На этом шаге настройте облачный ресурс-свидетель. Если вы не знакомы с действиями, см. [руководство по отказоустойчивому кластеру](virtual-machines-windows-portal-sql-create-failover-cluster.md#create-a-cloud-witness). 

## <a name="enable-availability-group-feature"></a>Включение функции группы доступности 

На этом шаге включите функцию группы доступности. Если вы не знакомы с этими шагами, см. [руководство по группе доступности](virtual-machines-windows-portal-sql-availability-group-tutorial.md#enable-availability-groups). 

## <a name="create-keys-and-certificate"></a>Создание ключей и сертификатов

На этом шаге создайте сертификаты, которые используются именем входа SQL в зашифрованной конечной точке. Создайте папку на каждом узле для хранения резервных копий сертификатов, например `c:\certs`. 

Чтобы настроить первый узел, выполните следующие действия. 

1. Откройте **SQL Server Management Studio** и подключитесь к первому узлу, например `AGNode1`. 
1. Откройте **новое окно запроса** и выполните следующую инструкцию TRANSACT-SQL (T-SQL) после обновления до сложного и безопасного пароля:

   ```sql
   USE master;  
   CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'PassWOrd123!';  
   GO

   --create a cert from the master key
   USE master;  
   CREATE CERTIFICATE AGNode1Cert   
      WITH SUBJECT = 'AGNode1 Certificate';  
   GO  

   --Backup the cert and transfer it to AGNode2
   BACKUP CERTIFICATE AGNode1Cert TO FILE = 'C:\certs\AGNode1Cert.crt';  
   GO  
   ```

1. Затем создайте конечную точку HADR и используйте сертификат для проверки подлинности, выполнив следующую инструкцию Transact-SQL (T-SQL):

   ```sql
   --CREATE or ALTER the mirroring endpoint
   CREATE ENDPOINT hadr_endpoint  
      STATE = STARTED  
      AS TCP (  
         LISTENER_PORT=5022  
         , LISTENER_IP = ALL  
      )   
      FOR DATABASE_MIRRORING (   
         AUTHENTICATION = CERTIFICATE AGNode1Cert  
         , ENCRYPTION = REQUIRED ALGORITHM AES  
         , ROLE = ALL  
      );  
   GO  
   ```

1. С помощью **проводника** перейдите в расположение файла, в котором находится сертификат, например `c:\certs`. 
1. Вручную создайте копию сертификата, например `AGNode1Cert.crt`, с первого узла и передайте ее в то же расположение на втором узле. 

Чтобы настроить второй узел, выполните следующие действия. 

1. Подключитесь к второму узлу с помощью **SQL Server Management Studio**, например `AGNode2`. 
1. В **новом окне запроса** выполните следующую инструкцию TRANSACT-SQL (T-SQL) после обновления до сложного и безопасного пароля: 

   ```sql
   USE master;  
   CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'PassWOrd123!';  
   GO 
   
   --create a cert from the master key
   USE master;  
   CREATE CERTIFICATE AGNode2Cert   
      WITH SUBJECT = 'AGNode2 Certificate';  
   GO  
   --Backup the cert and transfer it to AGNode1
   BACKUP CERTIFICATE AGNode2Cert TO FILE = 'C:\certs\AGNode2Cert.crt';  
   GO
   ```

1. Затем создайте конечную точку HADR и используйте сертификат для проверки подлинности, выполнив следующую инструкцию Transact-SQL (T-SQL):

   ```sql
   --CREATE or ALTER the mirroring endpoint
   CREATE ENDPOINT hadr_endpoint  
      STATE = STARTED  
      AS TCP (  
         LISTENER_PORT=5022  
         , LISTENER_IP = ALL  
      )   
      FOR DATABASE_MIRRORING (   
         AUTHENTICATION = CERTIFICATE AGNode2Cert  
         , ENCRYPTION = REQUIRED ALGORITHM AES  
         , ROLE = ALL  
      );  
   GO  
   ```

1. С помощью **проводника** перейдите в расположение файла, в котором находится сертификат, например `c:\certs`. 
1. Вручную создайте копию сертификата, например `AGNode2Cert.crt`, с второго узла и передайте ее в то же расположение на первом узле. 

Если в кластере есть другие узлы, повторите эти действия и измените соответствующие имена сертификатов. 

## <a name="create-logins"></a>Создание имен для входа

Проверка подлинности с помощью сертификата используется для синхронизации данных между узлами. Чтобы разрешить это, создайте имя входа для другого узла, создайте пользователя для имени входа, создайте сертификат для имени входа, чтобы использовать резервную копию сертификата, а затем предоставьте Connect в конечной точке зеркального отображения. 

Для этого сначала выполните следующий запрос Transact-SQL (T-SQL) на первом узле, например `AGNode1`: 

```sql
--create a login for the AGNode2
USE master;  
CREATE LOGIN AGNode2_Login WITH PASSWORD = 'PassWord123!';  
GO  

--create a user from the login
CREATE USER AGNode2_User FOR LOGIN AGNode2_Login;  
GO  

--create a certificate that the login uses for authentication
CREATE CERTIFICATE AGNode2Cert  
   AUTHORIZATION AGNode2_User  
   FROM FILE = 'C:\certs\AGNode2Cert.crt'  
GO 

--grant connect for login
GRANT CONNECT ON ENDPOINT::hadr_endpoint TO [AGNode2_login];  
GO
```

Затем выполните следующий запрос Transact-SQL (T-SQL) на втором узле, например `AGNode2`: 

```sql
--create a login for the AGNode1
USE master;  
CREATE LOGIN AGNode1_Login WITH PASSWORD = 'PassWord123!';  
GO  

--create a user from the login
CREATE USER AGNode1_User FOR LOGIN AGNode1_Login;  
GO  

--create a certificate that the login uses for authentication
CREATE CERTIFICATE AGNode1Cert  
   AUTHORIZATION AGNode1_User  
   FROM FILE = 'C:\certs\AGNode1Cert.crt'  
GO 

--grant connect for login
GRANT CONNECT ON ENDPOINT::hadr_endpoint TO [AGNode1_login];  
GO
```

Если в кластере есть другие узлы, повторите эти действия, изменив соответствующие сертификаты и имена пользователей. 

## <a name="configure-availability-group"></a>Настройка группы доступности

На этом шаге настройте группу доступности и добавьте в нее свои базы данных. Не создавайте прослушиватель в данный момент. Если вы не знакомы с действиями, см. [руководство по группе доступности](virtual-machines-windows-portal-sql-availability-group-tutorial.md#create-the-availability-group). Не забудьте инициировать отработку отказа и восстановление размещения, чтобы убедиться, что все работает должным образом. 

   > [!NOTE]
   > В случае сбоя в процессе синхронизации может потребоваться предоставить `NT AUTHORITY\SYSTEM` права sysadmin на создание ресурсов кластера на первом узле, например `AGNode1` временно. 

## <a name="configure-load-balancer"></a>Настройка балансировщика нагрузки

На этом заключительном шаге настройте подсистему балансировки нагрузки с помощью [портал Azure](virtual-machines-windows-portal-sql-alwayson-int-listener.md) или [PowerShell](virtual-machines-windows-portal-sql-ps-alwayson-int-listener.md) .


## <a name="next-steps"></a>Next Steps

Для настройки группы доступности можно также использовать команду [AZ SQL VM CLI](virtual-machines-windows-sql-availability-group-cli.md) . 


