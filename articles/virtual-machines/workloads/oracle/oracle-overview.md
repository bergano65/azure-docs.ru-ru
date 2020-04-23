---
title: Решения Oracle в Microsoft Azure | Документация Майкрософт
description: Узнайте о вариантах развертывания приложений и решений Oracle в Microsoft Azure, включая работу полностью на инфраструктуре Azure или использование кросс-облачного подключения с Oracle Cloud Infrastructure (OCI).
services: virtual-machines-linux
documentationcenter: ''
author: BorisB2015
tags: azure-resource-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/16/2020
ms.author: borisb
ms.openlocfilehash: c737189650d571fb62a770707e84ed15c5a37a57
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81870497"
---
# <a name="overview-of-oracle-applications-and-solutions-on-azure"></a>Обзор приложений и решений Oracle в Azure

В этой статье представлены возможности для запуска решений Oracle с использованием инфраструктуры Azure. Смотрите также подробные знакомства с доступными [изображениями Oracle VM](oracle-vm-solutions.md) в Azure Marketplace и [возможностью соединения Azure с облачной инфраструктурой Oracle (OCI).](oracle-oci-overview.md)

## <a name="oracle-databases-on-azure-infrastructure"></a>Базы данных Oracle об инфраструктуре Azure

Запуск баз данных Oracle на инфраструктуре Azure с помощью базы данных Oracle на изображениях Oracle Linux, доступных в Azure Marketplace:

* Oracle База данных 12.1, 12.2, и 18.3 Enterprise Edition 

* База данных Oracle 12.1, 12.2 и 18.3 Стандартное издание 

Вы также можете настроить базу данных Oracle на изображении, доступном в Azure, не Oracle Linux, основать решение на пользовательском изображении, созданном с нуля в Azure, или загрузить пользовательское изображение из вашей предварительной среды.

Дополнительно настроить с несколькими прикрепленными дисками и повысить производительность базы данных путем установки Oracle Автоматизированное управление хранением (ASM).

## <a name="applications-on-oracle-linux-and-weblogic-server"></a>Приложения на Oracle Linux и WebLogic Server

Запуск корпоративных приложений в Azure на поддерживаемых операционных системах Oracle. Следующие изображения доступны в Azure Marketplace:

* Oracle WebLogic Server 12.1.2

* Oracle Linux с ядром нерушимого предприятия (UEK) 6.8, 6.9, 6.10, 7.3, 7.4, 7.5 и 7.6 

## <a name="high-availability-and-disaster-recovery-options"></a>Высокая доступность и параметры аварийного восстановления

* Наймите [Oracle Data Guard,](https://docs.oracle.com/cd/B19306_01/server.102/b14239/concepts.htm#g1049956) [Active Data Guard с FSFO,](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/dgbkr/index.html) [Sharding](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/admin/sharding-overview.html) или [Golden Gate](https://www.oracle.com/middleware/technologies/goldengate.html) на инфраструктуре Azure в сочетании с [зонами доступности](../../../availability-zones/az-overview.md) для высокой доступности в регионе. Можно также настроить эти конфигурации в нескольких регионах Azure для дополнительной доступности и аварийного восстановления.

* Используйте [восстановление сайта Azure](../../../site-recovery/site-recovery-overview.md) для организации и управления аварийным восстановлением для ваших ВМ Oracle Linux в Azure и ваших наместах или физических серверах. 

* Включите кластеры реальных приложений Oracle (RAC) в Azure с помощью [решения Azure VMWare](https://docs.microsoft.com/azure/vmware-cloudsimple/oracle-real-application-clusters/) или [FlashGrid SkyCluster.](https://www.flashgrid.io/oracle-rac-in-azure/)

## <a name="backup-oracle-workloads"></a>Резервные рабочие нагрузки Oracle

* Резервное копирование ВМ Oracle с помощью [резервного копирования Azure](https://docs.microsoft.com/azure/backup/backup-overview)

* Резервное копирование базы данных Oracle с помощью Oracle RMAN и опционально использовать [Azure Blob предохранитель](https://docs.microsoft.com/azure/storage/blobs/storage-how-to-mount-container-linux) для установки [высоко реудирующей учетной записи хранения Azure Blob storage](https://docs.microsoft.com/azure/storage/common/storage-redundancy) и написать резервные копии RMAN на нее для дополнительной устойчивости.

## <a name="integration-of-azure-with-oci"></a>Интеграция Azure с OCI

Запуск oracle-приложений в инфраструктуре Azure, подключенных к базам данных бэкэнда в облачной инфраструктуре Oracle (OCI). Это решение использует следующие возможности: 

* **Кросс-облачная сеть** - Используйте прямую взаимосвязь между Azure ExpressRoute и Oracle FastConnect для установления высокой пропускной способности, частных и низкой задержки связей между приложением и уровнем базы данных.
* **Интегрированная идентификация** - Настройка федеративного удостоверения между Azure AD и Oracle IDCS для создания единого источника идентификации для решений. Включите одиночный всхотвравлять для того чтобы управлять ресурсами через OCI и Azure.

### <a name="deploy-oracle-applications-on-azure"></a>Развертывание приложений Oracle в Azure

Используйте шаблоны Terraform для настройки инфраструктуры Azure и установки приложений Oracle. 

> [!IMPORTANT]
> Oracle сертифицирует эти приложения для запуска в Azure при использовании решения для интерконнекта Azure /Oracle Cloud к маю 2020 года.

* E-Бизнес Люкс
* JD Эдвардс EnterpriseOne
* Peoplesoft
* Приложения Oracle Retail
* Oracle Гиперион Финансовое управление

Кроме того, развертывайте пользовательские приложения в Azure, которые соединяются с OCI и другими службами Azure.

### <a name="set-up-oracle-databases-in-oci"></a>Настройка баз данных Oracle в OCI

Используйте облачные службы Oracle Database (Автономная база данных, RAC, Exadata, DBaaS, один очный узел) совместно с приложениями Oracle, работающими в Azure. Узнайте больше о [вариантах базы данных OCI](https://docs.cloud.oracle.com/iaas/Content/Database/Concepts/databaseoverview.htm). 
 

## <a name="licensing"></a>Лицензирование

Развертывание приложений Oracle в Azure основано на модели «принесите свою собственную лицензию». Предполагается, что вы имеете надлежащую лицензию на использование программного обеспечения Oracle и что у вас есть текущее соглашение о поддержке с Oracle. Oracle гарантирует перемещение лицензий из локальной среды в Azure. Смотрите [часто задаваемые вопросы](https://www.oracle.com/cloud/technologies/oracle-azure-faq.html)Oracle-Azure .

## <a name="next-steps"></a>Дальнейшие действия

* Узнайте больше о развертывании [изображений Oracle VM](oracle-vm-solutions.md) в инфраструктуре Azure.

* Узнайте больше о том, как [соединить Azure с OCI.](oracle-oci-overview.md)

* Ознакомьтесь с [обзорной сессией Oracle на обзоре Azure](https://myignite.techcommunity.microsoft.com/sessions/82915) с исплаения 2019 года. 
