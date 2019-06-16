---
title: Решения Oracle в Microsoft Azure | Документация Майкрософт
description: Дополнительные сведения о вариантах развертывания приложений Oracle и решений в Microsoft Azure, включая запущены на инфраструктуры Azure или с помощью подключения к облачной инфраструктуры облака с Oracle (OCI).
services: virtual-machines-linux
documentationcenter: ''
author: romitgirdhar
manager: jeconnoc
tags: azure-resource-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 06/04/2019
ms.author: rogirdh
ms.openlocfilehash: b5e40975fca491f289d949ee273d13053897fc6d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66743640"
---
# <a name="overview-of-oracle-applications-and-solutions-on-azure"></a>Обзор приложения Oracle и решений в Azure

В этой статье рассматриваются возможности для запуска решений Oracle, с помощью инфраструктуры Azure. См. также подробное введение доступных [образов виртуальных Машин Oracle](oracle-vm-solutions.md) в Azure Marketplace, а также возможность предварительного просмотра [соединения Azure с Oracle облачной инфраструктуры (OCI)](oracle-oci-overview.md).

## <a name="oracle-databases-on-azure-infrastructure"></a>Баз данных Oracle в инфраструктуре Azure

Выполните баз данных Oracle на инфраструктуру Azure, с помощью образов Linux в Azure Marketplace:

* Oracle Database 12.1 12.2 и 18,3 Enterprise Edition 

* Oracle Database 12.1 12.2 и 18,3 Standard Edition 

Можно также создать решение на основе пользовательского образа, создание с нуля в Azure и отправка пользовательского образа из локальной среды.

При необходимости настроить несколько подключенных дисков и улучшить производительность базы данных, установив Oracle автоматической Storage Management (ASM).

## <a name="applications-on-oracle-linux-and-weblogic-server"></a>Приложений на Linux, Oracle и WebLogic Server

Запуск корпоративных приложений в Azure в поддерживаемых операционных системах с поддержкой Oracle. В Azure Marketplace доступны следующие образы:

* Oracle WebLogic Server 12.1.2

* Oracle Linux (UEK), 6.8, 6.9, 6.10, 7.3, 7.4, 7.5 и 7.6

## <a name="high-availability-and-disaster-recovery-options"></a>Варианты обеспечения высокого уровня доступности и аварийного восстановления

* Настройка Oracle Data Guard, Active Data Guard и GoldenGate в инфраструктуре Azure в сочетании с [зон доступности](../../../availability-zones/az-overview.md) для обеспечения высокой доступности.

* Используйте [Azure Site Recovery](../../../site-recovery/site-recovery-overview.md) для координации и управления процессами аварийного восстановления для виртуальных машин Oracle Linux в Azure и локальных или физических серверов. 

* Включить кластеры реальных приложений Oracle (RAC) в Azure с помощью [FlashGrid SkyCluster](https://www.flashgrid.io/oracle-rac-in-azure/).

## <a name="integration-of-azure-with-oci-preview"></a>Интеграция Azure с помощью OCI (Предварительная версия)

Запустите приложения Oracle в инфраструктуру Azure, подключенных к базе данных серверной части облачной инфраструктуры Oracle (OCI). Это решение использует следующие возможности: 

* **Облачной сети** -использовать прямые соединения между Azure ExpressRoute и Oracle FastConnect для установления соединения высокой пропускной способностью, частных и низкой задержкой между приложением и на уровне базы данных.
* **Интегрированные удостоверений** -Настройка федеративного удостоверения между Azure AD и IDCS Oracle, чтобы создать источник единое удостоверение для решений. Включите единый вход для управления ресурсами OCI и в Azure.

### <a name="deploy-oracle-applications-on-azure"></a>Развертывать приложения Oracle в Azure

Используйте шаблоны Terraform для настройки инфраструктуры Azure и установка приложений Oracle тестируются и поддерживаются для запуска в облачной конфигурации:

* E-Business Suite
* JD Edwards EnterpriseOne
* PeopleSoft
* Приложения для розничной торговли Oracle
* Управление финансами Hyperion Oracle

Также развертывание пользовательских приложений в Azure, которые подключаются с OCI и других служб Azure.

### <a name="set-up-oracle-databases-in-oci"></a>Настройка баз данных Oracle в OCI

Используйте облачные службы базы данных Oracle (автономной базы данных, RAC, Exadata, DBaaS, одним узлом) вместе с Oracle приложений, работающих в Azure. Дополнительные сведения о [параметры базы данных OCI](https://docs.cloud.oracle.com/iaas/Content/Database/Concepts/databaseoverview.htm). 
 

## <a name="licensing"></a>Лицензирование

Развертывание приложений Oracle в Azure основан на модели «принести собственные лицензии». Предполагается, что вы имеете право использовать программное обеспечение Oracle и у вас соглашение о текущей поддержке на месте с Oracle должным образом. Oracle гарантирует перемещение лицензий из локальной среды в Azure. См. в разделе Oracle Azure [часто задаваемые вопросы о](https://www.oracle.com/cloud/technologies/oracle-azure-faq.html).

## <a name="next-steps"></a>Дальнейшие действия

* Дополнительные сведения о развертывании [образов виртуальных Машин Oracle](oracle-vm-solutions.md) в инфраструктуре Azure.

* Дополнительные сведения о том, как [соединения Azure с помощью OCI](oracle-oci-overview.md).