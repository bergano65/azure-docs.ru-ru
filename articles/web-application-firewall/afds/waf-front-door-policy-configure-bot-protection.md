---
title: Налаживать защиту бота для веб-приложений брандмауэра с Azure Front Door (Предварительный просмотр)
description: Изучите веб-приложение Firewall (WAF).
author: vhorne
ms.service: web-application-firewall
ms.topic: article
services: web-application-firewall
ms.date: 08/21/2019
ms.author: victorh
ms.openlocfilehash: f48b683044bc727cda461fb64a743c055188962a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76934653"
---
# <a name="configure-bot-protection-for-web-application-firewall-preview"></a>Настройка защиты бота для веб-приложений брандмауэра (Предварительный просмотр)
В этой статье показано, как настроить правило защиты ботов в Брандмауэре веб-приложений Azure (WAF) для Front Door с помощью портала Azure. Правило защиты ботов также можно настроить с помощью шаблона CLI, Azure PowerShell или azure Resource Manager.

> [!IMPORTANT]
> Сейчас набор правил защиты от ботов предоставляется в общедоступной предварительной версии и поддерживается соглашением об уровне обслуживания для предварительной версии. Некоторые функции могут не поддерживаться или их возможности могут быть ограничены.  См. [дополнительные условия использования для предварительных версий Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Предварительные требования

Создайте базовую политику WAF для Front Door, следуя инструкциям, описанным в [политике «Создание» для передней двери Azure, используя портал Azure.](waf-front-door-create-portal.md)

## <a name="enable-bot-protection-rule-set"></a>Включить набор правил защиты ботов

На странице **Управляемые правила** при создании политики Web Application Firewall сначала найдите раздел **Управляемого набора правил,** выберите флажок перед правилом **Microsoft_BotManager_1.0** из меню выпадения, а затем выберите **Обзор и Создайте**.

   ![Правило защиты ботов](.././media/waf-front-door-configure-bot-protection/botmanager112019.png)

## <a name="next-steps"></a>Дальнейшие действия

- Узнайте, как [контролировать WAF](waf-front-door-monitor.md).
