---
title: Собирать данные Azure AD в предварительной версии Sentinel Azure | Документация Майкрософт
description: Дополнительные сведения о сборе данных Azure Active Directory в Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 0a8f4a58-e96a-4883-adf3-6b8b49208e6a
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 1/30/2019
ms.author: rkarlin
ms.openlocfilehash: 315b18feb74862bbeca6ff8265ee003fbad48595
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/02/2019
ms.locfileid: "57242323"
---
# <a name="collect-data-from-azure-active-directory"></a>Сбор данных из Azure Active Directory

> [!IMPORTANT]
> Azure Sentinel сейчас находится в общедоступной предварительной версии.
> Эта предварительная версия предоставляется без соглашения об уровне обслуживания и не рекомендована для использования рабочей среде. Некоторые функции могут не поддерживаться или их возможности могут быть ограничены. Дополнительные сведения см. в статье [Дополнительные условия использования предварительных выпусков Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Azure Sentinel позволяет собирать данные из [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) и передать их в Azure Sentinel. Вы можете выбрать поток [журналы входа](../active-directory/reports-monitoring/concept-sign-ins.md) и [журналы аудита](../active-directory/reports-monitoring/concept-audit-logs.md) .

## <a name="prerequisites"></a>Технические условия

- Если вы хотите экспортировать данные входа из Active Directory, необходимо иметь лицензию Azure AD P1 или P2.

- Пользователь с глобального администратора или безопасности разрешений администратора для клиента, необходимо выполнять потоковую передачу журналов из.


## <a name="connect-to-azure-ad"></a>Подключение к Azure AD

1. В Azure Sentinel, выберите **сбора данных** и нажмите кнопку **Azure Active Directory** плитку.

2. Рядом с журналы для потоковой передачи в Azure Sentinel, щелкните **Connect**.






## <a name="next-steps"></a>Дальнейшие действия
В этом документе вы узнали, как соединиться с Azure AD Azure Sentinel. Дополнительные сведения о Azure Sentinel, см. в разделе со следующими статьями:
- Узнайте, как [получить представление о данных и потенциальные угрозы](quickstart-get-visibility.md).
- Начало работы [обнаружение угроз с помощью Azure Sentinel](tutorial-detect-threats.md).
