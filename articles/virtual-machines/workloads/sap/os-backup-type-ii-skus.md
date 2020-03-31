---
title: Резервное копирование и восстановление операционной системы SAP HANA в Azure (большие экземпляры) для номеров SKU типа II | Документация Майкрософт
description: Выполните резервное копирование операционной системы и восстановление SAP HANA на Azure (большие экземпляры) II SKUs типа II
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/12/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 100e1b974e54d8c0065194bc7beb18f458011434
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77616864"
---
# <a name="os-backup-and-restore-for-type-ii-skus-of-revision-3-stamps"></a>Резервное копирование ОС и восстановление для ТИП II SKUs пересмотра 3 марок

В этом документе описаны шаги для выполнения резервного копирования уровня файла операционной системы и восстановления **для SKUs типа II** крупных инстанций «Ревизии 3». 

>[!Important]
> **Эта статья не распространяется на развертывание SKU типа II в марках Пересмотр 4 HANA Large Instance.** Boot LUNS из типа II HANA Большой экземпляр единиц, которые развернуты в пересмотре 4 HANA Большой экземпляр марки могут быть подкреплены хранения снимков, как это в случае с типом I SKUs уже в пересмотре 3 марки


>[!NOTE]
>Для сценариев резервного копирования операционной системы используется предварительно установленное на сервере программное обеспечение ReaR.  

После завершения подготовки командой `Service Management` Майкрософт по умолчанию сервер настраивается с двумя графиками резервного копирования для резервного копирования задней части операционной системы на уровне файловой системы. Вы можете проверить расписание заданий резервного копирования, используя следующую команду:
```
#crontab –l
```
Вы можете изменить расписание резервного копирования в любое время, используя следующую команду:
```
#crontab -e
```
## <a name="how-to-take-a-manual-backup"></a>Как создать резервную копию вручную?

Резервное копирование файловой системы ОС запланировано с использованием **задания cron** уже. Тем не менее вы также можете выполнить резервное копирование операционной системы на уровне файловой системы вручную. Для этого выполните команду ниже:

```
#rear -v mkbackup
```
На снимке экрана ниже показан пример создания резервной копии вручную:

![пример](media/HowToHLI/OSBackupTypeIISKUs/HowtoTakeManualBackup.PNG)


## <a name="how-to-restore-a-backup"></a>Как восстановить резервную копию?

Вы можете восстановить полную резервную копию или отдельный файл из нее. Для этого воспользуйтесь следующей командой:

```
#tar  -xvf  <backup file>  [Optional <file to restore>]
```
После операции восстановления файл будет восстановлен в текущем рабочем каталоге.

С помощью команды ниже показано восстановление файла */etc/fstabfrom* из файла резервной копии *backup.tar.gz*.
```
#tar  -xvf  /osbackups/hostname/backup.tar.gz  etc/fstab 
```
>[!NOTE] 
>Вам необходимо скопировать файл в нужное расположение после его восстановления из файла резервной копии.

На следующем скриншоте показана восстановление полной резервной копирования:

![HowtoRestoreaBackup.PNG](media/HowToHLI/OSBackupTypeIISKUs/HowtoRestoreaBackup.PNG)

## <a name="how-to-install-the-rear-tool-and-change-the-configuration"></a>Как установить инструмент ReaR и изменить конфигурацию? 

Для **номеров SKU типа II** больших экземпляров HANA пакеты Relax-and-Recover (ReaR) **предварительно установлены**, поэтому никакие дополнительные действия с вашей стороны не требуются. Для резервного копирования операционной системы вы можете напрямую запустить ReaR.
Тем не менее в случаях, когда требуется отдельная установка пакетов, вы можете выполнить шаги ниже, чтобы установить и настроить инструмент ReaR.

Чтобы установить пакеты резервного копирования **ReaR**, выполните команды ниже:

Для операционной системы **SLES** используйте следующую команду:
```
#zypper install <rear rpm package>
```
Для операционной системы **RHEL** используйте следующую команду: 
```
#yum install rear -y
```
Чтобы настроить инструмент ReaR, вам необходимо обновить параметры **OUTPUT_URL** и **BACKUP_URL** в *файле /etc/rear/local.conf*.
```
OUTPUT=ISO
ISO_MKISOFS_BIN=/usr/bin/ebiso
BACKUP=NETFS
OUTPUT_URL="nfs://nfsip/nfspath/"
BACKUP_URL="nfs://nfsip/nfspath/"
BACKUP_OPTIONS="nfsvers=4,nolock"
NETFS_KEEP_OLD_BACKUP_COPY=
EXCLUDE_VG=( vgHANA-data-HC2 vgHANA-data-HC3 vgHANA-log-HC2 vgHANA-log-HC3 vgHANA-shared-HC2 vgHANA-shared-HC3 )
BACKUP_PROG_EXCLUDE=("${BACKUP_PROG_EXCLUDE[@]}" '/media' '/var/tmp/*' '/var/crash' '/hana' '/usr/sap'  ‘/proc’)
```

На следующем скриншоте показано восстановление полного ![резервного копирования: RearToolConfiguration.PNG](media/HowToHLI/OSBackupTypeIISKUs/RearToolConfiguration.PNG)
