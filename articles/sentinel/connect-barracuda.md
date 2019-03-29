---
title: Сбор данных Barracuda в предварительной версии Sentinel Azure | Документация Майкрософт
description: Дополнительные сведения о сборе данных Barracuda в Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 3b33b4aa-7286-4d79-b461-8e1812edc2e1
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2019
ms.author: rkarlin
ms.openlocfilehash: b4d1830e705e1813c8448787e587d720eaf5ddbd
ms.sourcegitcommit: c63fe69fd624752d04661f56d52ad9d8693e9d56
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2019
ms.locfileid: "58574724"
---
# <a name="connect-your-barracuda-appliance"></a>Подключение устройства Barracuda 

> [!IMPORTANT]
> Сейчас Azure Sentinel предоставляется в общедоступной предварительной версии.
> Эта предварительная версия предоставляется без соглашения об уровне обслуживания и не рекомендована для использования рабочей среде. Некоторые функции могут не поддерживаться или их возможности могут быть ограничены. Дополнительные сведения см. в статье [Дополнительные условия использования предварительных выпусков Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Соединитель barracuda брандмауэра веб-приложения (WAF) позволяет легко подключение к вашей Azure Sentinel, для просмотра панелей мониторинга, создать настраиваемые оповещения и улучшит исследование журналам Barracuda. Это дает больше возможностей регулировать сети вашей организации и улучшает возможности безопасности операции. Azure Sentinel использует преимущества интеграции платформенной функциональности между **Barracuda** и Microsoft Azure OMS, чтобы обеспечить прозрачную интеграцию. 


> [!NOTE]
> 
> - Данные будут храниться в географическое расположение рабочей области, на котором выполняется Azure Sentinel.

## <a name="configure-and-connect-barracuda-waf"></a>Настройка и подключение Barracuda WAF
Barracuda Web Application Firewall можно интегрировать и экспортировать журналы непосредственно к [ASI] через сервер Azure OMS.
1. Перейдите к [Barracuda WAF конфигурации потока](https://campus.barracuda.com/product/webapplicationfirewall/doc/73696965/configure-the-barracuda-web-application-firewall-to-integrate-with-the-oms-server-and-export-logs/)и следуйте инструкциям, чтобы установить подключение, используя следующие параметры:
    - **Идентификатор рабочей области**: скопируйте значение идентификатора рабочей области на странице соединителя Azure Sentinel Barracuda.
    - **Первичный ключ**: скопируйте значение первичного ключа на странице соединителя Azure Sentinel Barracuda.
2. На портале Azure Sentinel, перейдите в рабочую область, в которой развернут Azure Sentinel и нажмите кнопку с многоточием (...) в конце строки и выберите **Дополнительные параметры**. 
1. Выберите **данных** и затем **Syslog**.
1. Убедитесь, что средство в Barracuda существует и задать уровень серьезности и нажмите кнопку **Сохранить**.
6. Чтобы использовать соответствующей схемы в Log Analytics для событий, Barracuda, поиск **CommonSecurityLog**.


## <a name="validate-connectivity"></a>Проверка подключения

Может потребоваться до 20 минут, пока не журналов в Log Analytics. 



## <a name="next-steps"></a>Дальнейшие действия
В этом документе вы узнали, как подключиться к Azure Sentinel Barracuda устройств. Ознакомьтесь с дополнительными сведениями об Azure Sentinel в соответствующих статьях.
- Узнайте, как [получить представление о данных и потенциальные угрозы](quickstart-get-visibility.md).
- Начало работы [обнаружение угроз с помощью Azure Sentinel](tutorial-detect-threats.md).

