---
title: Сбор данных защиты идентификации Azure AD в предварительной версии Sentinel Azure | Документация Майкрософт
description: Дополнительные сведения о сборе данных защиты идентификации Azure AD в Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 91c870e5-2669-437f-9896-ee6c7fe1d51d
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2019
ms.author: rkarlin
ms.openlocfilehash: 609aced38b7e30f78d81934867196c568dcc85ca
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/02/2019
ms.locfileid: "57240004"
---
# <a name="collect-data-from-azure-ad-identity-protection"></a>Сбор данных из Azure AD Identity Protection

> [!IMPORTANT]
> Azure Sentinel сейчас находится в общедоступной предварительной версии.
> Эта предварительная версия предоставляется без соглашения об уровне обслуживания и не рекомендована для использования рабочей среде. Некоторые функции могут не поддерживаться или их возможности могут быть ограничены. Дополнительные сведения см. в статье [Дополнительные условия использования предварительных выпусков Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Можно осуществлять потоковую передачу журналов из [защиты идентификации Azure AD](https://docs.microsoft.com/azure/information-protection/reports-aip) в Azure Sentinel на поток оповещения в Azure Sentinel для просмотра панелей мониторинга, создать настраиваемые оповещения и улучшит исследование. Защита удостоверений с Azure Active Directory предоставляет консолидированное представление пользователей в группе риска, событий риска и уязвимостей, с возможностью немедленного уменьшения риска и установки политик для автоматического уменьшения риска будущих событий. Службы лежит опыт Майкрософт по защите удостоверений клиентов и точностью, обрабатывая более 13 миллиардов входами в день. 


## <a name="prerequisites"></a>Технические условия

- Необходимо иметь [лицензии Azure Active Directory Premium P1 или P2](https://azure.microsoft.com/pricing/details/active-directory/)
- Пользователь с глобальным администратором или разрешения администратора безопасности


## <a name="connect-to-azure-ad-identity-protection"></a>Подключение к службе защиты идентификации Azure AD

При наличии защиты идентификации Azure AD, убедитесь, что это [включена в сети](../active-directory/identity-protection/enable.md).
Если развертывается защита идентификации Azure AD и получение данных, данные оповещения можно легко настроить потоковую передачу в Azure Sentinel.


1. В Azure Sentinel, выберите **сбора данных** и нажмите кнопку **защиты идентификации Azure AD** плитку.

2. Нажмите кнопку **Connect** запустить потоковой передачи событий защиты идентификации Azure AD в Azure Sentinel.


6. Для соответствующей схемы в Log Analytics можно использовать для оповещения защиты идентификации Azure AD, поиск **IdentityProtectionLogs_CL**.

## <a name="next-steps"></a>Дальнейшие действия
В этом документе вы узнали, как подключиться к Azure Sentinel защиты идентификации Azure AD. Дополнительные сведения о Azure Sentinel, см. в разделе со следующими статьями:
- Узнайте, как [получить представление о данных и потенциальные угрозы](quickstart-get-visibility.md).
- Начало работы [обнаружение угроз с помощью Azure Sentinel](tutorial-detect-threats.md).
