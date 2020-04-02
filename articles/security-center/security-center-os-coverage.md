---
title: Платформы, поддерживаемые Центром безопасности Azure (ru) Документы Майкрософт
description: В этом документе приводится список платформ, поддерживаемых Центром безопасности Azure.
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
ms.openlocfilehash: 3c8bf69b745f5dba8c08556908df4d4ae5b5769f
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/01/2020
ms.locfileid: "80521917"
---
# <a name="supported-platforms"></a>Поддерживаемые платформы 

На этой странице показаны платформы и среды, поддерживаемые Центром безопасности Azure.

## <a name="combinations-of-environments"></a>Комбинации сред<a name="vm-server"></a>

Центр безопасности Azure поддерживает виртуальные машины и серверы на различных типах гибридных сред:

* Только Лазурный
* Лазурный и натерритории
* Лазурный и другие облака
* Лазурный, другие облака, и на территории

Для среды Azure, активированной в подписке Azure, Центр безопасности Azure автоматически обнаружит ресурсы IaaS, развернутые в подписи.

## <a name="supported-operating-systems"></a>Поддерживаемые операционные системы

Центр безопасности зависит от [агента по аналитике журналов.](../azure-monitor/platform/agents-overview.md#log-analytics-agent) Убедитесь, что ваши машины работают с одной из поддерживаемых операционных систем для этого агента, как описано на следующих страницах:

* [Агент журналной аналитики для Windows поддерживает операционные системы](../azure-monitor/platform/log-analytics-agent.md#supported-windows-operating-systems)
* [Агент журналной аналитики для Linux поддерживает операционные системы](../azure-monitor/platform/log-analytics-agent.md#supported-linux-operating-systems)

Кроме того, убедитесь, что ваш агент Log Analytics [настроен должным образом для отправки данных в Центр безопасности](security-center-enable-data-collection.md#manual-agent)

> [!TIP]
> Чтобы узнать больше о конкретных функциях Центра [Feature coverage for machines](security-center-services.md)безопасности, доступных на Windows и Linux, см.

## <a name="managed-virtual-machine-services"></a>Управляемые услуги виртуальных машин<a name="virtual-machine"></a>

Виртуальные машины также создаются в подписке клиентов как часть некоторых сервисов, управляемых Azure, таких как Azure Kubernetes (AKS), Azure Databricks и многое другое. Центр безопасности обнаруживает эти виртуальные машины тоже, и агент Log Analytics может быть установлен и настроен, если поддерживается ОС доступна.

## <a name="cloud-services"></a>Облачные службы<a name="cloud-services"></a>

Поддерживаемы также виртуальные машины, запускаемые в облачном сервисе. Мониторинг выполняется только для облачных служб и рабочих ролей, запущенных в слотах рабочей среды. Дополнительные сведения см. в статье [Общие сведения об облачных службах Azure](../cloud-services/cloud-services-choose-me.md).

Защита для вм, проживающих в Azure Stack, также поддерживается. Для получения дополнительной информации об интеграции Центра безопасности с Azure Stack см. [На борту виртуальных машин Azure Stack в Центре безопасности.](https://docs.microsoft.com/azure/security-center/quick-onboard-azure-stack)

## <a name="next-steps"></a>Дальнейшие действия

- Узнайте, как [Центр безопасности собирает данные с помощью агента аналитики журналов.](security-center-enable-data-collection.md)
- Узнайте, как [Центр безопасности управляет и защищает данные.](security-center-data-security.md)
- Узнайте, как [спланировать и понять соображения проектирования, чтобы принять Центр безопасности Azure.](security-center-planning-and-operations-guide.md)