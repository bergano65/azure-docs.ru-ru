---
title: Подключение Azure Active Directory данных к Azure Sentinel | Документация Майкрософт
description: Узнайте, как получать данные из Azure Active Directory и выполнять потоковую передачу журналов входа Azure AD и журналов аудита в Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
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
ms.author: yelevin
ms.openlocfilehash: 37106517c47c86f4a4a562eebd6d120e31e22334
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85564523"
---
# <a name="connect-data-from-azure-active-directory-azure-ad"></a>Подключение данных из Azure Active Directory (Azure AD)



Azure Sentinel позволяет получать данные из [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) и передавать их в Azure Sentinel. Вы можете выбрать потоковую передачу [журналов входа](../active-directory/reports-monitoring/concept-sign-ins.md) и [журналов аудита](../active-directory/reports-monitoring/concept-audit-logs.md) .

## <a name="prerequisites"></a>Предварительные условия

- Если вы хотите экспортировать данные входа из Azure AD, необходимо иметь лицензию Azure AD P1 или P2.

- Пользователь с разрешениями глобального администратора или администратора безопасности в клиенте, из которого нужно выполнять потоковую передачу журналов.

- Чтобы иметь возможность видеть состояние подключения, необходимо иметь разрешение на доступ к журналам диагностики Azure AD. 


## <a name="connect-to-azure-active-directory"></a>Подключение к Azure Active Directory

1. В пункте Azure Sentinel выберите **соединители данных** в меню навигации.

1. В коллекции соединителей данных выберите **Azure Active Directory** и нажмите кнопку **открыть страницу соединителя** .

1. Установите флажки рядом с журналами, которые вы хотите передавать в Azure Sentinel, и нажмите кнопку **подключить**.

1. Вы можете выбрать, будут ли оповещения из Azure AD автоматически создавать инциденты в Azure Sentinel. В разделе **Создать инциденты** выберите **Разрешить**, чтобы включить правило аналитики по умолчанию, которое автоматически создает инциденты на основе оповещений, созданных в подключенной службе безопасности. Затем это правило можно изменить в разделе **Аналитика**, а затем **Активные правила**.

1. Чтобы использовать соответствующую схему в Log Analytics для запроса оповещений Azure AD, введите `SigninLogs` или `AuditLogs` в окне запроса.




## <a name="next-steps"></a>Дальнейшие шаги
В этом документе вы узнали, как подключить Azure Active Directory к Azure Sentinel. Ознакомьтесь с дополнительными сведениями об Azure Sentinel в соответствующих статьях.
- Узнайте, как [отслеживать свои данные и потенциальные угрозы](quickstart-get-visibility.md).
- Узнайте, как приступить к [обнаружению угроз с помощью Azure Sentinel](tutorial-detect-threats-built-in.md).
