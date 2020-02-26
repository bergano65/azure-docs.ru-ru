---
title: Подключение защита идентификации Azure AD данных к Azure Sentinel
description: Узнайте, как подключить данные защита идентификации Azure AD к Azure Sentinel.
author: yelevin
manager: rkarlin
ms.assetid: 91c870e5-2669-437f-9896-ee6c7fe1d51d
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.date: 11/17/2019
ms.author: yelevin
ms.openlocfilehash: 7d42ff28ddd2d883feb25139096d781efe64d50f
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/25/2020
ms.locfileid: "77588575"
---
# <a name="connect-data-from-azure-ad-identity-protection"></a>Подключение данных из защита идентификации Azure AD



Вы можете выполнять потоковую передачу журналов из [Защита идентификации Azure AD](https://docs.microsoft.com/azure/information-protection/reports-aip) в метку Azure для потоковой передачи оповещений в Azure Sentinel для просмотра панелей мониторинга, создания пользовательских оповещений и улучшения расследования. Защита идентификации Azure Active Directory предоставляет объединенное представление для пользователей с рисками, обнаружения рисков и уязвимостей, с возможностью немедленного устранения рисков и установки политик для автоматического устранения будущих событий. Служба построена на основе опыта корпорации Майкрософт по защите идентификаторов потребителей и значительной точности от сигнала с более чем 13 000 000 000 журнала в день. 


## <a name="prerequisites"></a>Предварительные требования

- Требуется [лицензия Azure Active Directory Premium P1 или P2](https://azure.microsoft.com/pricing/details/active-directory/) .
- Пользователь с разрешениями глобального администратора или администратора безопасности


## <a name="connect-to-azure-ad-identity-protection"></a>Подключение к защита идентификации Azure AD

Если у вас уже есть защита идентификации Azure AD, убедитесь, что он [включен в сети](../active-directory/identity-protection/overview-identity-protection.md).
Если защита идентификации Azure AD развертывается и получает данные, данные предупреждений можно легко передать в Azure Sentinel.


1. В поле Sentinel Azure выберите **соединители данных** , а затем щелкните плитку **Защита идентификации Azure AD** .

2. Нажмите кнопку **подключить** , чтобы начать потоковую передачу событий Защита идентификации Azure AD события в Azure Sentinel.


6. Чтобы использовать соответствующую схему в Log Analytics для защита идентификации Azure AD предупреждений, выполните поиск по запросу **секуритялерт**.

## <a name="next-steps"></a>Следующие шаги
В этом документе вы узнали, как подключить защита идентификации Azure AD к Azure Sentinel. Ознакомьтесь с дополнительными сведениями об Azure Sentinel в соответствующих статьях.
- Узнайте, как [отслеживать свои данные и потенциальные угрозы](quickstart-get-visibility.md).
- Узнайте, как приступить к [обнаружению угроз с помощью Azure Sentinel](tutorial-detect-threats-built-in.md).
