---
title: Настройка защиты от программ-роботов для брандмауэра веб-приложения с Azure двери (Предварительная версия)
description: Узнайте, брандмауэр веб-приложения (WAF).
services: frontdoor
author: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/31/2019
ms.author: tyao;kumud
ms.openlocfilehash: af92f3b9049862fc19c69965f979b7dfe8c62526
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66481629"
---
# <a name="configure-bot-protection-for-web-application-firewall-preview"></a>Настройка защиты от программ-роботов для брандмауэра веб-приложения (Предварительная версия)
В этой статье показано, как настроить правила для защиты программ-роботов в брандмауэр Azure веб-приложения (WAF) для входной двери с помощью шаблона Azure CLI, Azure PowerShell или Azure Resource Manager.

Набор правил защиты управляемых программ-роботов можно включить для вашего WAF выполнять пользовательские действия для запросов от известных вредоносных IP-адресов. IP-адреса предоставляются из функции анализа угроз Майкрософт веб-канала. [Intelligent Security Graph](https://www.microsoft.com/security/operations/intelligence) лежащих в основе анализа угроз Майкрософт и используется несколько служб, включая Центр безопасности Azure.

> [!IMPORTANT]
> Набор правил защиты программ-роботов в настоящее время находится в общедоступной предварительной версии и предоставляется с соглашением об уровне обслуживания предварительной версии. Некоторые функции могут не поддерживаться или их возможности могут быть ограничены.  См. [дополнительные условия использования для предварительных версий Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Технические условия

Создание базовой политики WAF для входной двери, следуя инструкциям, описанным в [создать политику WAF передовой линии для Azure с помощью портала Azure](waf-front-door-create-portal.md).

## <a name="enable-bot-protection-rule-set"></a>Включить набор правил защиты программ-роботов

1. На странице основные политики, созданной в предыдущем разделе, в разделе **параметры**, нажмите кнопку **правила**.
2. На странице подробностей в разделе **Управление правилами** раздел, в раскрывающемся меню, установите флажок перед правило **BotProtection Предварительная версия 0.1**и выберите **Сохранить**выше.
    
   ![Правила защиты программ-роботов](./media/waf-front-door-configure-bot-protection/botprotect2.png)

## <a name="next-steps"></a>Дальнейшие действия

- Узнайте, как [отслеживать WAF](waf-front-door-monitor.md).
