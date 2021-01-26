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
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/20/2021
ms.author: yelevin
ms.openlocfilehash: eb89d2a4e719e34ad5ea31656dc9e3c02472b07d
ms.sourcegitcommit: fc8ce6ff76e64486d5acd7be24faf819f0a7be1d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/26/2021
ms.locfileid: "98802252"
---
# <a name="connect-data-from-azure-active-directory-azure-ad"></a>Подключение данных из Azure Active Directory (Azure AD)

С помощью встроенного соединителя Azure Sentinel можно собирать данные из [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) и передавать их в поток Azure Sentinel. Соединитель позволяет выполнять потоковую передачу [журналов входа](../active-directory/reports-monitoring/concept-sign-ins.md) и [журналов аудита](../active-directory/reports-monitoring/concept-audit-logs.md).

## <a name="prerequisites"></a>Предварительные требования

- Для приема журналов входа в Azure Sentinel необходимо иметь подписку [Azure AD Premium P2](https://azure.microsoft.com/pricing/details/active-directory/) . Для Azure Monitor (Log Analytics) и Azure Sentinel может взиматься дополнительная плата за гигабайт.

- Пользователю должна быть назначена роль "участник Sentinel Azure" в рабочей области.

- Пользователю должны быть назначены роли глобального администратора или администратора безопасности на клиенте, с которого нужно выполнять потоковую передачу журналов.

- Чтобы увидеть состояние подключения, пользователь должен иметь разрешения на чтение и запись для параметров диагностики Azure AD. 

## <a name="connect-to-azure-active-directory"></a>Подключение к Azure Active Directory

1. В пункте Azure Sentinel выберите **соединители данных** в меню навигации.

1. В коллекции соединителей данных выберите **Azure Active Directory** а затем щелкните **открыть страницу соединителя**.

1. Установите флажки рядом с типами журналов, которые вы хотите передавать в Azure Sentinel, и нажмите кнопку **подключить**. Ниже приведены типы журналов, которые можно выбрать.

    - **Журналы входа**: сведения об использовании управляемых приложений и действиях входа пользователей.
    - **Журналы аудита**. сведения о действиях системы для управления пользователями и группами, управляемыми приложениями и действиями с каталогами.

## <a name="find-your-data"></a>Поиск данных

После установки успешного подключения данные отображаются в **журналах** в разделе **логманажемент** в следующих таблицах:

- `SigninLogs`
- `AuditLogs`

Чтобы запросить журналы Azure AD, введите соответствующее имя таблицы в верхней части окна запроса.

## <a name="next-steps"></a>Дальнейшие действия
В этом документе вы узнали, как подключить Azure Active Directory к Azure Sentinel. Ознакомьтесь с дополнительными сведениями об Azure Sentinel в соответствующих статьях.
- Узнайте, как [отслеживать свои данные и потенциальные угрозы](quickstart-get-visibility.md).
- Узнайте, как приступить к [обнаружению угроз с помощью Azure Sentinel](tutorial-detect-threats-built-in.md).
