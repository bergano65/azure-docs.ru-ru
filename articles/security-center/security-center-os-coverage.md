---
title: Платформы, поддерживаемые центром безопасности Azure | Документация Майкрософт
description: В этом документе представлен список платформ, поддерживаемых центром безопасности Azure.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 70c076ef-3ad4-4000-a0c1-0ac0c9796ff1
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/31/2020
ms.author: memildin
ms.openlocfilehash: 2092a1aa3d5157db0392397e86553c5cc9da9de2
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/22/2020
ms.locfileid: "90883803"
---
# <a name="supported-platforms"></a>Поддерживаемые платформы 

На этой странице показаны платформы и среды, поддерживаемые центром безопасности Azure.

## <a name="combinations-of-environments"></a>Сочетания сред <a name="vm-server"></a>

Центр безопасности Azure поддерживает виртуальные машины и серверы в разных типах гибридных сред.

* Только Azure
* Azure и локальная среда
* Azure и другие облака
* Azure, другие облака и локальная среда

Для среды Azure, активированной в подписке Azure, центр безопасности Azure автоматически обнаружит ресурсы IaaS, развернутые в подписке.

## <a name="supported-operating-systems"></a>Поддерживаемые операционные системы

Центр безопасности зависит от [агента log Analytics](../azure-monitor/platform/agents-overview.md#log-analytics-agent). Убедитесь, что компьютеры работают под управлением одной из поддерживаемых операционных систем для этого агента, как описано на следующих страницах:

* [Агент Log Analytics для операционных систем, поддерживаемых Windows](../azure-monitor/platform/agents-overview.md#supported-operating-systems)
* [Агент Log Analytics для поддерживаемых операционных систем Linux](../azure-monitor/platform/agents-overview.md#supported-operating-systems)

Также убедитесь, что агент Log Analytics [правильно настроен для отправки данных в центр безопасности](security-center-enable-data-collection.md#manual-agent) .

> [!TIP]
> Дополнительные сведения о конкретных функциях центра безопасности, доступных в Windows и Linux, см. в разделе [покрытие компонентов для компьютеров](security-center-services.md).

## <a name="managed-virtual-machine-services"></a>Управляемые службы виртуальных машин <a name="virtual-machine"></a>

Виртуальные машины также создаются в подписке клиента как часть некоторых служб, управляемых Azure, таких как Azure Kubernetes (AKS), Azure Databricks и т. д. Центр безопасности находит эти виртуальные машины тоже, и агент Log Analytics можно установить и настроить, если доступна поддерживаемая ОС.

## <a name="cloud-services"></a>Облачные службы <a name="cloud-services"></a>

Также поддерживаются виртуальные машины, работающие в облачной службе. Мониторинг выполняется только для облачных служб и рабочих ролей, запущенных в слотах рабочей среды. Дополнительные сведения см. в статье [Общие сведения об облачных службах Azure](../cloud-services/cloud-services-choose-me.md).

Также поддерживается защита виртуальных машин, находящихся в Azure Stack. Дополнительные сведения об интеграции центра безопасности с Azure Stack см. в статье подключение [Azure Stack виртуальных машин к центру безопасности](quickstart-onboard-machines.md). 

## <a name="next-steps"></a>Дальнейшие действия

- Узнайте [, как центр безопасности собирает данные с помощью агента log Analytics](security-center-enable-data-collection.md).
- Узнайте [, как центр безопасности управляет и защищает данные](security-center-data-security.md).
- Узнайте, как [планировать и понимать вопросы проектирования, касающиеся перехода в центр безопасности Azure](security-center-planning-and-operations-guide.md).