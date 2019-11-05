---
title: Настройка программы Bot Protection для брандмауэра веб-приложения с помощью передней дверцы Azure (Предварительная версия)
description: Знакомство с брандмауэром веб-приложения (WAF).
author: vhorne
ms.service: web-application-firewall
ms.topic: article
services: web-application-firewall
ms.date: 08/21/2019
ms.author: victorh
ms.openlocfilehash: 9a8901e3763b10b7ee00c10f4eec08a8325d7dec
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73512474"
---
# <a name="configure-bot-protection-for-web-application-firewall-preview"></a>Настройка программы Bot Protection для брандмауэра веб-приложения (Предварительная версия)
В этой статье показано, как настроить правило защиты Bot в брандмауэре веб-приложения Azure (WAF) для Front двери с помощью Azure CLI, Azure PowerShell или шаблона Azure Resource Manager.

Для WAF можно включить управляемый набор правил защиты Bot, чтобы выполнять пользовательские действия с запросами от известных вредоносных IP-адресов. IP-адреса поступают из канала аналитики угроз Майкрософт. [Intelligent Security Graph](https://www.microsoft.com/security/operations/intelligence) включает аналитику угроз Майкрософт и используется несколькими службами, включая центр безопасности Azure.

> [!IMPORTANT]
> Набор правил защиты Bot в настоящее время находится в общедоступной предварительной версии и предоставляется с соглашением об уровне обслуживания предварительной версии. Некоторые функции могут не поддерживаться или их возможности могут быть ограничены.  См. [дополнительные условия использования для предварительных версий Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Технические условия

Создайте базовую политику WAF для Front двери, следуя инструкциям, описанным в статье [Создание политики WAF для передней дверцы Azure с помощью портал Azure](waf-front-door-create-portal.md).

## <a name="enable-bot-protection-rule-set"></a>Включить набор правил для защиты Bot

1. На странице Basic Policy (базовая политика), созданной в предыдущем разделе, в разделе **Параметры**щелкните **правила**.
2. На странице сведения в разделе **Управление правилами** в раскрывающемся меню установите флажок перед правилом **ботпротектион-Preview-0,1**, а затем нажмите кнопку **сохранить** выше.
    
   ![Правило защиты Bot](.././media/waf-front-door-configure-bot-protection/botprotect2.png)

## <a name="next-steps"></a>Дальнейшие действия

- Узнайте, как [ОТСЛЕЖИВАТЬ WAF](waf-front-door-monitor.md).
