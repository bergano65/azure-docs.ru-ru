---
title: Решения Oracle в Microsoft Azure | Документация Майкрософт
description: Узнайте о вариантах развертывания приложений и решений Oracle на Microsoft Azure, включая работу полностью в инфраструктуре Azure или использование межоблачного подключения к облачной инфраструктуре Oracle (OCI).
services: virtual-machines-linux
documentationcenter: ''
author: romitgirdhar
manager: gwallace
tags: azure-resource-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 06/04/2019
ms.author: rogirdh
ms.openlocfilehash: 6ab41182d59f8c96edfca4854aad5f9a13f53436
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/04/2019
ms.locfileid: "74806615"
---
# <a name="overview-of-oracle-applications-and-solutions-on-azure"></a>Общие сведения о приложениях и решениях Oracle в Azure

В этой статье представлены возможности для запуска решений Oracle с помощью инфраструктуры Azure. См. также подробные сведения о доступных [образах виртуальных машин Oracle](oracle-vm-solutions.md) в Azure Marketplace и возможности предварительной версии для [соединения Azure с облачной инфраструктурой Oracle (OCI)](oracle-oci-overview.md).

## <a name="oracle-databases-on-azure-infrastructure"></a>Базы данных Oracle в инфраструктуре Azure

Запуск баз данных Oracle в инфраструктуре Azure с помощью Oracle Database для образов Oracle Linux, доступных в Azure Marketplace:

* Oracle Database 12,1, 12,2 и 18,3 Enterprise Edition 

* Oracle Database 12,1, 12,2 и 18,3 Standard Edition 

Вы также можете настроить Oracle Database на неOracle Linuxном образе, доступном в Azure, создать решение на основе пользовательского образа, созданного с нуля в Azure, или отправить пользовательский образ из локальной среды.

При необходимости настройте с несколькими подключенными дисками и увеличьте производительность базы данных, установив автоматическое управление хранилищем (ASM) Oracle.

## <a name="applications-on-oracle-linux-and-weblogic-server"></a>Приложения на Oracle Linux и сервере серверной логической логики

Запускайте корпоративные приложения в Azure в поддерживаемых операционных системах Oracle. В Azure Marketplace доступны следующие образы:

* Сервер 12.1.2 для Oracle.

* Oracle Linux с неповрежденным ядром Enterprise (UEK) 6,8, 6,9, 6,10, 7,3, 7,4, 7,5 и 7,6 

## <a name="high-availability-and-disaster-recovery-options"></a>Параметры высокого уровня доступности и аварийного восстановления

* Настройте [Oracle Data Guard](https://docs.oracle.com/cd/B19306_01/server.102/b14239/concepts.htm#g1049956), [Active Data Guard с фсфо](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/dgbkr/index.html), [сегментированием](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/admin/sharding-overview.html) или [золотым шлюзом](https://www.oracle.com/middleware/technologies/goldengate.html) в инфраструктуре Azure в сочетании с [зоны доступности](../../../availability-zones/az-overview.md) для обеспечения высокой доступности в регионе. Вы также можете настроить эти конфигурации в нескольких регионах Azure для повышения доступности и аварийного восстановления.

* Используйте [Azure Site Recovery](../../../site-recovery/site-recovery-overview.md) , чтобы управлять аварийным восстановлением для виртуальных машин Oracle Linux и локальных или физических серверов, а также осуществлять управление ими. 

* Включите кластеры реальных приложений (RAC) Oracle в Azure с помощью [решения VMware для Azure](https://docs.azure.cloudsimple.com/oracle-rac/) или [FlashGrid скиклустер](https://www.flashgrid.io/oracle-rac-in-azure/).

## <a name="backup-oracle-workloads"></a>Резервные рабочие нагрузки Oracle

* Резервное копирование виртуальных машин Oracle с помощью [Azure Backup](https://docs.microsoft.com/azure/backup/backup-overview)

* Создайте резервную копию Oracle Database с помощью Oracle RMAN и, при необходимости, используйте [предохранитель BLOB-объекта Azure](https://docs.microsoft.com/azure/storage/blobs/storage-how-to-mount-container-linux) , чтобы подключить [высокоредудантную учетную запись хранилища больших двоичных объектов Azure](https://docs.microsoft.com/azure/storage/common/storage-redundancy) и записать в нее резервные копии RMAN для повышения устойчивости.

## <a name="integration-of-azure-with-oci-preview"></a>Интеграция Azure с OCI (Предварительная версия)

Запускайте приложения Oracle в инфраструктуре Azure, подключенные к серверным базам данных в облачной инфраструктуре Oracle (OCI). Это решение использует следующие возможности: 

* **Сети между облаками** . Используйте прямое соединение между Azure ExpressRoute и Oracle фастконнект для установки соединений с высокой пропускной способностью, частными и низкой задержкой между приложением и уровнем базы данных.
* **Интегрированная идентификация** . Настройте федеративные удостоверения между Azure AD и Oracle идкс, чтобы создать единый источник удостоверений для решений. Включите единый вход для управления ресурсами в OCI и Azure.

### <a name="deploy-oracle-applications-on-azure"></a>Развертывание приложений Oracle в Azure

Используйте шаблоны terraform, чтобы настроить инфраструктуру Azure и установить приложения Oracle, проверенные и поддерживаемые для запуска в конфигурации между облаками:

* Набор E-Business
* JD Edwards EnterpriseOne
* PeopleSoft
* Розничные приложения Oracle
* Oracle Hyperion Financial Management

Также развертывайте пользовательские приложения в Azure, которые подключаются к OCI и другим службам Azure.

### <a name="set-up-oracle-databases-in-oci"></a>Настройка баз данных Oracle в OCI

Используйте Oracle Database облачные службы (автономная база данных, RAC, Ексадата, DBaaS, один узел) в сочетании с приложениями Oracle, работающими в Azure. Дополнительные сведения о [параметрах базы данных OCI](https://docs.cloud.oracle.com/iaas/Content/Database/Concepts/databaseoverview.htm). 
 

## <a name="licensing"></a>Лицензирование

Развертывание приложений Oracle в Azure основано на модели "использование собственной лицензии". Предполагается, что вы правильно лицензированы для использования программного обеспечения Oracle и у вас есть действующее соглашение о поддержке для Oracle. Oracle гарантирует перемещение лицензий из локальной среды в Azure. См. раздел [часто задаваемые вопросы об](https://www.oracle.com/cloud/technologies/oracle-azure-faq.html)Oracle и Azure.

## <a name="next-steps"></a>Дальнейшие действия

* Дополнительные сведения о развертывании [образов виртуальных машин Oracle](oracle-vm-solutions.md) в инфраструктуре Azure.

* Узнайте больше о том, как [присоединиться к Azure с помощью OCI](oracle-oci-overview.md).

* Ознакомьтесь с [обзорным сеансом Oracle в Azure](https://myignite.techcommunity.microsoft.com/sessions/82915) от Ignite 2019. 
