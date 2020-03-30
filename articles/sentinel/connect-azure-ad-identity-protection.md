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
ms.openlocfilehash: 7d42ff28ddd2d883feb25139096d781efe64d50f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588575"
---
# <a name="connect-data-from-azure-ad-identity-protection"></a>Подключение данных из системы защиты идентификации AD Azure



Вы можете передавать журналы из [Azure AD Identity Protection](https://docs.microsoft.com/azure/information-protection/reports-aip) в Azure Sentinel для потоковой передачи оповещений в Azure Sentinel для просмотра панелей мониторинга, создания пользовательских оповещений и улучшения исследования. Azure Active Directory Identity Protection обеспечивает консолидированное представление пользователей рисков, обнаружений рисков и уязвимостей, возможность немедленного устранения рисков и настройку политик для автоматического устранения будущих событий. Сервис построен на опыте Корпорации Майкрософт по защите идентификационных данных потребителей и получает огромную точность от сигнала от более чем 13 миллиардов входов в день. 


## <a name="prerequisites"></a>Предварительные требования

- Вы должны иметь [лицензию Azure Active Directory Premium P1 или P2](https://azure.microsoft.com/pricing/details/active-directory/)
- Пользователь с разрешениями глобального администратора или администратора безопасности


## <a name="connect-to-azure-ad-identity-protection"></a>Подключение к защите идентификационных данных Azure AD

Если у вас уже есть Azure AD Identity Protection, убедитесь, что она [включена в вашей сети.](../active-directory/identity-protection/overview-identity-protection.md)
При развертывании и получении данных Azure AD и получении данных данные оповещения можно легко передавать в Azure Sentinel.


1. В Azure Sentinel выберите **разъемы данных,** а затем щелкните плитку **Защиты идентификации Azure AD.**

2. Нажмите **Connect,** чтобы начать потоковую передачу событий по защите идентификации Azure AD в Azure Sentinel.


6. Чтобы использовать соответствующую схему в журнале Analytics для предупреждений о защите идентификации Azure AD, ищите **SecurityAlert.**

## <a name="next-steps"></a>Дальнейшие действия
В этом документе вы узнали, как подключить защиту идентификации Azure AD к Azure Sentinel. Ознакомьтесь с дополнительными сведениями об Azure Sentinel в соответствующих статьях.
- Узнайте, как [отслеживать свои данные и потенциальные угрозы](quickstart-get-visibility.md).
- Узнайте, как приступить к [обнаружению угроз с помощью Azure Sentinel](tutorial-detect-threats-built-in.md).
