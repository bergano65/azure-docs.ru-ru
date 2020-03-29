---
title: Списки исключений из веб-приложений в front Door Azure - портал Azure
description: В этой статье содержится информация о конфигурации списков исключений в Azure Front с порталом Azure.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 02/25/2020
ms.author: victorh
ms.topic: conceptual
ms.openlocfilehash: 6ed382e88700e4ecd7f8de20a2c8da7ed3c13566
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77925934"
---
# <a name="web-application-firewall-waf-with-front-door-service-exclusion-lists"></a>Веб-приложение Firewall (WAF) со списками исключений службы передних дверей 

Иногда веб-application Firewall (WAF) может заблокировать запрос, который вы хотите разрешить для вашего приложения. Например, Active Directory вставляет маркеры, которые используются для проверки подлинности. Эти маркеры могут содержать специальные символы, которые могут вызвать ложное срабатывание из правил WAF. Списки исключений WAF позволяют пропустить некоторые атрибуты запроса при проверке WAF.  Список исключений можно настроить с помощью [PowserShell,](https://docs.microsoft.com/powershell/module/az.frontdoor/New-AzFrontDoorWafManagedRuleExclusionObject?view=azps-3.5.0) [Azure CLI,](https://docs.microsoft.com/cli/azure/ext/front-door/network/front-door/waf-policy/managed-rules/exclusion?view=azure-cli-latest#ext-front-door-az-network-front-door-waf-policy-managed-rules-exclusion-add) [Rest API](https://docs.microsoft.com/rest/api/frontdoorservice/webapplicationfirewall/policies/createorupdate)или портала Azure. В следующем примере показана конфигурация портала Azure. 
## <a name="configure-exclusion-lists-using-the-azure-portal"></a>Настройка списков исключений с помощью портала Azure
**Управление исключениями** доступно с портала WAF в соответствии с **правилами управления**

![Управление](../media/waf-front-door-exclusion/exclusion1.png)
![исключением Управление exclusion_add](../media/waf-front-door-exclusion/exclusion2.png)

 Список исключений: ![Управление exclusion_define](../media/waf-front-door-exclusion/exclusion3.png)

Этот пример исключает значение в поле заголовка *пользователя.* Действительный запрос может включать поле *пользователя,* содержащее строку, которая запускает правило впрыска S'L. В этом случае можно исключить *пользовательский* параметр, чтобы правило WAF ничего не оценивало в поле.

Следующие атрибуты могут быть добавлены в списки исключений по имени. Значения полей, которые вы используете, не оцениваются в отношении правил WAF, но их имена оцениваются. Списки исключений устраняют проверку стоимости поля.

* Имя заголовка запроса
* Запрос имени cookie
* Название строки запроса args
* Запрос тела пост args имя

Вы можете указать точное соответствие заголовка запроса, текста, файла cookie или атрибута строки запроса.  Либо при необходимости вы можете задать частичное соответствие. Следующие операторы являются поддерживаемыми критериями соответствия:

- **Equals**: этот оператор используется для точного совпадения. Например, чтобы выбрать заголовок под названием **bearerToken,** используйте оператора равных с набором селектора в качестве **bearerToken.**
- **Starts with**: этот оператор находит все поля, которые начинаются с указанного значения селектора.
- **Ends with**: этот оператор находит все поля запроса, заканчивающиеся на указанное значение селектора.
- **Contains**: этот оператор находит все поля запроса, которые содержат указанное значение селектора.
- **Равно любой**: Этот оператор соответствует всем полям запроса. - это значение селектора.

Имена заголовков и файлов cookie являются нечувствительными.

Список исключений можно применить ко всем правилам в управляемом наборе правил, к правилам для определенной группы правил или к одному правилу, как показано в предыдущем примере. 

## <a name="next-steps"></a>Дальнейшие действия

После настройки настроек WAF узнайте, как просматривать журналы WAF. Для получения дополнительной [информации, см.](../afds/waf-front-door-monitor.md)
