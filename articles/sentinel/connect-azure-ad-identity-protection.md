---
title: Подключение защита идентификации Azure AD данных к предварительной версии Azure Sentinel | Документация Майкрософт
description: Узнайте, как подключить данные защита идентификации Azure AD к Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: 91c870e5-2669-437f-9896-ee6c7fe1d51d
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/07/2019
ms.author: rkarlin
ms.openlocfilehash: 7478e5a5ec2260760bb6ddb1a90a66e3acdf2201
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70129259"
---
# <a name="connect-data-from-azure-ad-identity-protection"></a>Подключение данных из защита идентификации Azure AD

> [!IMPORTANT]
> Сейчас Azure Sentinel предоставляется в общедоступной предварительной версии.
> Эта предварительная версия предоставляется без соглашения об уровне обслуживания и не рекомендована для использования рабочей среде. Некоторые функции могут не поддерживаться или их возможности могут быть ограничены. Дополнительные сведения см. в статье [Дополнительные условия использования предварительных выпусков Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Вы можете выполнять потоковую передачу журналов из [Защита идентификации Azure AD](https://docs.microsoft.com/azure/information-protection/reports-aip) в метку Azure для потоковой передачи оповещений в Azure Sentinel для просмотра панелей мониторинга, создания пользовательских оповещений и улучшения расследования. Защита идентификации Azure Active Directory предоставляет объединенное представление для пользователей с рисками, обнаружения рисков и уязвимостей, с возможностью немедленного устранения рисков и установки политик для автоматического устранения будущих событий. Служба построена на основе опыта корпорации Майкрософт по защите идентификаторов потребителей и значительной точности от сигнала с более чем 13 000 000 000 журнала в день. 


## <a name="prerequisites"></a>Предварительные требования

- Требуется [лицензия Azure Active Directory Premium P1 или P2](https://azure.microsoft.com/pricing/details/active-directory/) .
- Пользователь с разрешениями глобального администратора или администратора безопасности


## <a name="connect-to-azure-ad-identity-protection"></a>Подключение к защита идентификации Azure AD

Если у вас уже есть защита идентификации Azure AD, убедитесь, что он [включен в сети](../active-directory/identity-protection/enable.md).
Если защита идентификации Azure AD развертывается и получает данные, данные предупреждений можно легко передать в Azure Sentinel.


1. В поле Sentinel Azure выберите **соединители данных** , а затем щелкните плитку **Защита идентификации Azure AD** .

2. Нажмите кнопку **подключить** , чтобы начать потоковую передачу событий Защита идентификации Azure AD события в Azure Sentinel.


6. Чтобы использовать соответствующую схему в Log Analytics для защита идентификации Azure AD предупреждений, выполните поиск по запросу **IdentityProtectionLogs_CL**.

## <a name="next-steps"></a>Следующие шаги
В этом документе вы узнали, как подключить защита идентификации Azure AD к Azure Sentinel. Ознакомьтесь с дополнительными сведениями об Azure Sentinel в соответствующих статьях.
- Узнайте, как [получить представление о данных и потенциальных угрозах](quickstart-get-visibility.md).
- Приступая [к обнаружению угроз с помощью Azure Sentinel](tutorial-detect-threats.md).
