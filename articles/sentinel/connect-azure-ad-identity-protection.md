---
title: Подключение данных защиты идентификационных данных Azure AD к Azure Sentinel
description: Узнайте, как подключить данные защиты идентификационных данных Azure AD к Azure Sentinel.
author: yelevin
manager: rkarlin
ms.assetid: 91c870e5-2669-437f-9896-ee6c7fe1d51d
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.date: 11/17/2019
ms.author: yelevin
ms.openlocfilehash: b82ddfef57efaaca0ae43750cd306a63a772b911
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/02/2020
ms.locfileid: "80616827"
---
# <a name="connect-data-from-azure-ad-identity-protection"></a>Подключение данных из системы защиты идентификации AD Azure



Вы можете передавать журналы из [Azure AD Identity Protection](https://docs.microsoft.com/azure/active-directory/identity-protection/overview-identity-protection) в Azure Sentinel для потоковой передачи оповещений в Azure Sentinel для просмотра панелей мониторинга, создания пользовательских оповещений и улучшения исследования. Azure Active Directory Identity Protection обеспечивает консолидированное представление пользователей рисков, обнаружений рисков и уязвимостей, возможность немедленного устранения рисков и настройку политик для автоматического устранения будущих событий. Сервис построен на опыте Корпорации Майкрософт по защите идентификационных данных потребителей и получает огромную точность от сигнала от более чем 13 миллиардов входов в день. 


## <a name="prerequisites"></a>Предварительные требования

- Вы должны иметь [лицензию Azure Active Directory Premium P1 или P2](https://azure.microsoft.com/pricing/details/active-directory/)
- Пользователь с разрешениями глобального администратора или администратора безопасности


## <a name="connect-to-azure-ad-identity-protection"></a>Подключение к защите идентификационных данных Azure AD

Если у вас уже есть Azure AD Identity Protection, убедитесь, что она [включена в вашей сети.](../active-directory/identity-protection/overview-identity-protection.md)
При развертывании и получении данных Azure AD и получении данных данные оповещения можно легко передавать в Azure Sentinel.


1. В Azure Sentinel выберите **разъемы данных,** а затем щелкните плитку **Защиты идентификации Azure AD.**

2. Нажмите **Connect,** чтобы начать потоковую передачу событий по защите идентификации Azure AD в Azure Sentinel.


6. Чтобы использовать соответствующую схему в журнале Analytics для предупреждений о защите идентификации Azure AD, ищите **SecurityAlert.**

## <a name="next-steps"></a>Следующие шаги
В этом документе вы узнали, как подключить защиту идентификации Azure AD к Azure Sentinel. Ознакомьтесь с дополнительными сведениями об Azure Sentinel в соответствующих статьях.
- Узнайте, как [отслеживать свои данные и потенциальные угрозы](quickstart-get-visibility.md).
- Узнайте, как приступить к [обнаружению угроз с помощью Azure Sentinel](tutorial-detect-threats-built-in.md).
