---
title: Настройка программы Bot Protection для WAF с помощью передней дверцы Azure (Предварительная версия)
description: Знакомство с брандмауэром веб-приложения (WAF).
author: vhorne
ms.service: web-application-firewall
ms.topic: article
services: web-application-firewall
ms.date: 08/21/2019
ms.author: victorh
ms.openlocfilehash: 95660f764d28172ecb55a4952b785fea5f2aa4bb
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/19/2019
ms.locfileid: "74186713"
---
# <a name="configure-bot-protection-for-web-application-firewall-preview"></a>Настройка программы Bot Protection для брандмауэра веб-приложения (Предварительная версия)
В этой статье показано, как настроить правило защиты Bot в брандмауэре веб-приложения Azure (WAF) для Front двери с помощью Azure CLI, Azure PowerShell или шаблона Azure Resource Manager.

Для WAF можно включить управляемый набор правил защиты от ботов, чтобы выполнять пользовательские действия с запросами от известных вредоносных IP-адресов. Эти IP-адреса передаются из канала Microsoft Threat Intelligence. [Intelligent Security Graph](https://www.microsoft.com/security/operations/intelligence) поддерживает Microsoft Threat Intelligence и используется во множестве служб, включая Центр безопасности Azure.

> [!IMPORTANT]
> Сейчас набор правил защиты от ботов предоставляется в общедоступной предварительной версии и поддерживается соглашением об уровне обслуживания для предварительной версии. Некоторые функции могут не поддерживаться или их возможности могут быть ограничены.  См. [дополнительные условия использования для предварительных версий Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>предварительным требованиям

Создайте базовую политику WAF для Front двери, следуя инструкциям, описанным в статье [Создание политики WAF для передней дверцы Azure с помощью портал Azure](waf-front-door-create-portal.md).

## <a name="enable-bot-protection-rule-set"></a>Включить набор правил для защиты Bot

1. На странице Basic Policy (базовая политика), созданной в предыдущем разделе, в разделе **Параметры**щелкните **правила**.
2. На странице сведения в разделе **Управление правилами** в раскрывающемся меню установите флажок перед правилом **ботпротектион-Preview-0,1**, а затем нажмите кнопку **сохранить** выше.
    
   ![Правило защиты Bot](.././media/waf-front-door-configure-bot-protection/botprotect2.png)

## <a name="next-steps"></a>Дополнительная информация

- Узнайте, как [ОТСЛЕЖИВАТЬ WAF](waf-front-door-monitor.md).
