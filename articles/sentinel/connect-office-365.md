---
title: Подключение данных Office 365 к Azure Sentinel | Документация Майкрософт
description: Узнайте, как подключить данные Office 365 к Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: rkarlin
ms.openlocfilehash: 481acfdccb54077f18c1f4bf0375c0dc66e66007
ms.sourcegitcommit: 992e070a9f10bf43333c66a608428fcf9bddc130
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/24/2019
ms.locfileid: "71240667"
---
# <a name="connect-data-from-office-365-logs"></a>Подключение данных из журналов Office 365



Вы можете выполнить потоковую передачу журналов аудита из [Office 365](https://docs.microsoft.com/office365/admin/admin-home?view=o365-worldwide) в Azure Sentinel одним щелчком мыши. Вы можете выполнять потоковую передачу журналов аудита из нескольких клиентов в одну рабочую область в Azure Sentinel. Соединитель журнала действий Office 365 предоставляет информацию о текущих действиях пользователей. Вы получите сведения о различных действиях пользователя, администратора, системы и политики, а также о событиях из Office 365. Подключив журналы Office 365 в Azure Sentinel, вы можете использовать эти данные для просмотра панелей мониторинга, создания пользовательских оповещений и улучшения процесса расследования.

> [!IMPORTANT]
> Если у вас есть лицензия E3, перед тем как получить доступ к данным через API-интерфейс действия управления Office 365, необходимо включить единое ведение журнала аудита для организации Office 365. Это можно сделать, включив журнал аудита Office 365. Инструкции см. [в разделе Включение или отключение поиска по журналам аудита Office 365](https://docs.microsoft.com/office365/securitycompliance/turn-audit-log-search-on-or-off). Дополнительные сведения см. в [справочнике по API действий управления Office 365](https://docs.microsoft.com/office/office-365-management-api/office-365-management-activity-api-reference).

## <a name="prerequisites"></a>Предварительные требования

- Необходимо быть глобальным администратором или администратором безопасности вашего клиента.
- На компьютере, с которого вы вошли в Azure Sentinel для создания подключения, убедитесь, что порт 4433 открыт для веб-трафика. После успешного подключения этот порт можно закрыть снова.
- Если у вашего клиента нет лицензии Office 365 E3 или Office 365, необходимо включить единый аудит в клиенте с помощью одного из следующих процессов:
    - [С помощью командлета Set-админаудитлогконфиг](https://docs.microsoft.com/powershell/module/exchange/policy-and-compliance-audit/set-adminauditlogconfig?view=exchange-ps) и включите параметр "унифиедаудитлогинжестионенаблед").
    - [Или с помощью пользовательского интерфейса центр безопасности и соответствия требованиям](https://docs.microsoft.com/office365/securitycompliance/search-the-audit-log-in-security-and-compliance#before-you-begin).

## <a name="connect-to-office-365"></a>Подключение к Office 365

1. В поле Sentinel Azure выберите **соединители данных** , а затем щелкните плитку **Office 365** .

2. Если вы еще не включили его, в разделе **Подключение** используйте кнопку **включить** , чтобы включить решение Office 365. Если он уже включен, он будет идентифицирован на экране подключения как уже включенный.
1. Office 365 позволяет передавать данные из нескольких клиентов в Azure Sentinel. Для каждого клиента, к которому необходимо подключиться, добавьте клиент в разделе **подключение клиентов к Azure Sentinel**. 
1. Откроется экран Active Directory. Вам будет предложено выполнить проверку подлинности с помощью глобального администратора на каждом клиенте, который вы хотите подключить к Azure Sentinel, и предоставить разрешения на метку Azure для чтения своих журналов. 
5. В разделе потоковая передача журналов действий Office 365 щелкните **выбрать** , чтобы выбрать типы журналов для потоковой передачи в Azure Sentinel. Сейчас Sentinel Azure поддерживает Exchange и SharePoint.

4. Нажмите кнопку **Применить изменения**.

3. Чтобы использовать соответствующую схему в Log Analytics журналов Office 365, выполните поиск по запросу **оффицеактивити**.


## <a name="next-steps"></a>Следующие шаги
В этом документе вы узнали, как подключить Office 365 к Azure Sentinel. Ознакомьтесь с дополнительными сведениями об Azure Sentinel в соответствующих статьях.
- Узнайте, как [получить представление о данных и потенциальных угрозах](quickstart-get-visibility.md).
- Приступая [к обнаружению угроз с помощью Azure Sentinel](tutorial-detect-threats-built-in.md).

