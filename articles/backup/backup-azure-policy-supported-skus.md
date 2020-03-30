---
title: Поддерживаемые номера SKU виртуальных машин для Политики Azure
description: Статья, описывающая поддерживаемые VM SKUs (издателем, предложением изображений и Изображением SKU), которые поддерживаются для встроенных политик Azure, предоставляемых резервным копированием
ms.topic: conceptual
ms.date: 11/08/2019
ms.openlocfilehash: 1b6a94b0f57ecfea946654c6cae38ac335335e00
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74980118"
---
# <a name="supported-vm-skus-for-azure-policy"></a>Поддерживаемые номера SKU виртуальных машин для Политики Azure

Резервное копирование Azure предоставляет встроенную политику (с использованием политики Azure), которая может быть назначена **всем VMs Azure в определенном месте в группе подписок или ресурсов.** Когда эта политика присваивается заданной области, все новые ВМ, созданные в этой области, автоматически настраиваются для резервного копирования **в существующее хранилище в том же месте и подписке.** В таблице ниже перечислены все СКУ VM, поддерживаемые этой политикой.

### <a name="supported-vms"></a>**Поддерживаемые виртуальные машины**

**Название политики:** Настройка резервного копирования на вс-чатах местоположения к существующему центральному хранилищу в том же месте

Издатель изображения | Предложение изображения | Номер SKU образа.
--- | --- | ---
MicrosoftWindowsServer | WindowsServer | Windows Server 2008 R2 SP1 (2008-R2-SP1)
MicrosoftWindowsServer | WindowsServer | (малый диск) Windows Server 2008 R2 SP (2008-R2-SP1-smalldisk)
MicrosoftWindowsServer | WindowsServer | Windows Server 2012 Центр обработки данных (2012-Центр обработки данных)
MicrosoftWindowsServer | WindowsServer | (малый диск) Windows Server 2012 Центр обработки данных (2012-Datacenter-smalldisk)
MicrosoftWindowsServer | WindowsServer | Windows Server 2012 R2 Datacenter (2012-R2-Datacenter)
MicrosoftWindowsServer | WindowsServer | (малый диск) Windows Server 2012 R2 Datacenter (2012-R2-Datacenter-smalldisk)
MicrosoftWindowsServer | WindowsServer | Windows Server 2016 Центр обработки данных (2016-Центр обработки данных)
MicrosoftWindowsServer | WindowsServer | Windows Server 2016 Центр обработки данных - Ядро сервера (2016-Datacenter-Server-Core)
MicrosoftWindowsServer | WindowsServer | (малый диск) Windows Server 2016 Центр обработки данных - Ядро сервера (2016-Datacenter-Server-Core-smalldisk)
MicrosoftWindowsServer | WindowsServer | (малый диск) Windows Server 2016 Центр обработки данных (2016-Datacenter-smalldisk)
MicrosoftWindowsServer | WindowsServer | Windows Server 2019 Datacenter Server Core с контейнерами (2016-Datacenter-with-Containers)
MicrosoftWindowsServer | WindowsServer | Windows Server 2016 Удаленный рабочий стол сессии Host 2016 (2016-Datacenter-с-RDSH)
MicrosoftWindowsServer | WindowsServer | Windows Server 2019 Центр обработки данных (2019-Центр данных)
MicrosoftWindowsServer | WindowsServer | Windows Server 2019 Datacenter Server Core (2019-Datacenter-Core)
MicrosoftWindowsServer | WindowsServer | (малый диск) Windows Server 2019 Datacenter Server Core (2019-Datacenter-Core-smalldisk)
MicrosoftWindowsServer | WindowsServer | Windows Server 2019 Datacenter Server Core с контейнерами (2019-Datacenter-Core-with-Containers)
MicrosoftWindowsServer | WindowsServer | (малый диск) Windows Server 2019 Datacenter Server Core с контейнерами (2019-Datacenter-Core-with-Containers-smalldisk)
MicrosoftWindowsServer | WindowsServer | (малый диск) Windows Server 2019 Центр обработки данных (2019-Datacenter-smalldisk)
MicrosoftWindowsServer | WindowsServer | Windows Server 2019 Центр обработки данных с контейнерами (2019-Datacenter-с-контейнеры)
MicrosoftWindowsServer | WindowsServer | (малый диск) Windows Server 2019 Центр обработки данных с контейнерами (2019-Datacenter-с-контейнеры-малый диск)
MicrosoftWindowsServer | WindowsServer | Windows Server 2019 Центр обработки данных (zh-cn) (2019-Datacenter-zhcn)
MicrosoftWindowsServer | WindowsServerSemiAnnual | Центр обработки данных-Core-1709-smalldisk
MicrosoftWindowsServer | WindowsServerSemiAnnual | Центр обработки данных-Core-1709-с-контейнерами-малым диском
MicrosoftWindowsServer | WindowsServerSemiAnnual | Центр обработки данных-Core-1803-с-контейнеры-малый диск
MicrosoftWindowsServerHPCPack | WindowsServerHPCPack | Все изображения SKUs
MicrosoftSQLServer | S-L2016SP1-WS2016 | Все изображения SKUs
MicrosoftSQLServer | SQL2016-WS2016  | Все изображения SKUs
MicrosoftSQLServer | S-L2016SP1-WS2016-BYOL | Все изображения SKUs
MicrosoftSQLServer | S-L2012SP3-WS2012R2 | Все изображения SKUs
MicrosoftSQLServer | S-L2016-WS2012R2 | Все изображения SKUs
MicrosoftSQLServer | SQL2014SP2-WS2012R2 | Все изображения SKUs
MicrosoftSQLServer | S-L2012SP3-WS2012R2-BYOL | Все изображения SKUs
MicrosoftSQLServer | S-L2014SP1-WS2012R2-BYOL | Все изображения SKUs
MicrosoftSQLServer | S-L2014SP2-WS2012R2-BYOL | Все изображения SKUs
MicrosoftSQLServer | СЗЛ2016-WS2012R2-BYOL | Все изображения SKUs
MicrosoftRServer | MLServer-WS2016 | Все изображения SKUs
MicrosoftVisualStudio | VisualStudio | Все изображения SKUs
MicrosoftVisualStudio | Windows | Все изображения SKUs
MicrosoftDynamicsAX | Dynamics  | Pre-Req-AX7-Onebox-U8
microsoft-ads | окна-данные-наука-Vm | Все изображения SKUs
MicrosoftWindowsDesktop | Windows-10 | Все изображения SKUs
RedHat | RHEL | 6.7, 6.8, 6.9, 6.10, 7.2, 7.3, 7.4, 7.5, 7.6, 7.7
RedHat | RHEL-SAP-HANA | 6.7, 7.2, 7.3
SUSE | SLES | 12.X
SUSE | SLES-HPC | 12.X
SUSE | SLES-HPC-Приоритет | 12.X
SUSE | SLES-SAP | 12.X
SUSE | SLES-SAP-BYOS | 12.X
SUSE | SLES-Приоритет | 12.X
SUSE | SLES-BYOS | 12.X
SUSE | SLES-SAPCAL | 12.X
SUSE | SLES-Стандарт | 12.X
Canonical | UbuntuServer | 14.04.0-LTS
Canonical | UbuntuServer | 14.04.1-LTS
Canonical | UbuntuServer | 14.04.2-LTS
Canonical | UbuntuServer | 14.04.3-LTS
Canonical | UbuntuServer | 14.04.4-LTS
Canonical | UbuntuServer | 14.04.5-DAILY-LTS
Canonical | UbuntuServer | 14.04.5-LTS
Canonical | UbuntuServer | 16.04-DAILY-LTS
Canonical | UbuntuServer | 16.04-LTS
Canonical | UbuntuServer | 16.04.0-LTS
Canonical | UbuntuServer | 18.04-DAILY-LTS
Canonical | UbuntuServer | 18.04-LTS
Oracle; | Oracle-Linux | 6.8, 6.9, 6.10, 7.3, 7.4, 7.5, 7.6
OpenLogic | CentOS | 6.X, 7.X
OpenLogic | CentOS-LVM | 6.X, 7.X
OpenLogic | CentOS-SRIOV | 6.X, 7.X
cloudera | cloudera-centos-os | 7.X
