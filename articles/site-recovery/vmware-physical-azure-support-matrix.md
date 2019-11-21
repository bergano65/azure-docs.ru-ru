---
title: Support matrix for VMware/physical disaster recovery in Azure Site Recovery
description: Summarizes support for disaster recovery of VMware VMs and physical server to Azure using Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/15/2019
ms.author: raynew
ms.openlocfilehash: b75d9e9fc88cabacab912efa17938708eb061b99
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74232436"
---
# <a name="support-matrix-for-disaster-recovery--of-vmware-vms-and-physical-servers-to-azure"></a>Таблица поддержки аварийного восстановления виртуальных машин VMware и физических серверов в Azure

This article summarizes supported components and settings for disaster recovery of VMware VMs and physical servers to Azure using [Azure Site Recovery](site-recovery-overview.md).

- [Learn more](vmware-azure-architecture.md) about VMware VM/physical server disaster recovery architecture.
- Follow our [tutorials](tutorial-prepare-azure.md) to try out disaster recovery.

## <a name="deployment-scenarios"></a>Сценарии развертывания

**Сценарий** | **Дополнительные сведения**
--- | ---
Disaster recovery of VMware VMs | Репликация локальных виртуальных машин VMware в Azure. Этот сценарий можно развернуть с помощью портала Azure или [PowerShell](vmware-azure-disaster-recovery-powershell.md).
Disaster recovery of physical servers | Репликация локальных физических серверов Windows или Linux в Azure. Этот сценарий можно развернуть с помощью портала Azure.

## <a name="on-premises-virtualization-servers"></a>Локальные серверы виртуализации

**Сервер** | **Требования** | **Дополнительные сведения**
--- | --- | ---
с сервером vCenter | Version 6.7, 6.5, 6.0, or 5.5 | We recommend that you use a vCenter server in your disaster recovery deployment.
vSphere hosts | Version 6.7, 6.5, 6.0, or 5.5 | Мы рекомендуем размещать узлы vSphere и серверы vCenter в той же сети, в которой находится сервер обработки. By default the process server runs on the configuration server. [Узнайте больше](vmware-physical-azure-config-process-server-overview.md).


## <a name="site-recovery-configuration-server"></a>Сервер конфигурации Site Recovery

Сервер конфигурации размещается на локальном компьютере, где выполняются компоненты Site Recovery, включая сервер конфигурации, сервер обработки и главный целевой сервер.

- For VMware VMs you set the configuration server by downloading an OVF template to create a VMware VM.
- For physical servers, you set up the configuration server machine manually.

**Компонент** | **Требования**
--- |---
Ядра ЦП | 8
ОЗУ | 16 ГБ
Число дисков | 3 диска<br/><br/> В состав дисков входят: диск ОС, диск кэша сервера обработки, диск хранения (для восстановления размещения).
Свободное место на диске | 600 GB of space for the process server cache.
Свободное место на диске | 600 GB  of space for the retention drive.
Операционная система  | Windows Server 2012 R2, or Windows Server 2016 with Desktop experience <br/><br> If you plan to use the in-built Master Target of this appliance for failback, ensure that the OS version is same or higher than the replicated items.|
Язык операционной системы | Английский (en-us)
[PowerCLI](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1) | Not needed for configuration server version [9.14](https://support.microsoft.com/help/4091311/update-rollup-23-for-azure-site-recovery) or later. 
Роли Windows Server | Don't enable Active Directory Domain Services; Internet Information Services (IIS) or Hyper-V. 
Групповые политики| — запрет на использование командной строки; <br/> — запрет на использование инструментов редактирования реестра; <br/> — логика доверия для вложенных файлов; <br/> — включение выполнения скриптов. <br/> - [Learn more](https://technet.microsoft.com/library/gg176671(v=ws.10).aspx)|
IIS | Не забудьте выполнить следующие действия.<br/><br/> — убедитесь, что отсутствует предварительно созданный веб-сайт по умолчанию; <br/> — включите [анонимную аутентификацию](https://technet.microsoft.com/library/cc731244(v=ws.10).aspx); <br/> — включите параметр [FastCGI](https://technet.microsoft.com/library/cc753077(v=ws.10).aspx);  <br/> — убедитесь, что отсутствует предварительно созданный веб-сайт или приложение, ожидающее передачи данных через порт 443.<br/>
Тип сетевой карты | VMXNET3 (при развертывании в качестве виртуальной машины VMware)
Тип IP-адреса | Статические
порты; | 443 used for control channel orchestration<br/>9443 for data transport

## <a name="replicated-machines"></a>Реплицируемые компьютеры

Site Recovery поддерживает репликацию любой рабочей нагрузки, выполняемой на поддерживаемом компьютере.

> [!Note]
> The following table lists the support for machines with BIOS boot. Please refer to [Storage](#storage) section for support on UEFI based machines.

**Компонент** | **Дополнительные сведения**
--- | ---
Параметры компьютера | Компьютеры, которые реплицируются в Azure, должны соответствовать [требованиям Azure](#azure-vm-requirements).
Рабочая нагрузка компьютера | Site Recovery поддерживает репликацию любой рабочей нагрузки, выполняемой на поддерживаемом компьютере. [Узнайте больше](https://aka.ms/asr_workload).
Windows Server 2019 | Supported from [Update rollup 34](https://support.microsoft.com/help/4490016) (version 9.22 of the Mobility service) onwards.
Windows Server 2016 64-bit | Supported for Server Core, Server with Desktop Experience.
Windows Server 2012 R2 / Windows Server 2012 | Поддерживается.
Windows Server 2008 R2 with SP1 onwards. | Поддерживается.<br/><br/> From version 9.30.x.x (expected release beginning of November 2019) of the Mobility service agent, you need [servicing stack update (SSU)](https://support.microsoft.com/help/4490628) and [SHA-2 update](https://support.microsoft.com/help/4474419) installed on machines running Windows 2008 R2 with SP1 or later. SHA-1 isn't supported from September 2019, and if SHA-2 code signing isn't enabled the agent extension won't install/upgrade as expected. Learn more about [SHA-2 upgrade and requirements](https://aka.ms/SHA-2KB).
Windows Server 2008 with SP2 or later (64-bit/32-bit) |  Supported for migration only. [Узнайте больше](migrate-tutorial-windows-server-2008.md).<br/><br/> From version 9.30.x.x (expected release beginning of November 2019) of the Mobility service agent, you need [servicing stack update (SSU)](https://support.microsoft.com/help/4493730) and [SHA-2 update](h https://support.microsoft.com/help/4474419) installed on Windows 2008 SP2 machines. ISHA-1 isn't supported from September 2019, and if SHA-2 code signing isn't enabled the agent extension won't install/upgrade as expected. Learn more about [SHA-2 upgrade and requirements](https://aka.ms/SHA-2KB).
Windows 10, Windows 8.1, Windows 8 | Поддерживается.
Windows 7 with SP1 64-bit | Supported from [Update rollup 36](https://support.microsoft.com/help/4503156) (version 9.22 of the Mobility service) onwards. </br></br> From version 9.30.x.x (expected release beginning of November 2019) of the Mobility service agent, you need [servicing stack update (SSU)](https://support.microsoft.com/help/4490628) and [SHA-2 update](https://support.microsoft.com/help/4474419) installed on Windows 7 SP1 machines.  SHA-1 isn't supported from September 2019, and if SHA-2 code signing isn't enabled the agent extension won't install/upgrade as expected. Learn more about [SHA-2 upgrade and requirements](https://aka.ms/SHA-2KB).
Linux | Only 64-bit system is supported. 32-bit system isn't supported.<br/><br/>Every Linux server should have [Linux Integration Services (LIS) components](https://www.microsoft.com/download/details.aspx?id=55106) installed. It is required to boot the server in Azure after test failover/failover. If LIS components are missing, ensure to install the [components](https://www.microsoft.com/download/details.aspx?id=55106) before enabling replication for the machines to boot in Azure. <br/><br/> Site Recovery координирует отработку отказа для запуска серверов Linux в Azure. Но поставщики Linux могут поддерживать только те версии дистрибутивов, срок поддержки которых еще не окончился.<br/><br/> Поддерживаются только дистрибутивы Linux на основе номенклатурных ядер, которые являются частью выпуска или обновления дополнительной версии.<br/><br/> Обновление защищенных виртуальных машин с основной версией дистрибутивов Linux не поддерживается. Для обновления отключите репликацию, обновите операционную систему и включите репликацию снова.<br/><br/> [Learn more](https://support.microsoft.com/help/2941892/support-for-linux-and-open-source-technology-in-azure) about support for Linux and open-source technology in Azure.
Linux Red Hat Enterprise | 5.2 to 5.11</b><br/> 6.1 to 6.10</b> </br> 7.0, 7.1, 7.2, 7.3, 7.4, 7.5, 7.6, [7.7](https://support.microsoft.com/help/4528026/update-rollup-41-for-azure-site-recovery), [8.0](https://support.microsoft.com/help/4531426/update-rollup-42-for-azure-site-recovery) <br/> Servers running Red Hat Enterprise Linux 5.2-5.11 & 6.1-6.10 do not have [Linux Integration Services (LIS) components](https://www.microsoft.com/download/details.aspx?id=55106) pre-installed. Ensure to install the [components](https://www.microsoft.com/download/details.aspx?id=55106) before enabling replication for the machines to boot in Azure.
Linux: CentOS | 5.2 to 5.11</b><br/> 6.1 to 6.10</b><br/> 7.0 to 7.6<br/> <br/> Servers running  CentOS 5.2-5.11 & 6.1-6.10 do not have  [Linux Integration Services (LIS) components](https://www.microsoft.com/download/details.aspx?id=55106) pre-installed. Ensure to install the [components](https://www.microsoft.com/download/details.aspx?id=55106) before enabling replication for the machines to boot in Azure.
Ubuntu | Ubuntu 14.04 LTS server [(review supported kernel versions)](#ubuntu-kernel-versions)<br/><br/>Ubuntu 16.04 LTS server [(review supported kernel versions)](#ubuntu-kernel-versions) </br> Ubuntu 18.04 LTS server [(review supported kernel versions)](#ubuntu-kernel-versions)
Debian | Debian 7/Debian 8 [(review supported kernel versions)](#debian-kernel-versions)
SUSE Linux | SUSE Linux Enterprise Server 12 SP1, SP2, SP3, SP4 [(review supported kernel versions)](#suse-linux-enterprise-server-12-supported-kernel-versions)<br/> SUSE Linux Enterprise Server 11 с пакетом обновления 3 или SUSE Linux Enterprise Server 11 с пакетом обновления 4<br/> Upgrading replicated machines from SUSE Linux Enterprise Server 11 SP3 to SP4 isn't supported. To upgrade, disable replication and re-enable after the upgrade.
Oracle Linux | 6.4, 6.5, 6.6, 6.7, 6.8, 6.9, 6.10, 7.0, 7.1, 7.2, 7.3, 7.4, 7.5, 7.6, [7.7](https://support.microsoft.com/help/4531426/update-rollup-42-for-azure-site-recovery)<br/><br/> Running the Red Hat compatible kernel or Unbreakable Enterprise Kernel Release 3, 4 & 5 (UEK3, UEK4, UEK5) 


### <a name="ubuntu-kernel-versions"></a>Версии ядра Ubuntu

**Поддерживаемый выпуск** | **Версия службы Mobility Service** | **Версия ядра** |
--- | --- | --- |
14.04 LTS | [9.28][9.28 UR]| 3.13.0-24-generic to 3.13.0-170-generic,<br/>3.16.0-25-generic to 3.16.0-77-generic,<br/>3.19.0-18-generic to 3.19.0-80-generic,<br/>4.2.0-18-generic to 4.2.0-42-generic,<br/>4.4.0-21-generic to 4.4.0-148-generic,<br/>4.15.0-1023-azure to 4.15.0-1045-azure |
14.04 LTS | [9.27][9.27 UR]| 3.13.0-24-generic to 3.13.0-170-generic,<br/>3.16.0-25-generic to 3.16.0-77-generic,<br/>3.19.0-18-generic to 3.19.0-80-generic,<br/>4.2.0-18-generic to 4.2.0-42-generic,<br/>4.4.0-21-generic to 4.4.0-148-generic,<br/>4.15.0-1023-azure to 4.15.0-1045-azure |
14.04 LTS | [9.26][9.26 UR]| 3.13.0-24-generic to 3.13.0-170-generic,<br/>3.16.0-25-generic to 3.16.0-77-generic,<br/>3.19.0-18-generic to 3.19.0-80-generic,<br/>4.2.0-18-generic to 4.2.0-42-generic,<br/>4.4.0-21-generic to 4.4.0-148-generic,<br/>4.15.0-1023-azure to 4.15.0-1045-azure |
14.04 LTS | [9.25][9.25 UR]  | 3.13.0-24-generic to 3.13.0-169-generic,<br/>3.16.0-25-generic to 3.16.0-77-generic,<br/>3.19.0-18-generic to 3.19.0-80-generic,<br/>4.2.0-18-generic to 4.2.0-42-generic,<br/>4.4.0-21-generic to 4.4.0-146-generic,<br/>4.15.0-1023-azure to 4.15.0-1042-azure |
|||
16.04 LTS | [9.30](https://support.microsoft.com/help/4531426/update-rollup-42-for-azure-site-recovery) | 4.4.0-21-generic to 4.4.0-166-generic,<br/>с 4.8.0-34-generic по 4.8.0-58-generic<br/>с 4.10.0-14-generic по 4.10.0-42-generic,<br/>с 4.11.0-13-generic по 4.11.0-14-generic,<br/>с 4.13.0-16-generic по 4.13.0-45-generic,<br/>4.15.0-13-generic to 4.15.0-66-generic<br/>с 4.11.0-1009-azure по 4.11.0-1016-azure,<br/>с 4.13.0-1005-azure по 4.13.0-1018-azure. <br/>4.15.0-1012-azure to 4.15.0-1061-azure|
16.04 LTS | [9.28][9.28 UR] | 4.4.0-21-generic to 4.4.0-159-generic,<br/>с 4.8.0-34-generic по 4.8.0-58-generic<br/>с 4.10.0-14-generic по 4.10.0-42-generic,<br/>с 4.11.0-13-generic по 4.11.0-14-generic,<br/>с 4.13.0-16-generic по 4.13.0-45-generic,<br/>4.15.0-13-generic to 4.15.0-58-generic<br/>с 4.11.0-1009-azure по 4.11.0-1016-azure,<br/>с 4.13.0-1005-azure по 4.13.0-1018-azure. <br/>4.15.0-1012-azure to 4.15.0-1055-azure|
16.04 LTS | [9.27][9.27 UR] | 4.4.0-21-generic to 4.4.0-154-generic,<br/>с 4.8.0-34-generic по 4.8.0-58-generic<br/>с 4.10.0-14-generic по 4.10.0-42-generic,<br/>с 4.11.0-13-generic по 4.11.0-14-generic,<br/>с 4.13.0-16-generic по 4.13.0-45-generic,<br/>4.15.0-13-generic to 4.15.0-54-generic<br/>с 4.11.0-1009-azure по 4.11.0-1016-azure,<br/>с 4.13.0-1005-azure по 4.13.0-1018-azure. <br/>4.15.0-1012-azure to 4.15.0-1050-azure|
16.04 LTS | [9.26][9.26 UR] | 4.4.0-21-generic to 4.4.0-148-generic,<br/>с 4.8.0-34-generic по 4.8.0-58-generic<br/>с 4.10.0-14-generic по 4.10.0-42-generic,<br/>с 4.11.0-13-generic по 4.11.0-14-generic,<br/>с 4.13.0-16-generic по 4.13.0-45-generic,<br/>4.15.0-13-generic to 4.15.0-50-generic<br/>с 4.11.0-1009-azure по 4.11.0-1016-azure,<br/>с 4.13.0-1005-azure по 4.13.0-1018-azure. <br/>4.15.0-1012-azure to 4.15.0-1045-azure|
16.04 LTS | [9.25][9.25 UR] | 4.4.0-21-generic to 4.4.0-146-generic,<br/>с 4.8.0-34-generic по 4.8.0-58-generic<br/>с 4.10.0-14-generic по 4.10.0-42-generic,<br/>с 4.11.0-13-generic по 4.11.0-14-generic,<br/>с 4.13.0-16-generic по 4.13.0-45-generic,<br/>4.15.0-13-generic to 4.15.0-48-generic<br/>с 4.11.0-1009-azure по 4.11.0-1016-azure,<br/>с 4.13.0-1005-azure по 4.13.0-1018-azure. <br/>4.15.0-1012-azure to 4.15.0-1042-azure|
16.04 LTS | [9.24][9.24 UR] | 4.4.0-21-generic to 4.4.0-143-generic,<br/>с 4.8.0-34-generic по 4.8.0-58-generic<br/>с 4.10.0-14-generic по 4.10.0-42-generic,<br/>с 4.11.0-13-generic по 4.11.0-14-generic,<br/>с 4.13.0-16-generic по 4.13.0-45-generic,<br/>4.15.0-13-generic to 4.15.0-46-generic<br/>с 4.11.0-1009-azure по 4.11.0-1016-azure,<br/>с 4.13.0-1005-azure по 4.13.0-1018-azure. <br/>4.15.0-1012-azure to 4.15.0-1040-azure|
|||
18.04 LTS | [9.29](https://support.microsoft.com/help/4528026/update-rollup-41-for-azure-site-recovery) | 4.15.0-20-generic to 4.15.0-62-generic </br> 4.18.0-13-generic to 4.18.0-25-generic </br> 5.0.0-15-generic to 5.0.0-27-generic </br> 4.15.0-1009-azure to 4.15.0-1037-azure </br> 4.18.0-1006-azure to 4.18.0-1025-azure </br> 5.0.0-1012-azure to 5.0.0-1018-azure
18.04 LTS | [9.30](https://support.microsoft.com/help/4531426/update-rollup-42-for-azure-site-recovery) | 4.15.0-20-generic to 4.15.0-66-generic </br> 4.18.0-13-generic to 4.18.0-25-generic </br> 5.0.0-15-generic to 5.0.0-32-generic </br> 4.15.0-1009-azure to 4.15.0-1037-azure </br> 4.18.0-1006-azure to 4.18.0-1025-azure </br> 5.0.0-1012-azure to 5.0.0-1023-azure


### <a name="debian-kernel-versions"></a>Версии ядра Debian


**Поддерживаемый выпуск** | **Версия службы Mobility Service** | **Версия ядра** |
--- | --- | --- |
Debian 7 | [9.25][9.25 UR],[9.26][9.26 UR], [9.27][9.27 UR], [9.28][9.28 UR]| С 3.2.0-4-amd64 по 3.2.0-6-amd64, 3.16.0-0.bpo.4-amd64 |
|||
Debian 8; | [9.28][9.28 UR] | 3.16.0-4-amd64 to 3.16.0-10-amd64, 4.9.0-0.bpo.4-amd64 to 4.9.0-0.bpo.9-amd64 |
Debian 8; | [9.27][9.27 UR] | 3.16.0-4-amd64 to 3.16.0-9-amd64, 4.9.0-0.bpo.4-amd64 to 4.9.0-0.bpo.9-amd64 |
Debian 8; | [9.25][9.25 UR], [9.26][9.26 UR] | 3.16.0-4-amd64 to 3.16.0-8-amd64, 4.9.0-0.bpo.4-amd64 to 4.9.0-0.bpo.8-amd64 |

### <a name="suse-linux-enterprise-server-12-supported-kernel-versions"></a>Поддерживаемые версии ядра SUSE Linux Enterprise Server 12

**Выпуск** | **Версия службы Mobility Service** | **Версия ядра** |
--- | --- | --- |
SUSE Linux Enterprise Server 12 (SP1,SP2,SP3,SP4) | [9.28][9.28 UR] | С SP1 3.12.49-11-default по 3.12.74-60.64.40-default</br></br> SP1(LTSS) 3.12.74-60.64.45-default to 3.12.74-60.64.118-default</br></br> С SP2 4.4.21-69-default по 4.4.120-92.70-default</br></br>SP2(LTSS) 4.4.121-92.73-default to 4.4.121-92.117-default</br></br>SP3 4.4.73-5-default to 4.4.180-94.100-default</br></br>SP3 4.4.138-4.7-azure to 4.4.180-4.31-azure</br></br>SP4 4.12.14-94.41-default to 4.12.14-95.29-default</br>SP4 4.12.14-6.3-azure to 4.12.14-6.23-azure |
SUSE Linux Enterprise Server 12 (SP1,SP2,SP3,SP4) | [9.27][9.27 UR] | С SP1 3.12.49-11-default по 3.12.74-60.64.40-default</br></br> SP1(LTSS) 3.12.74-60.64.45-default to 3.12.74-60.64.115-default</br></br> С SP2 4.4.21-69-default по 4.4.120-92.70-default</br></br>SP2(LTSS) 4.4.121-92.73-default to 4.4.121-92.114-default</br></br>SP3 4.4.73-5-default to 4.4.180-94.97-default</br></br>SP3 4.4.138-4.7-azure to 4.4.180-4.31-azure</br></br>SP4 4.12.14-94.41-default to 4.12.14-95.19-default</br>SP4 4.12.14-6.3-azure to 4.12.14-6.15-azure |
SUSE Linux Enterprise Server 12 (SP1,SP2,SP3,SP4) | [9.26][9.26 UR] | С SP1 3.12.49-11-default по 3.12.74-60.64.40-default</br></br> SP1(LTSS) 3.12.74-60.64.45-default to 3.12.74-60.64.110-default</br></br> С SP2 4.4.21-69-default по 4.4.120-92.70-default</br></br>SP2(LTSS) 4.4.121-92.73-default to 4.4.121-92.109-default</br></br>SP3 4.4.73-5-default to 4.4.178-94.91-default</br></br>SP3 4.4.138-4.7-azure to 4.4.178-4.28-azure</br></br>SP4 4.12.14-94.41-default to 4.12.14-95.16-default</br>SP4 4.12.14-6.3-azure to 4.12.14-6.9-azure |
SUSE Linux Enterprise Server 12 (SP1,SP2,SP3,SP4) | [9.25][9.25 UR] | С SP1 3.12.49-11-default по 3.12.74-60.64.40-default</br></br> С SP1(LTSS) 3.12.74-60.64.45-default по 3.12.74-60.64.107-default</br></br> С SP2 4.4.21-69-default по 4.4.120-92.70-default</br></br>SP2(LTSS) 4.4.121-92.73-default to 4.4.121-92.104-default</br></br>SP3 4.4.73-5-default to 4.4.176-94.88-default</br></br>SP3 4.4.138-4.7-azure to 4.4.176-4.25-azure</br></br>SP4 4.12.14-94.41-default to 4.12.14-95.13-default</br>SP4 4.12.14-6.3-azure to 4.12.14-6.9-azure |

## <a name="linux-file-systemsguest-storage"></a>Файловые системы Linux и гостевое хранилище

**Компонент** | **Поддерживаются**
--- | ---
Файловые системы | Ext3, ext4, XFS
Диспетчер томов | - LVM is supported.<br/> - /boot on LVM is supported from [Update Rollup 31](https://support.microsoft.com/help/4478871/) (version 9.20 of the Mobility service) onwards. It isn't supported in earlier Mobility service versions.<br/> - Multiple OS disks aren't supported.
Паравиртуализированные запоминающие устройства | Устройства, экспортированные с помощью паравиртуализированных драйверов, не поддерживаются.
Блочные устройства ввода-вывода с несколькими очередями | Не поддерживается.
Физические серверы с контроллером хранилища HP CCISS | Не поддерживается.
Соглашение об именовании точки устройства/подключения | Имя устройства или точки подключения должно быть уникальным.<br/> Ensure that no two devices/mount points have case-sensitive names. For example naming devices for the same VM as *device1* and *Device1* isn't supported.
Каталоги | If you're running a version of the Mobility service earlier than version 9.20 (released in [Update Rollup 31](https://support.microsoft.com/help/4478871/)), then these restrictions apply:<br/><br/> - These directories (if set up as separate partitions/file-systems) must be on the same OS disk on the source server: /(root), /boot, /usr, /usr/local, /var, /etc.</br> - The /boot directory should be on a disk partition and not be an LVM volume.<br/><br/> From version 9.20 onwards, these restrictions don't apply. 
Каталог загрузок | - Boot disks mustn't be in GPT partition format. This is an Azure architecture limitation. GPT disks are supported as data disks.<br/><br/> Multiple boot disks on a VM aren't supported<br/><br/> - /boot on an LVM volume across more than one disk isn't supported.<br/> - A machine without a boot disk can't be replicated.
Требования к свободному месту| 2 ГБ в разделе /root <br/><br/> 250 МБ в папке установки
XFSv5 | XFSv5 features on XFS file systems, such as metadata checksum, are supported (Mobility service version 9.10 onwards).<br/> Используйте служебную программу xfs_info, чтобы проверить системный блок XFS для раздела. If `ftype` is set to 1, then XFSv5 features are in use.
BTRFS | BTRFS is supported from [Update Rollup 34](https://support.microsoft.com/help/4490016) (version 9.22 of the Mobility service) onwards. BTRFS isn't supported if:<br/><br/> - The BTRFS file system subvolume is changed after enabling protection.</br> - The BTRFS file system is spread over multiple disks.</br> - The BTRFS file system supports RAID.

## <a name="vmdisk-management"></a>Управление виртуальной машиной и диском

**Действие** | **Дополнительные сведения**
--- | ---
Изменение размера диска на реплицируемой виртуальной машине | Поддерживается.
Добавление диска к реплицируемой виртуальной машине | Не поддерживается.<br/> Disable replication for the VM, add the disk, and then re-enable replication.

## <a name="network"></a>Network

**Компонент** | **Поддерживаются**
--- | ---
Объединение сетевых карт в сети узла | Поддерживается для виртуальных машин VMware. <br/><br/>Не поддерживается для репликации физического компьютера.
Виртуальная локальная сеть узла | Да.
IPv4-адрес сети узла | Да.
IPv6-адрес сети узла | Нет.
Объединение гостевых сетевых карт в сети гостевой системы или сервера | Нет.
IPv4-адрес cети клиента или сервера | Да.
IPv6-адрес cети клиента или сервера | Нет.
Статический IP-адрес (Windows) сети клиента или сервера | Да.
Статический IP-адрес (Linux) cети клиента или сервера | Да. <br/><br/>Виртуальные машины настроены для использования DHCP при восстановлении размещения.
Несколько сетевых адаптеров cети клиента или сервера | Да.


## <a name="azure-vm-network-after-failover"></a>Сеть виртуальной машины Azure (после отработки отказа)

**Компонент** | **Поддерживаются**
--- | ---
Azure ExpressRoute | ДА
Внутренний балансировщик нагрузки | ДА
Внешний балансировщик нагрузки | ДА
Azure Traffic Manager | ДА
Несколько сетевых адаптеров | ДА
Обзор зарезервированных IP-адресов | ДА
IPv4 | ДА
Сохранение исходного IP-адреса | ДА
Конечные точки служб для виртуальной сети Azure<br/> | ДА
Ускорение работы в сети | Нет

## <a name="storage"></a>Storage
**Компонент** | **Поддерживаются**
--- | ---
Динамический диск | OS disk must be a basic disk. <br/><br/>Диски данных могут быть динамическими дисками.
Конфигурация дисков Docker | Нет
NFS узла | Да для VMware<br/><br/> Нет для физических серверов
SAN узла (ISCSI или FC) | ДА
vSAN узла | Да для VMware<br/><br/> Недоступно для физических серверов.
Операции многопутевого ввода-вывода (MPIO) для узла | Да — протестировано с помощью Microsoft DSM, EMC PowerPath 5.7 SP4 и EMC PowerPath DSM для CLARiiON
Виртуальные тома узла (VVol) | Да для VMware<br/><br/> Недоступно для физических серверов.
VMDK клиента или сервера | ДА
Общий диск кластера клиента или сервера | Нет
Зашифрованный диск клиента или сервера | Нет
NFS клиента или сервера | Нет
Guest/server iSCSI | For Migration - Yes<br/>For Disaster Recovery - No, iSCSI will failback as an attached disk to the VM
SMB 3.0 клиента или сервера | Нет
RDM клиента или сервера | ДА<br/><br/> Недоступно для физических серверов.
Диск размером более 1 ТБ для клиента или сервера | Yes, disk must be larger than 1024 MB<br/><br/>Up to 8,192 GB when replicating to managed disks (9.26 version onwards)<br></br> Up to 4,095 GB when replicating to storage accounts
Диск клиента или сервера с физическими и логическими секторами с размером в 4 КБ | Нет
Guest/server disk with 4K logical and 512-bytes physical sector size | Нет
Том с чередующимся диском размером более 4 ТБ для гостевой системы или сервера <br/><br/>Управление логическими томами (LVM)| ДА
Дисковые пространства для гостя или сервера | Нет
"Горячее" добавление и удаление дисков для гостя или сервера | Нет
Исключение диска для гостя или сервера | ДА
Поддержка операций многопутевого ввода-вывода (MPIO) для гостевой системы или сервера | Нет
Guest/server GPT partitions | Five partitions are supported from [Update Rollup 37](https://support.microsoft.com/help/4508614/) (version 9.25 of the Mobility service) onwards. Ранее поддерживалось четыре раздела.
ReFS | Resilient File System is supported with Mobility service version 9.23 or higher
Guest/server EFI/UEFI boot | - Supported for Windows Server 2012 or later, SLES 12 SP4 and RHEL 8.0 with mobility agent version 9.30 onwards<br/> - Secure UEFI boot type is not supported. 

## <a name="replication-channels"></a>Replication channels

|**Type of replication**   |**Поддерживаются**  |
|---------|---------|
|Offloaded Data Transfers  (ODX)    |       Нет  |
|Автономное заполнение        |   Нет      |
| Устройство Azure Data Box | Нет

## <a name="azure-storage"></a>Хранилище Azure

**Компонент** | **Поддерживаются**
--- | ---
Локально избыточное хранилище | ДА
Гео-избыточное хранилище | ДА
Геоизбыточное хранилище с доступом для чтения | ДА
"Холодное" хранилище | Нет
"Горячее" хранилище| Нет
Блочные BLOB-объекты | Нет
Encryption-at-rest (SSE)| ДА
Encryption-at-rest (CMK)| Нет
Хранилище класса Premium | ДА
Служба импорта и экспорта | Нет
Azure Storage firewalls for VNets | Да.<br/> Configured on target storage/cache storage account (used to store replication data).
General-purpose v2 storage accounts (hot and cool tiers) | Yes (Transaction costs are substantially higher for V2 compared to V1)

## <a name="azure-compute"></a>Служба вычислений Azure

**Компонент** | **Поддерживаются**
--- | ---
Группы доступности | ДА
Зоны доступности | Нет
Концентратор | ДА
Управляемые диски | ДА

## <a name="azure-vm-requirements"></a>Требования для виртуальных машин Azure

On-premises VMs replicated to Azure must meet the Azure VM requirements summarized in this table. When Site Recovery runs a prerequisites check for replication, the check will fail if some of the requirements aren't met.

**Компонент** | **Требования** | **Дополнительные сведения**
--- | --- | ---
Операционная система на виртуальной машине | [Поддерживаемые операционные системы для реплицируемых виртуальных машин](#replicated-machines) | Если не поддерживается, проверка завершается ошибкой.
Архитектура операционной системы на виртуальной машине | 64-разрядная | Если не поддерживается, проверка завершается ошибкой.
Размер диска операционной системы | До 2048 ГБ | Если не поддерживается, проверка завершается ошибкой.
Число дисков операционной системы | 1 | Если не поддерживается, проверка завершается ошибкой.
Число дисков с данными | 64 ГБ и меньше | Если не поддерживается, проверка завершается ошибкой.
Размер диска данных | Up to 8,192 GB when replicating to managed disk (9.26 version onwards)<br></br>Up to 4,095 GB when replicating to storage account| Если не поддерживается, проверка завершается ошибкой.
Сетевые адаптеры | Поддерживаются несколько адаптеров. |
Общий виртуальный жесткий диск | Не поддерживается. | Если не поддерживается, проверка завершается ошибкой.
Диск FC | Не поддерживается. | Если не поддерживается, проверка завершается ошибкой.
BitLocker | Не поддерживается. | Прежде чем включать репликацию для компьютера, необходимо отключить BitLocker. |
имя виртуальной машины; | От 1 до 63 символов.<br/><br/> при этом допустимы только буквы, цифры и дефисы<br/><br/> Имя компьютера должно начинаться и заканчиваться буквой или цифрой. |  Обновите значение в свойствах компьютера в службе Site Recovery.

## <a name="resource-group-limits"></a>Resource group limits

To understand the number of virtual machines that can be protected under a single resource group, refer to the article on [subscription limits and quotas](https://docs.microsoft.com/azure/azure-subscription-service-limits#resource-group-limits)

## <a name="churn-limits"></a>Churn limits

В таблице ниже приведены ограничения Azure Site Recovery. 
- These limits are based on our tests, but don't cover all possible app I/O combinations.
- Фактические результаты зависят от сочетания операций ввода-вывода приложения.
- For best results, we strongly recommend that you run the [Deployment Planner tool](site-recovery-deployment-planner.md), and perform extensive application testing using test failovers to get the true performance picture for your app.

**Replication target** | **Средний размер ввода-вывода исходного диска** |**Средняя скорость обработки данных исходного диска** | **Общий размер обработанных данных на исходном диске за день**
---|---|---|---
Хранилище уровня "Стандартный" | 8 КБ | 2 МБ/с | 168 ГБ на диск
Диск P10 или P15 класса Premium | 8 КБ  | 2 МБ/с | 168 ГБ на диск
Диск P10 или P15 класса Premium | 16 КБ | 4 МБ/с |  336 ГБ на диск
Диск P10 или P15 класса Premium | 32 КБ или выше | 8 МБ/с | 672 ГБ на диск
Диск P20, P30, P40 или P50 класса Premium | 8 КБ    | 5 МБ/с | 421 ГБ на диск
Диск P20, P30, P40 или P50 класса Premium | 16 КБ или выше |20 MB/s | 1684 GB per disk


**Исходная скорость обработки данных** | **Максимальное ограничение**
---|---
Пиковая скорость обработки данных на всех дисках виртуальной машины | 54 МБ/с
Максимальный объем обработки данных, поддерживаемый сервером обработки | 2 ТБ

- В указанных средних значениях предполагается 30-процентное перекрытие операций ввода-вывода.
- В зависимости от коэффициента перекрытия, размера записи и фактической рабочей нагрузки ввода-вывода Site Recovery может обрабатывать более высокую пропускную способность.
- These numbers assume a typical backlog of approximately five minutes. то есть после передачи выполняется обработка данных, а созданная точка восстановления составляет 5 минут.

## <a name="vault-tasks"></a>Задачи хранилища

**Действие** | **Поддерживаются**
--- | ---
Перемещение хранилища между группами ресурсов | Нет
Move vault within and across subscriptions | Нет
Перемещение хранилищ, сетей, виртуальных машин Azure между группами ресурсов | Нет
Move storage, network, Azure VMs within and across subscriptions. | Нет


## <a name="obtain-latest-components"></a>Obtain latest components

**Имя** | **Описание** | **Дополнительные сведения**
--- | --- | ---
Сервер конфигурации | Installed on-premises.<br/> Coordinates communications between on-premises VMware servers or physical machines, and Azure. | - [Learn about](vmware-physical-azure-config-process-server-overview.md) the configuration server.<br/> - [Learn about](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server) upgrading to the latest version.<br/> - [Learn about](vmware-azure-deploy-configuration-server.md) setting up the configuration server. 
Сервер обработки | По умолчанию устанавливается на сервере конфигурации.<br/> Receives replication data, optimizes it with caching, compression, and encryption, and sends it to Azure.<br/> As your deployment grows, you can add additional process servers to handle larger volumes of replication traffic. | - [Learn about](vmware-physical-azure-config-process-server-overview.md) the process server.<br/> - [Learn about](vmware-azure-manage-process-server.md#upgrade-a-process-server) upgrading to the latest version.<br/> - [Learn about](vmware-physical-large-deployment.md#set-up-a-process-server) setting up scale-out process servers.
Служба Mobility Service | Installed on VMware VM or physical servers you want to replicate.<br/> Coordinates replication between on-premises VMware servers/physical servers and Azure.| - [Learn about](vmware-physical-mobility-service-overview.md) the Mobility service.<br/> - [Learn about](vmware-physical-manage-mobility-service.md#update-mobility-service-from-azure-portal) upgrading to the latest version.<br/> 



## <a name="next-steps"></a>Дальнейшие действия
[Узнайте, как](tutorial-prepare-azure.md) подготовить Azure для аварийного восстановления виртуальных машин VMware.

[9.28 UR]: https://support.microsoft.com/en-in/help/4521530/update-rollup-40-for-azure-site-recovery
[9.27 UR]: https://support.microsoft.com/en-in/help/4517283/update-rollup-39-for-azure-site-recovery
[9.26 UR]: https://support.microsoft.com/en-in/help/4513507/update-rollup-38-for-azure-site-recovery
[9.25 UR]: https://support.microsoft.com/en-in/help/4508614/update-rollup-37-for-azure-site-recovery
[9.24 UR]: https://support.microsoft.com/en-in/help/4503156
[9.23 UR]: https://support.microsoft.com/en-in/help/4494485/update-rollup-35-for-azure-site-recovery
[9.22 UR]: https://support.microsoft.com/help/4489582/update-rollup-33-for-azure-site-recovery
[9.21 UR]: https://support.microsoft.com/help/4485985/update-rollup-32-for-azure-site-recovery
[9.20 UR]: https://support.microsoft.com/help/4478871/update-rollup-31-for-azure-site-recovery
[9.19 UR]: https://support.microsoft.com/help/4468181/azure-site-recovery-update-rollup-30
[9.18 UR]: https://support.microsoft.com/help/4466466/update-rollup-29-for-azure-site-recovery
