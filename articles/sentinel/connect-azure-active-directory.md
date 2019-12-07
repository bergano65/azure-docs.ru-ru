---
title: Подключение данных Azure AD к Azure Sentinel | Документация Майкрософт
description: Узнайте, как подключить данные Azure Active Directory к Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: 0a8f4a58-e96a-4883-adf3-6b8b49208e6a
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: rkarlin
ms.openlocfilehash: f08cd731e40b204d042e5df418b03626b9082c3b
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/06/2019
ms.locfileid: "74894655"
---
# <a name="connect-data-from-azure-active-directory"></a>Подключение данных из Azure Active Directory



Azure Sentinel позволяет получать данные из [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) и передавать их в Azure Sentinel. Вы можете выбрать потоковую передачу [журналов входа](../active-directory/reports-monitoring/concept-sign-ins.md) и [журналов аудита](../active-directory/reports-monitoring/concept-audit-logs.md) .

## <a name="prerequisites"></a>Технические условия

- Если вы хотите экспортировать данные входа из Active Directory, необходимо иметь лицензию Azure AD P1 или P2.

- Пользователь с разрешениями глобального администратора или администратора безопасности в клиенте, из которого нужно выполнять потоковую передачу журналов.

- Чтобы иметь возможность видеть состояние подключения, необходимо иметь разрешение на доступ к журналам диагностики Azure AD. 


## <a name="connect-to-azure-ad"></a>Подключение к Azure AD

1. В поле Sentinel Azure выберите **соединители данных** , а затем щелкните плитку **Azure Active Directory** .

1. Рядом с журналами, которые вы хотите передавать в Azure Sentinel, нажмите кнопку **подключить**.

1. Вы можете выбрать, будут ли оповещения из Azure AD автоматически создавать инциденты в Azure Sentinel. В разделе **Создать инциденты** выберите **Разрешить**, чтобы включить правило аналитики по умолчанию, которое автоматически создает инциденты на основе оповещений, созданных в подключенной службе безопасности. Затем это правило можно изменить в разделе **Аналитика**, а затем **Активные правила**.

1. Чтобы использовать соответствующую схему в Log Analytics оповещений Azure AD, выполните поиск по запросу **сигнинлогс** и **AuditLogs**.




## <a name="next-steps"></a>Дальнейшие действия
В этом документе вы узнали, как подключить Azure AD к Azure Sentinel. Ознакомьтесь с дополнительными сведениями об Azure Sentinel в соответствующих статьях.
- Узнайте, как [отслеживать свои данные и потенциальные угрозы](quickstart-get-visibility.md).
- Узнайте, как приступить к [обнаружению угроз с помощью Azure Sentinel](tutorial-detect-threats-built-in.md).
