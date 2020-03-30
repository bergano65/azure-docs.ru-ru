---
title: Настройка защиты ботов для брандмауэра веб-приложений Azure (WAF)
description: Узнайте, как настроить защиту ботов для веб-приложений Firewall (WAF) на шлюзе приложений Azure.
services: web-application-firewall
ms.topic: article
author: vhorne
ms.service: web-application-firewall
ms.date: 10/31/2019
ms.author: victorh
ms.openlocfilehash: 89c863e85d9eab27a47bc1bf7b98cd1c8d89e900
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73516868"
---
# <a name="configure-bot-protection-for-web-application-firewall-on-azure-application-gateway-preview"></a>Налажить защиту ботов для веб-приложений брандмауэра на шлюзе приложения Azure (Предварительный просмотр)

В этой статье показано, как настроить правило защиты ботов в Брандмауэре веб-приложений Azure (WAF) для шлюза приложений с помощью портала Azure. 

Можно включить правило защиты управляемых ботов, установленное для вашего WAF, чтобы блокировать или регистрировать запросы с известных вредоносных IP-адресов. Эти IP-адреса передаются из канала Microsoft Threat Intelligence. Intelligent Security Graph поддерживает Microsoft Threat Intelligence и используется во множестве служб, включая Центр безопасности Azure.

> [!NOTE]
> В настоящее время набор правил защиты ботов находится в открытом предварительном просмотре и предоставляется соглашением об уровне предварительного просмотра. Некоторые функции могут не поддерживаться или их возможности могут быть ограничены. Дополнительные [условия использования для предварительных](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) просмотров Microsoft Azure можно узнать подробнее.

## <a name="prerequisites"></a>Предварительные требования

Создайте базовую политику WAF для шлюза приложений, следуя инструкциям, описанным в [политике создания веб-приложений Firewall для шлюза приложений.](create-waf-policy-ag.md)

## <a name="enable-bot-protection-rule-set"></a>Включить набор правил защиты ботов

1. На странице **основных политик,** созданной ранее, в **настройках**выберите **Правила.**  

2. На странице детализации, в разделе **Правила** управления, из выпадающего меню выберите флажок для правила защиты бота, а затем выберите **Сохранить.**

> [!div class="mx-imgBorder"]
> ![Защита от ботов](../media/bot-protection/bot-protection.png)

## <a name="next-steps"></a>Дальнейшие действия

Для получения дополнительной информации об пользовательских правилах [см.](custom-waf-rules-overview.md)