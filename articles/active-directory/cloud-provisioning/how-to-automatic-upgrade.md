---
title: 'Облачный агент Azure AD Connect: Автоматическое обновление Документы Майкрософт'
description: В этой статье описывается встроенная функция автоматического обновления в облачном агенте Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/02/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: f09b2fc685881aa8a7bd87b6a855c657af9ef43d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78190319"
---
# <a name="azure-ad-connect-cloud-provisioning-agent-automatic-upgrade"></a>Облачный механизм подготовки Azure AD Connect: Автоматическое обновление

Убедиться в том, что установка агента облачного приложения Azure Active Directory (Azure AD) Подключение всегда актуальна, очень просто с функцией автоматического обновления.

Агент установлен здесь: "Файлы программы AD Connect Представитель Подготовки Агент AADConnectProvisioningAgent.exe"

Чтобы проверить свою версию, нажмите правой кнопкой мыши исполняемые и выберите свойства, а затем детали.

![Версия файла агента](media/how-to-automatic-upgrade/agent1.png)

Обновление агента установлено здесь: "Файлы программы AD Connect Обновление агента Обновления-AzureADConnectAgentUpdater.exe"

Чтобы проверить свою версию, нажмите правой кнопкой мыши исполняемые и выберите свойства, а затем детали.

![Версия обновления агента](media/how-to-automatic-upgrade/agent2.png)

## <a name="uninstall-the-agent"></a>Удаление агента
Чтобы удалить агент, перейдите на **Uninstall или изменить программу** и удалить следующее:

- **Microsoft Azure AD Connect Agent Updater;**
- **агент подготовки Microsoft Azure AD Connect;**
- **пакет агента подготовки Microsoft Azure AD Connect.**

![Удаление агента](media/how-to-automatic-upgrade/agent3.png)

## <a name="next-steps"></a>Дальнейшие действия 

- [Что собой представляет подготовка?](what-is-provisioning.md)
- [Что такое подготовка облака Azure AD Connect?](what-is-cloud-provisioning.md)

