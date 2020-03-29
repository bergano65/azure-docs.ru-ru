---
title: Подключение данных Azure AD к Azure Sentinel Документы Майкрософт
description: Узнайте, как подключить данные Active Directory Azure к Azure Sentinel.
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
ms.openlocfilehash: be9241a6156621d3f90dbab2da5bebeb463b4232
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588626"
---
# <a name="connect-data-from-azure-active-directory"></a>Подключение данных из active-каталога Azure



Azure Sentinel позволяет собирать данные из [Active Directory Azure](../active-directory/fundamentals/active-directory-whatis.md) и передавать их в Azure Sentinel. Вы можете выбрать поток [журналов входа и](../active-directory/reports-monitoring/concept-sign-ins.md) [журналов аудита.](../active-directory/reports-monitoring/concept-audit-logs.md)

## <a name="prerequisites"></a>Предварительные требования

- Если вы хотите экспортировать данные о ввоза из Active Directory, вы должны иметь лицензию Azure AD P1 или P2.

- Пользователь с глобальными разрешениями админа или админ-аминона безопасности на арендатора, с помощью чего вы хотите передавать журналы.

- Чтобы увидеть состояние соединения, необходимо иметь разрешение на доступ к диагностическим журналам Azure AD. 


## <a name="connect-to-azure-ad"></a>Подключение к Azure AD

1. В Azure Sentinel выберите **разъемы данных,** а затем щелкните плитку **Active Directory Azure.**

1. Рядом с журналами, которые вы хотите транслировать в Azure Sentinel, нажмите **Connect.**

1. Можно выбрать, хотите ли оповещения из Azure AD автоматически генерировать инциденты в Azure Sentinel. В разделе **Создать инциденты** выберите **Разрешить**, чтобы включить правило аналитики по умолчанию, которое автоматически создает инциденты на основе оповещений, созданных в подключенной службе безопасности. Затем это правило можно изменить в разделе **Аналитика**, а затем **Активные правила**.

1. Чтобы использовать соответствующую схему в журнале Analytics для предупреждений Azure AD, ищите **данные о Входах** в журнал и **аудиты.**




## <a name="next-steps"></a>Дальнейшие действия
В этом документе вы узнали, как подключить Azure AD к Azure Sentinel. Ознакомьтесь с дополнительными сведениями об Azure Sentinel в соответствующих статьях.
- Узнайте, как [отслеживать свои данные и потенциальные угрозы](quickstart-get-visibility.md).
- Узнайте, как приступить к [обнаружению угроз с помощью Azure Sentinel](tutorial-detect-threats-built-in.md).
