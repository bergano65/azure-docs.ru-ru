---
title: Архитектуры решений с использованием Azure NetApp Files | Документация Майкрософт
description: Содержит ссылки на рекомендации по архитектуре решений с помощью Azure NetApp Files.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/13/2020
ms.author: b-juche
ms.openlocfilehash: 79ad95179f4ad6d332a848e59ca341b8a9f90b1f
ms.sourcegitcommit: ef055468d1cb0de4433e1403d6617fede7f5d00e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/16/2020
ms.locfileid: "88258232"
---
# <a name="solution-architectures-using-azure-netapp-files"></a>Архитектура решений, использующих Azure NetApp Files
В этой статье содержатся ссылки на рекомендации, которые помогут вам понять архитектуру решения для использования Azure NetApp Files.  

На следующей диаграмме перечислены категории архитектур решений, которые Azure NetApp Files предлагает:

![Категории архитектуры решения](../media/azure-netapp-files/solution-architecture-categories.png)

## <a name="linux-oss-apps-and-database-solutions"></a>ОС Linux и решения для баз данных

В этом разделе содержатся ссылки на решения для приложений и баз данных ОС Linux. 

### <a name="oracle"></a>Oracle;

* [Рекомендации по развертыванию Oracle в Azure с помощью Azure NetApp Files](https://www.netapp.com/us/media/tr-4780.pdf)
* [Образы виртуальных машин Oracle и их развертывание на Microsoft Azure: параметры конфигурации общего хранилища](https://docs.microsoft.com/azure/virtual-machines/workloads/oracle/oracle-vm-solutions#shared-storage-configuration-options)
* [Преимущества использования Azure NetApp Files с Oracle Database](solutions-benefits-azure-netapp-files-oracle-database.md)

## <a name="windows-apps-and-sql-server-solutions"></a>Приложения Windows и решения SQL Server

В этом разделе содержатся ссылки на приложения Windows и SQL Server решения.

### <a name="file-sharing-and-global-file-caching"></a>Общий доступ к файлам и глобальное кэширование файлов

* [Создаете собственную службу Azure NFS? Решение файловые ресурсы Linux в облако](https://cloud.netapp.com/blog/ma-anf-blg-build-your-own-linux-nfs-file-shares)
* [Развертывание глобального кэша файлов/Azure NetApp Files](https://youtu.be/91LKb1qsLIM)

### <a name="sql-server"></a>SQL Server

* [Развертывание SQL Server через SMB с Azure NetApp Files](https://www.youtube.com/watch?v=x7udfcYbibs)
* [Развертывание отказоустойчивого кластера SQL Server Always-on по протоколу SMB с Azure NetApp Files](https://www.youtube.com/watch?v=zuNJ5E07e8Q)
* [Развертывание групп доступности Always-On с помощью Azure NetApp Files](https://www.youtube.com/watch?v=y3VQmzzeyvc)

## <a name="sap-on-azure-solutions"></a>Решения SAP в Azure

В этом разделе содержатся ссылки на решения SAP в Azure. 

### <a name="generic-sap-and-sap-netweaver"></a>Универсальные SAP и SAP NetWeaver 

* [Приложения SAP на Microsoft Azure с помощью Azure NetApp Files](https://www.netapp.com/us/media/tr-4746.pdf)
* [Обеспечение высокого уровня доступности виртуальных машин Azure для SAP NetWeaver на SUSE Linux Enterprise Server с помощью Azure NetApp Files для приложений SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files)
* [Высокий уровень доступности SAP NetWeaver на виртуальных машинах Azure на Red Hat Enterprise Linux с Azure NetApp Files для приложений SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files)
* [Высокий уровень доступности SAP NetWeaver на виртуальных машинах Azure в Windows с Azure NetApp Files (SMB) для приложений SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-windows-netapp-files-smb)
* [Высокий уровень доступности SAP NetWeaver на виртуальных машинах Azure на Red Hat Enterprise Linux для многоид безопасности приложений SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-multi-sid)

### <a name="sap-hana"></a>SAP HANA 

* [Конфигурации хранилища виртуальных машин SAP HANA в Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage)
* [SAP HANA горизонтальное масштабирование с помощью резервного узла на виртуальных машинах Azure с Azure NetApp Files на SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse)
* [SAP HANA горизонтальное масштабирование с помощью резервного узла на виртуальных машинах Azure с Azure NetApp Files на Red Hat Enterprise Linux](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-rhel)

### <a name="sap-tech-community-and-blog-posts"></a>Технические сообщества SAP и записи в блоге 

* [Azure NetApp Files — резервное копирование SAP HANA в секундах](https://blog.netapp.com/azure-netapp-files-sap-hana-backup-in-seconds/)
* [Azure NetApp Files — восстановление базы данных HANA из резервной копии моментальных снимков](https://blog.netapp.com/azure-netapp-files-backup-sap-hana)
* [Azure NetApp Files — SAP HANA разгрузка резервной копии с синхронизацией в облаке](https://blog.netapp.com/azure-netapp-files-sap-hana)
* [Ускорение копирования SAP HANA системы с помощью Azure NetApp Files](https://blog.netapp.com/sap-hana-faster-using-azure-netapp-files/)
* [Облачные тома ONTAP и Azure NetApp Files: упрощена миграция SAP HANA системы](https://blog.netapp.com/cloud-volumes-ontap-and-azure-netapp-files-sap-hana-system-migration-made-easy/)

## <a name="virtual-desktop-infrastructure-solutions"></a>Решения инфраструктуры виртуальных рабочих столов

В этом разделе содержатся ссылки на решения инфраструктуры виртуальных рабочих столов.

### <a name="windows-virtual-desktop"></a>Виртуальный рабочий стол Windows

* [Преимущества использования Azure NetApp Files с виртуальным рабочим столом Windows](solutions-windows-virtual-desktop.md)
* [Варианты хранения для контейнеров профилей Фслогикс в виртуальном рабочем столе Windows](https://docs.microsoft.com/azure/virtual-desktop/store-fslogix-profile#azure-platform-details)
* [Создание контейнера профиля Фслогикс для пула узлов с помощью Azure NetApp Files](https://docs.microsoft.com/azure/virtual-desktop/create-fslogix-profile-container)
* [Виртуальный рабочий стол Windows в масштабе предприятия](https://docs.microsoft.com/azure/architecture/example-scenario/wvd/windows-virtual-desktop)

## <a name="hpc-solutions"></a>Высокопроизводительные решения

В этом разделе содержатся ссылки на решения для высокопроизводительных вычислений (HPC). 

### <a name="generic-hpc"></a>Универсальный HPC

* [Azure NetApp Files: максимально эффективное получение из облачного хранилища](https://cloud.netapp.com/hubfs/Resources/ANF%20PERFORMANCE%20TESTING%20IN%20TEMPLATE.pdf)
* [Выполнение рабочих нагрузок MPI с помощью пакетной службы Azure и Azure NetApp Files](https://azure.microsoft.com/resources/run-mpi-workloads-with-azure-batch-and-azure-netapp-files/)
* [Циклическое облако Azure: среды Циклеклауд HPC на Azure NetApp Files](https://docs.microsoft.com/azure/cyclecloud/overview)

### <a name="oil-and-gas"></a>нефть и газ;

* [Высокопроизводительные вычислительные системы (HPC): топливо и газ в Azure](https://techcommunity.microsoft.com/t5/azure-global/high-performance-computing-hpc-oil-and-gas-in-azure/ba-p/824926)
* [Запуск программного обеспечения моделирования молекулярного в Azure](https://docs.microsoft.com/azure/architecture/example-scenario/infrastructure/reservoir-simulation)

### <a name="electronic-design-automation-eda"></a>Автоматизация разработки электронных макетов (EDA)

* [Преимущества использования Azure NetApp Files для автоматизации работы с электронными макетами](solutions-benefits-azure-netapp-files-electronic-design-automation.md)
* [Azure Циклеклауд: EDA HPC Lab с Azure NetApp Files](https://github.com/Azure/cyclecloud-hands-on-labs/blob/master/EDA/README.md)

### <a name="analytics"></a>Analytics

* [Azure NetApp Files: Новая Общая файловая система, используемая в сетке SAS на Microsoft Azure](https://communities.sas.com/t5/Architecture/Azure-NetApp-Files-A-new-shared-file-system-to-use-with-SAS-Grid/m-p/606978)

## <a name="azure-platform-services-solutions"></a>Решения для служб платформы Azure

В этом разделе приводятся решения для служб платформы Azure. 

### <a name="azure-kubernetes-services-and-kubernetes"></a>Azure Kubernetes Services и Kubernetes

* [Интеграция Azure NetApp Files со службой Kubernetes Azure](https://docs.microsoft.com/azure/aks/azure-netapp-files)
* [Kubernetes производительность в Azure с помощью Azure NetApp Files](https://cloud.netapp.com/blog/ma-anf-blg-configure-kubernetes-openshift)
* [Trident — Orchestrator Storage для контейнеров](https://netapp-trident.readthedocs.io/en/stable-v20.04/kubernetes/operations/tasks/backends/anf.html)

### <a name="azure-batch"></a>Пакетная служба Azure

* [Выполнение рабочих нагрузок MPI с помощью пакетной службы Azure и Azure NetApp Files](https://azure.microsoft.com/resources/run-mpi-workloads-with-azure-batch-and-azure-netapp-files/)
 
