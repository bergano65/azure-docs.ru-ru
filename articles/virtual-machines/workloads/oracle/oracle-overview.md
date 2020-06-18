---
title: Решения Oracle в Microsoft Azure | Документация Майкрософт
description: Узнайте о вариантах развертывания приложений и решений Oracle на Microsoft Azure, включая работу полностью в инфраструктуре Azure или использование межоблачного соединения с облачной инфраструктурой Oracle (Oracle Cloud Infrastructure, OCI).
services: virtual-machines-linux
documentationcenter: ''
author: BorisB2015
tags: azure-resource-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 05/12/2020
ms.author: borisb
ms.openlocfilehash: 649d96a158682752e0d4a31bf7ec73eb7c442f0f
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/19/2020
ms.locfileid: "83660547"
---
# <a name="overview-of-oracle-applications-and-solutions-on-azure"></a>Обзор приложений и решений Oracle в Azure

В этой статье описаны возможности для запуска решений Oracle с помощью инфраструктуры Azure. См. также подробные сведения о [приложениях Azure для WebLogic Server](oracle-weblogic.md), [образах виртуальных машин Oracle](oracle-vm-solutions.md), доступных в Azure Marketplace, а также о возможности [взаимодействия между Azure и облачной инфраструктурой Oracle (Oracle Cloud Infrastructure, OCI)](oracle-oci-overview.md).

## <a name="oracle-databases-on-azure-infrastructure"></a>Базы данных Oracle в инфраструктуре Azure

Следующие базы данных Oracle могут быть запущены в инфраструктуре Azure с помощью Oracle Database и образов Oracle Linux, доступных в Azure Marketplace:

* Oracle Database 12.1, 12.2 и 18.3 Enterprise Edition 

* Oracle Database 12.1, 12.2 и 18.3 Standard Edition 

Вы можете установить Oracle Database на образе, отличном от Oracle Linux, доступном в Azure, а также создать решение на основе пользовательского образа, созданного вами с нуля в Azure или загруженного из вашей локальной среды.

При необходимости можно подключить несколько дисков и увеличить производительность базы данных, установив систему автоматического управления хранением Oracle (Oracle Automatic Storage Management, ASM).

## <a name="weblogic-server-with-azure-service-integrations"></a>Интеграция WebLogic Server со службами Azure

Выбирайте из множества приложений Azure для WebLogic Server, чтобы быстрее пройти свою дорогу в облака.  Доступны несколько предварительно настроенных интеграций со службами Azure, включая базу данных, Шлюз приложений Azure и Azure Active Directory.

## <a name="applications-on-oracle-linux-and-weblogic-server"></a>Приложения на Oracle Linux и WebLogic Server

Запускайте корпоративные приложения в Azure на поддерживаемых операционных системах Oracle. Следующие образы виртуальных машин доступны в Azure Marketplace:

* Oracle WebLogic Server 12.1.2

* Oracle Linux с ядром Unbreakable Enterprise Kernel (UEK) 6.8, 6.9, 6.10, 7.3, 7.4, 7.5 и 7.6 

## <a name="high-availability-and-disaster-recovery-options"></a>Высокая доступность и аварийное восстановление

* Настройте [Oracle Data Guard](https://docs.oracle.com/cd/B19306_01/server.102/b14239/concepts.htm#g1049956), [Active Data Guard с FSFO](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/dgbkr/index.html), [Sharding](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/admin/sharding-overview.html) или [Golden Gate](https://www.oracle.com/middleware/technologies/goldengate.html) в инфраструктуре Azure в сочетании с [Зонами доступности](../../../availability-zones/az-overview.md) для обеспечения высокой доступности внутри региона. Вы также можете включить в эти конфигурации несколько регионов Azure для повышения доступности и расширения возможностей аварийного восстановления.

* Используйте [Azure Site Recovery](../../../site-recovery/site-recovery-overview.md) для оркестрации и управления аварийным восстановлением виртуальных машин Oracle Linux в Azure и своих локальных или физических серверов. 

* Включите Oracle Real Application Clusters (RAC) в Azure, используя [решение Azure VMware](https://docs.microsoft.com/azure/vmware-cloudsimple/oracle-real-application-clusters/) или [FlashGrid SkyCluster](https://www.flashgrid.io/oracle-rac-in-azure/).

## <a name="backup-oracle-workloads"></a>Резервное копирование рабочих нагрузок Oracle

* Выполняйте резервное копирование виртуальных машин Oracle с помощью [Azure Backup](https://docs.microsoft.com/azure/backup/backup-overview)

* Выполняйте резервное копирование Oracle Database при помощи Oracle RMAN. Для повышенной надежности подключите [высокоизбыточное хранилище BLOB-объектов Azure](https://docs.microsoft.com/azure/storage/common/storage-redundancy), используя [Azure Blob Fuse](https://docs.microsoft.com/azure/storage/blobs/storage-how-to-mount-container-linux), и запишите туда резервные копии, созданные RMAN.

## <a name="integration-of-azure-with-oci"></a>Интеграция Azure с OCI

Запускайте приложения Oracle в инфраструктуре Azure, подключенной к серверным базам данных в облачной инфраструктуре Oracle (Oracle Cloud Infrastructure, OCI). Это решение предоставляет следующие возможности. 

* **Сетевые соединения между облаками** — используйте прямое взаимодействие между Azure ExpressRoute и Oracle FastConnect, чтобы установить конфиденциальное соединение с высокой пропускной способностью и низкой задержкой между приложением и уровнем базы данных.
* **Интегрированная идентификация** — настройте федеративные удостоверения между Azure AD и Oracle IDCS, чтобы создать единый источник удостоверений для обоих решений. Включите единый вход для управления ресурсами в OCI и Azure.

### <a name="deploy-oracle-applications-on-azure"></a>Развертывание приложений Oracle в Azure

Используйте шаблоны Terraform для настройки инфраструктуры Azure и установки приложений Oracle. 

> [!IMPORTANT]
> К маю 2020 г. следующие приложения будут сертифицированы Oracle для запуска в Azure при использовании соединения между облаками Azure и Oracle Cloud:

* E-Business Suite
* JD Edwards EnterpriseOne
* PeopleSoft
* Приложения Oracle Retail
* Oracle Hyperion Financial Management

Развертывайте в Azure также пользовательские приложения, которые подключаются к OCI и службам Azure.

### <a name="set-up-oracle-databases-in-oci"></a>Размещение баз данных Oracle в OCI

Используйте облачные службы баз данных Oracle Database Cloud Services (Autonomous Database, RAC, Exadata, DBaaS, Single Node) в сочетании с приложениями Oracle, работающими в Azure. Дополнительные сведения о [вариантах размещения баз данных в OCI](https://docs.cloud.oracle.com/iaas/Content/Database/Concepts/databaseoverview.htm). 
 

## <a name="licensing"></a>Лицензирование

Развертывание приложений Oracle в Azure основано на модели "с использованием собственной лицензии". Вы должны иметь соответствующую лицензию на использование программного обеспечения Oracle, а также соглашение о текущей поддержке. Oracle гарантирует перемещение лицензий из локальной среды в Azure. См. раздел [вопросы и ответы](https://www.oracle.com/cloud/technologies/oracle-azure-faq.html) по сотрудничеству Oracle и Azure.

## <a name="next-steps"></a>Дальнейшие действия

* Узнайте больше о [приложениях Azure для WebLogic Server](oracle-weblogic.md) и поддерживаемых ими интеграциях со службами Azure.

* Узнайте больше о развертывании [образов виртуальных машин Oracle](oracle-vm-solutions.md) в инфраструктуре Azure.

* Узнайте больше о том, как [подключить Azure к OCI](oracle-oci-overview.md).

* Ознакомьтесь с [обзорной сессией об Oracle в Azure](https://myignite.techcommunity.microsoft.com/sessions/82915), записанной на Ignite 2019. 
