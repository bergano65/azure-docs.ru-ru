---
title: ОБЗОР защиты ботов для ботов Azure Application Gateway
titleSuffix: Azure Web Application Firewall
description: В этой статье содержится обзор брандмауэра веб-приложений (WAF) о защите ботов Application Gateway
services: web-application-firewall
author: winthrop28
ms.service: web-application-firewall
ms.date: 02/04/2020
ms.author: victorh
ms.topic: conceptual
ms.openlocfilehash: 3bc481cfc35ac94699d2795862f1fe8e4decf875
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77027099"
---
# <a name="azure-web-application-firewall-on-azure-application-gateway-bot-protection-overview"></a>Обзор защиты ботов для ботов Azure Web Application Firewall на веб-портале приложений Azure

Примерно 20% всего интернет-трафика поступает от плохих ботов. Они делают такие вещи, как соскабливание, сканирование и поиск уязвимостей в вашем веб-приложении. Когда эти боты останавливаются в веб-приложении Firewall (WAF), они не могут атаковать вас. Они также не могут использовать ваши ресурсы и службы, такие как бэкэнды и другие базовые инфраструктуры.

Можно включить правило защиты управляемых ботов, установленное для вашего WAF, чтобы блокировать или регистрировать запросы с известных вредоносных IP-адресов. Эти IP-адреса передаются из канала Microsoft Threat Intelligence. Intelligent Security Graph поддерживает Microsoft Threat Intelligence и используется во множестве служб, включая Центр безопасности Azure.

> [!IMPORTANT]
> В настоящее время набор правил защиты ботов находится в открытом предварительном просмотре и предоставляется соглашением об уровне предварительного просмотра. Некоторые функции могут не поддерживаться или их возможности могут быть ограничены. Дополнительные [условия использования для предварительных](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) просмотров Microsoft Azure можно узнать подробнее.

## <a name="use-with-owasp-rulesets"></a>Использование с правилами OWASP

Вы можете использовать набор правил Bot Protection наряду с любым ими правил OWASP (2.2.9, 3.0 и 3.1). Только один набор правил OWASP может быть использован в любой момент времени. Набор правил защиты ботов содержит дополнительное правило, которое отображается в его собственном наборе правил. Он называется **Microsoft_BotManagerRuleSet_0.1**, и вы можете включить или отключить его, как и другие правила OWASP.

![Бот-сетель правил](../media/bot-protection-overview/bot-ruleset.png)

## <a name="ruleset-update"></a>Обновление наборов правил

Список правил по смягчению последствий ботов известных плохих IP-адресов обновляется несколько раз в день из канала Microsoft Threat Intelligence, чтобы оставаться в синхронизации с ботами. Ваши веб-приложения постоянно защищены даже при изменении векторов атак ботов.

## <a name="next-steps"></a>Дальнейшие действия

- [Налажить защиту ботов для веб-приложений брандмауэра на шлюзе приложения Azure (Предварительный просмотр)](bot-protection.md)
