---
title: Прекращается поддержка API классической модели развертывания для метрик и автомасштабирования в Azure Monitor
description: Прекращается поддержка классических API метрик и автомасштабирования, именуемых также моделью развертывания RDFE или Azure Service Management (ASM)
author: rboucher
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 11/19/2018
ms.author: robb
ms.openlocfilehash: ce54b63aa7831ed40a8592d536c43fc83fdc5567
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/15/2018
ms.locfileid: "53433098"
---
# <a name="azure-monitor-retirement-of-classic-deployment-model-apis-for-metrics-and-autoscale"></a>Прекращается поддержка API классической модели развертывания для метрик и автомасштабирования в Azure Monitor

Платформа Azure Monitor (при первом выпуске звалась Azure Insights) в настоящее время позволяет создавать параметры автомасштабирования и управлять ими для классических виртуальных машин и классических облачных служб, а также использовать метрики таких машин и служб. Поддержка первоначального набора, включающего API классической модели развертывания, будет **прекращена после 30 июня 2019 г.** во всех общедоступных и частных облаках Azure во всех регионах.   

Те же операции поддерживались благодаря набору API на основе Azure Resource Manager в течение более года. На портале Azure используются новые REST API для автомасштабирования и метрик. Доступны также новый пакет SDK, PowerShell и интерфейс командной строки, основанные на этих API Resource Manager. Наши партнерские службы для мониторинга используют новые REST API на основе Resource Manager в Azure Monitor.  

## <a name="who-is-not-affected"></a>На кого изменения не повлияют

Если вы управляете автомасштабированием с помощью портала Azure, [нового пакета SDK для Azure Monitor](https://www.nuget.org/packages/Microsoft.Azure.Management.Monitor/), PowerShell, интерфейса командной строки или шаблонов Resource Manager, никакие действия не требуются.  

При использовании метрик через портал Azure или с помощью различных [партнерских служб мониторинга](../../azure-monitor/platform/partners.md) никакие действия не требуются. Корпорация Майкрософт вместе с партнерами по мониторингу работают над миграцией на новые API.

## <a name="who-is-affected"></a>На кого изменения повлияют

Эта статья написана для вас, если вы используете следующие компоненты:

- **Классический пакет SDK для Azure Insights.** Если вы используете [классический пакет SDK для Azure Insights](https://www.nuget.org/packages/Microsoft.WindowsAzure.Management.Monitoring/), переходите на использование нового пакета SDK Azure Monitor для [.NET](https://github.com/azure/azure-libraries-for-net#download) или [Java](https://github.com/azure/azure-libraries-for-java#download). Скачайте [пакет NuGet SDK для Azure Monitor](https://www.nuget.org/packages/Microsoft.Azure.Management.Monitor/).

- **Классическое автомасштабирование.** Если вы вызываете [классические API параметров автомасштабирования](https://msdn.microsoft.com/library/azure/mt348562.aspx) из пользовательских средств или с помощью [классического пакета SDK для Azure Insights](https://www.nuget.org/packages/Microsoft.WindowsAzure.Management.Monitoring/), следует перейти к использованию [REST API для Azure Monitor Resource Manager](https://docs.microsoft.com/rest/api/monitor/autoscalesettings).

- **Классические метрики.** Если вы используете метрики с помощью [классических REST API](https://msdn.microsoft.com/library/azure/dn510374.aspx) или [классического пакета SDK для Azure Insights](https://www.nuget.org/packages/Microsoft.WindowsAzure.Management.Monitoring/) из пользовательских средств, следует перейти к использованию [REST API для Azure Monitor Resource Manager](https://docs.microsoft.com/rest/api/monitor/autoscalesettings). 

Если вы не уверены, происходит ли вызов классических API со стороны вашего кода или пользовательских средств, проверьте следующее:

- Просмотрите универсальный код ресурса (URI), на который ссылается код или средство. Классические API используют URI https://management.core.windows.net. Следует использовать более новые URI для интерфейсов API на основе Resource Manager, начинающиеся с https://management.azure.com/.

- Сравните имя сборки на вашем компьютере. Более старая классическая сборка находится здесь: https://www.nuget.org/packages/Microsoft.WindowsAzure.Management.Monitoring/.

- Если вы используете проверку подлинности на основе сертификата для доступа к API автомасштабирования или метрик, вы используете классическую конечную точку и библиотеку. Для более новых API Resource Manager требуется проверка подлинности Azure Active Directory с помощью субъекта-службы или субъекта-пользователя.

- Если вы используете вызовы, упомянутые в документации по любой из указанных ниже ссылок, то вы используете более старые классические API.

  - [Библиотека классов Windows.Azure.Management.Monitoring](https://docs.microsoft.com/previous-versions/azure/dn510414(v=azure.100))

  - [.NET для мониторинга (классический вариант)](https://docs.microsoft.com/previous-versions/azure/reference/mt348562(v%3dazure.100))

  - [Интерфейс IMetricOperations](https://docs.microsoft.com/previous-versions/azure/reference/dn802395(v%3dazure.100))

## <a name="why-you-should-switch"></a>Почему нужно перейти

Все существующие функциональные возможности автомасштабирования и метрик останутся благодаря новым интерфейсам API.  

Миграция на более новые API обеспечивает доступ к таким возможностям Resource Manager, как поддержка согласованного управления доступом на основе ролей (RBAC) для всех служб мониторинга. Вы также получите такие дополнительные функции для метрик: 

- поддержка измерений;
- унифицированная 1-минутная степень детализации метрик для служб; 
- усовершенствованные запросы;
- увеличение периода хранения данных (93 дня для метрик по сравнению с 30 днями). 

Как и другие службы в Azure, API для Azure Monitor на основе Resource Manager обеспечивают лучшую производительность, масштабируемость и надежность. 

## <a name="what-happens-if-you-do-not-migrate"></a>Что произойдет, если не выполнить миграцию

### <a name="before-retirement"></a>До прекращения поддержки

Не будет никакого прямого влияния на ваши службы Azure или их рабочие нагрузки.  

### <a name="after-retirement"></a>После прекращения поддержки

Вызовы классических API, перечисленных ранее, завершатся ошибкой. Будут возвращаться сообщения об ошибках примерно следующего вида:

Для автомасштабирования: *Этот API не рекомендуется. Используйте портал Azure, пакет SDK для Azure Monitor, PowerShell, интерфейс командной строки или шаблоны Resource Manager для управления параметрами автомасштабирования*".  

Для метрик: *Этот API не рекомендуется. Используйте портал Azure, пакет SDK для Azure Monitor, PowerShell или интерфейс командной строки для запрашивания метрик*".

## <a name="email-notifications"></a>Уведомления по почте

Было отправлено уведомление о прекращении поддержки на электронные адреса для следующих ролей учетной записи: 

- администраторы служб и учетной записи;
- соадминистраторы.  

Если у вас возникли какие-либо вопросы, пишите нам на адрес MonitorClassicAPIhelp@microsoft.com.  

## <a name="references"></a>Ссылки

- [Более новые REST API для Azure Monitor](https://docs.microsoft.com/rest/api/monitor/) 
- [Более новый пакет SDK для Azure Monitor](https://www.nuget.org/packages/Microsoft.Azure.Management.Monitor/)
