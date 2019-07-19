---
title: Настройка программы Bot Protection для брандмауэра веб-приложения с помощью передней дверцы Azure (Предварительная версия)
description: Знакомство с брандмауэром веб-приложения (WAF).
services: frontdoor
author: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/31/2019
ms.author: kumud
ms.reviewer: tyao
ms.openlocfilehash: 0f36ea33badad52c55cd11491874db955df5408b
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/12/2019
ms.locfileid: "67846342"
---
# <a name="configure-bot-protection-for-web-application-firewall-preview"></a>Настройка программы Bot Protection для брандмауэра веб-приложения (Предварительная версия)
В этой статье показано, как настроить правило защиты Bot в брандмауэре веб-приложения Azure (WAF) для Front двери с помощью Azure CLI, Azure PowerShell или шаблона Azure Resource Manager.

Для WAF можно включить управляемый набор правил защиты Bot, чтобы выполнять пользовательские действия с запросами от известных вредоносных IP-адресов. IP-адреса поступают из канала аналитики угроз Майкрософт. [Intelligent Security Graph](https://www.microsoft.com/security/operations/intelligence) включает аналитику угроз Майкрософт и используется несколькими службами, включая центр безопасности Azure.

> [!IMPORTANT]
> Набор правил защиты Bot в настоящее время находится в общедоступной предварительной версии и предоставляется с соглашением об уровне обслуживания предварительной версии. Некоторые функции могут не поддерживаться или их возможности могут быть ограничены.  См. [дополнительные условия использования для предварительных версий Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>предварительные требования

Создайте базовую политику WAF для Front двери, следуя инструкциям, описанным в статье [Создание политики WAF для передней дверцы Azure с помощью портал Azure](waf-front-door-create-portal.md).

## <a name="enable-bot-protection-rule-set"></a>Включить набор правил для защиты Bot

1. На странице Basic Policy (базовая политика), созданной в предыдущем разделе, в разделе **Параметры**щелкните **правила**.
2. На странице сведения в разделе **Управление правилами** в раскрывающемся меню установите флажок перед правилом **ботпротектион-Preview-0,1**, а затем нажмите кнопку **сохранить** выше.
    
   ![Правило защиты Bot](./media/waf-front-door-configure-bot-protection/botprotect2.png)

## <a name="next-steps"></a>Следующие шаги

- Узнайте, как [ОТСЛЕЖИВАТЬ WAF](waf-front-door-monitor.md).
