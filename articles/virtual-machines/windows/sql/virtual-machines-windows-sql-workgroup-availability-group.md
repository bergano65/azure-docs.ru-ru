---
title: Настройка группы доступности рабочей группы, не зависят от доменов
description: Узнайте, как настроить независимую рабочую группу Active Directory Domain Always On availability group на виртуальной машине Сервера S'L в Azure.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77122679"
---
# <a name="configure-a-workgroup-availability-group"></a>Настройка группы доступности рабочей группы 

В этой статье разъясняются шаги, необходимые для создания независимого кластера доменов Active Directory с группой всегда на доступность; это также известно как кластер рабочей группы. В этой статье основное внимание уделяется шагам, которые имеют отношение к подготовке и настройке группы рабочих групп и доступности, и затушевываются шаги, которые описаны в других статьях, например, как создать кластер или развернуть группу доступности. 


## <a name="prerequisites"></a>Предварительные требования

Для настройки группы доступности рабочей группы необходимо следующее:
- По крайней мере, два Windows Server 2016 (или выше) виртуальных машин под управлением S'L Server 2016 (или выше), развернутых в одном и том же наборе доступности или в различных зонах доступности с использованием статических IP-адресов. 
- Локальная сеть с минимум 4 бесплатными IP-адресами в подсети. 
- Учетная запись на каждой машине в группе администраторов, которая также имеет права sysadmin в сервере S'L. 
- Открытые порты: TCP 1433, TCP 5022, TCP 59999. 

Для справки в этой статье используются следующие параметры, но могут быть изменены по мере необходимости: 

| **Название** | **Параметр** |
| :------ | :---------------------------------- |
| **Узла1**   | AGNode1 (10.0.0.4) |
| **Узел2**   | AGNode2 (10.0.0.5) |
| **Название кластера** | AGWGAG (10.0.0.6) |
| **Прослушивателя** | AGListener (10.0.0.7) | 
| **DNS-суффикс** | ag.wgcluster.example.com | 
| **Название рабочей группы** | AGWorkgroup | 
| &nbsp; | &nbsp; |

## <a name="set-dns-suffix"></a>Установить dNS суффикс 

На этом этапе назначаем суффикс DNS для обоих серверов. Например, `ag.wgcluster.example.com`. Это позволяет использовать имя объекта, к который вы хотите подключиться, в качестве `AGNode1.ag.wgcluster.example.com`полностью квалифицированного адреса в вашей сети, например. 

Чтобы настроить суффикс DNS, выполните следующие действия:

1. RDP в ваш первый узла и открытый менеджер сервера. 
1. Выберите **локальный сервер,** а затем выберите имя виртуальной машины под **именем Computer.** 
1. Выберите **Изменение...** под **переименовать этот компьютер ...**. 
1. Изменение названия названия рабочей группы, чтобы `AGWORKGROUP`быть чем-то значимым, например: 

   ![Изменение названия рабочей группы](media/virtual-machines-windows-sql-workgroup-availability-group/1-change-workgroup-name.png)

1. Выберите **больше...,** чтобы открыть диалоговый ящик **DNS Suffix и NetBIOS Computer Name.** 
1. Введите имя суффикса DNS под **суффиксом Primary DNS этого компьютера,** `ag.wgcluster.example.com` например, а затем выберите **OK:** 

   ![Добавить dNS суффикс](media/virtual-machines-windows-sql-workgroup-availability-group/2-add-dns-suffix.png)

1. Подтвердите, что **полное имя компьютера** теперь показывает суффикс DNS, а затем выберите **OK,** чтобы сохранить ваши изменения: 

   ![Добавить dNS суффикс](media/virtual-machines-windows-sql-workgroup-availability-group/3-confirm-full-computer-name.png)

1. Перезагрузите сервер, когда вам будет предложено сделать это. 
1. Повторите эти действия на любых других узлах, которые будут использоваться для группы доступности. 

## <a name="edit-host-file"></a>Отодвило файла хоста

Поскольку нет активного каталога, нет никакого способа проверки подлинности соединений окон. Таким образом, присвоите доверие путем редактирования файла-хоста с текстовым редактором. 

Чтобы отсеять файл хоста, выполните следующие действия:

1. RDP в виртуальную машину. 
1. Используйте **Файл Explorer,** чтобы перейти к `c:\windows\system32\drivers\etc`. 
1. Нажмите правой кнопкой мыши файл **хоста** и откройте файл с **помощью блокнота** (или любого другого текстового редактора).
1. В конце файла добавьте запись для каждого узла, группы доступности и `IP Address, DNS Suffix #comment` слушателя в виде: 

   ```
   10.0.0.4 AGNode1.ag.wgcluster.example.com #Availability group node
   10.0.0.5 AGNode2.ag.wgcluster.example.com #Availability group node
   10.0.0.6 AGWGAG.ag.wgcluster.example.com #Cluster IP
   10.0.0.7 AGListener.ag.wgcluster.example.com #Listener IP
   ```
 
   ![Добавление записей для IP-адреса, кластера и слушателя в файл хоста](media/virtual-machines-windows-sql-workgroup-availability-group/4-host-file.png)

## <a name="set-permissions"></a>Установка разрешений

Поскольку для управления разрешениями нет Active Directory, необходимо вручную разрешить локальной учетной записи локального администратора, не построенную в непостроенном состоянии, для создания кластера. 

Для этого запустите следующий cmdlet PowerShell в административном сеансе PowerShell на каждом узлах: 

```PowerShell

new-itemproperty -path HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System -Name LocalAccountTokenFilterPolicy -Value 1
```

## <a name="create-the-failover-cluster"></a>Создание отказоустойчивого кластера.

На этом этапе вы создадите кластер failover. Если вы не знакомы с этими шагами, вы можете следовать им из [учебника кластера failover.](virtual-machines-windows-portal-sql-create-failover-cluster.md#step-2-configure-the-windows-server-failover-cluster-with-storage-spaces-direct)

Заметные различия между учебником и тем, что следует сделать для кластера рабочей группы:
- Отменить **хранение и** **пробелы в хранении Прямо** при запуске кластерной проверки. 
- При добавлении узлов в кластер добавьте полностью квалифицированное имя, такое как:
   - `AGNode1.ag.wgcluster.example.com`
   - `AGNode2.ag.wgcluster.example.com`
- Uncheck **Добавьте все подходящие хранилища в кластер.** 

После создания кластера назначай статический IP-адрес кластера. Для этого выполните следующие действия.

1. На одном из узлов, открытый **failover кластера менеджер**, выберите кластер, право нажмите **на имя: \<ClusterNam>** под **кластером основных ресурсов,** а затем выбрать **Свойства.** 

   ![Свойства запуска для названия кластера](media/virtual-machines-windows-sql-workgroup-availability-group/5-launch-cluster-name-properties.png)

1. Выберите IP-адрес под **IP-адресами** и выберите **Edit.** 
1. Выберите **Использование Static,** предоставьте IP-адрес кластера, а затем выберите **OK:** 

   ![Предоставление статического IP-адреса для кластера](media/virtual-machines-windows-sql-workgroup-availability-group/6-provide-static-ip-for-cluster.png)

1. Убедитесь, что настройки выглядят правильно, а затем выберите **OK,** чтобы сохранить их:

   ![Проверка свойств кластера](media/virtual-machines-windows-sql-workgroup-availability-group/7-verify-cluster-properties.png)

## <a name="create-a-cloud-witness"></a>Создание облака-свидетеля 

На этом этапе назначаем свидетель обмена облаками. Если вы не знакомы с шагами, см. [failover cluster tutorial](virtual-machines-windows-portal-sql-create-failover-cluster.md#create-a-cloud-witness) 

## <a name="enable-availability-group-feature"></a>Включить функцию группы доступности 

На этом этапе включите функцию группы доступности. Если вы не знакомы с шагами, [см.](virtual-machines-windows-portal-sql-availability-group-tutorial.md#enable-availability-groups) 

## <a name="create-keys-and-certificate"></a>Создание ключей и сертификата

На этом этапе создайте сертификаты, которые логин s'L использует на зашифрованной конечной точке. Создайте папку на каждом узлах для хранения `c:\certs`резервных сообщений сертификата, например. 

Чтобы настроить первый узла, выполните следующие действия: 

1. Откройте **студию управления серверами и** подключитесь `AGNode1`к первому узлам, например. 
1. Откройте **окно нового запроса** и запустите следующее заявление О Трансакт-СЗЛ (Т-СЗЛ) после обновления сложного и безопасного пароля:

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

1. Затем создайте конечную точку HADR и используйте сертификат для проверки подлинности, запустив эту выписку по Трансакт-СЗЛ:

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

1. Используйте **File Explorer,** чтобы перейти к месту файла, где находится ваш сертификат, `c:\certs`например. 
1. Вручную сделайте копию сертификата, `AGNode1Cert.crt`например, из первого узла и перенесите его в то же место на втором уде. 

Чтобы настроить второй узлы, выполните следующие действия: 

1. Подключитесь ко второму узлам со `AGNode2` **студией управления серверами S'L,** например. 
1. В окне **нового запроса** запустите следующее заявление ОТранс-СЗЛ (Т-СЗЛ) после обновления сложного и безопасного пароля: 

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

1. Затем создайте конечную точку HADR и используйте сертификат для проверки подлинности, запустив эту выписку по Трансакт-СЗЛ:

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

1. Используйте **File Explorer,** чтобы перейти к месту файла, где находится ваш сертификат, `c:\certs`например. 
1. Вручную сделайте копию сертификата, `AGNode2Cert.crt`например, из второго узла и перенесите его в то же место на первом узлах. 

Если в кластере есть другие узлы, повторите эти шаги и там, изменив соответствующие имена сертификатов. 

## <a name="create-logins"></a>Создание логинов

Проверка подлинности сертификата используется для синхронизации данных между узлами. Чтобы позволить это, создайте логин для другого узла, создайте пользователя для входа, создайте сертификат для входа, чтобы использовать резервный сертификат, а затем предоставьте подключение на точке зеркального отражения. 

Для этого сначала запустите следующий запрос «Трансакт-СЗЛ» (Т-СЗЛ) `AGNode1`на первом узле, например: 

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

Далее запустите следующий запрос «Трансакт-СЗЛ» (Т-СЗЛ) `AGNode2`на втором узеле, например: 

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

Если в кластере есть другие узлы, повторите эти действия и там, изменив соответствующий сертификат и имена пользователей. 

## <a name="configure-availability-group"></a>Настройка группы доступности

На этом этапе настройте группу доступности и добавьте к ней базы данных. Не создавайте слушателя в это время. Если вы не знакомы с шагами, см. [availability group tutorial](virtual-machines-windows-portal-sql-availability-group-tutorial.md#create-the-availability-group) Будьте уверены, чтобы инициировать failover и failback, чтобы проверить, что все работает, как это должно быть. 

   > [!NOTE]
   > При сбое в процессе синхронизации может потребоваться `NT AUTHORITY\SYSTEM` предоставить права сисадмина на создание кластерных `AGNode1` ресурсов на первом узлах, например временно. 

## <a name="configure-load-balancer"></a>Настройка балансиватора нагрузки

На этом заключительном этапе наверстируйте балансомер нагрузки с помощью [портала Azure](virtual-machines-windows-portal-sql-alwayson-int-listener.md) или [PowerShell](virtual-machines-windows-portal-sql-ps-alwayson-int-listener.md)


## <a name="next-steps"></a>Next Steps

Вы также можете использовать [Az S'L VM CLI](virtual-machines-windows-sql-availability-group-cli.md) для настройки группы доступности. 


