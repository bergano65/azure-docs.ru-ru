---
title: Настройка программы Bot Protection для брандмауэра веб-приложения Azure (WAF)
description: Узнайте, как настроить программу Bot Protection для брандмауэра веб-приложения (WAF) в шлюзе приложений Azure.
services: web-application-firewall
ms.topic: article
author: vhorne
ms.service: web-application-firewall
ms.date: 10/31/2019
ms.author: victorh
ms.openlocfilehash: 89c863e85d9eab27a47bc1bf7b98cd1c8d89e900
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73516868"
---
# <a name="configure-bot-protection-for-web-application-firewall-on-azure-application-gateway-preview"></a>Настройка программы Bot Protection для брандмауэра веб-приложения в шлюзе приложений Azure (Предварительная версия)

В этой статье показано, как настроить правило защиты Bot в брандмауэре веб-приложения Azure (WAF) для шлюза приложений с помощью портал Azure. 

Вы можете включить управляемый набор правил защиты Bot для WAF, чтобы блокировать или регистрировать запросы от известных вредоносных IP-адресов. Эти IP-адреса передаются из канала Microsoft Threat Intelligence. Intelligent Security Graph поддерживает Microsoft Threat Intelligence и используется во множестве служб, включая Центр безопасности Azure.

> [!NOTE]
> Набор правил защиты Bot в настоящее время находится в общедоступной предварительной версии и предоставляется с соглашением об уровне обслуживания предварительной версии. Некоторые функции могут не поддерживаться или их возможности могут быть ограничены. Дополнительные сведения см. в [дополнительных условиях использования Microsoft Azure предварительных версиях](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) .

## <a name="prerequisites"></a>предварительным требованиям

Создайте базовую политику WAF для шлюза приложений, следуя инструкциям, описанным в разделе [Создание политик брандмауэра веб-приложения для шлюза](create-waf-policy-ag.md)приложений.

## <a name="enable-bot-protection-rule-set"></a>Включить набор правил для защиты Bot

1. На странице **Basic** Policy (базовая политика), созданной ранее, в разделе **Параметры**выберите **правила**.  

2. На странице сведения в разделе **Управление правилами** в раскрывающемся меню установите флажок для правила защиты Bot и нажмите кнопку **сохранить**.

> [!div class="mx-imgBorder"]
> ](../media/bot-protection/bot-protection.png) защиты ![Bot

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о настраиваемых правилах см. [в статье пользовательские правила для брандмауэра веб-приложения версии 2 в шлюзе приложений Azure](custom-waf-rules-overview.md).