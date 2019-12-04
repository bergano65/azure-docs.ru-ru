---
title: 'Azure AD Connect агент подготовки облака: автоматическое обновление | Документация Майкрософт'
description: В этом разделе описывается встроенная функция автоматического обновления в Azure AD Connect агент подготовки облака.
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
ms.openlocfilehash: 193804064fbf6d1abb2ce06df1e923ec709ef6de
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/03/2019
ms.locfileid: "74794462"
---
# <a name="azure-ad-connect-cloud-provisioning-agent-automatic-upgrade"></a>Azure AD Connect агент подготовки облака: автоматическое обновление

Проверка того, что ваша Azure AD Connectная Установка агента подготовки облачной службы в актуальном состоянии, никогда не была проще с помощью функции **автоматического обновления** . Эта функция включена по умолчанию и не может быть отключена.

Агент устанавливается здесь: **"Program FILES\AZURE Multi AD Connect подготовка ажент\аадконнектпровисионингажент.ЕКСЕ"**

Вы можете проверить версию, щелкнув исполняемый файл правой кнопкой мыши и выбрав пункт Свойства, а затем — сведения.

![Версия файла агента](media/how-to-automatic-upgrade/agent1.png)

Агент обновления устанавливается здесь: **"Program FILES\AZURE Multi AD Connect Подготовка агента упдатер\азуреадконнектажентупдатер.ЕКСЕ"**

Вы можете проверить версию, щелкнув исполняемый файл правой кнопкой мыши и выбрав пункт Свойства, а затем — сведения.

![Версия агента обновления](media/how-to-automatic-upgrade/agent2.png)

## <a name="uninstalling-the-agent"></a>Удаление агента
Чтобы удалить агент, перейдите к разделу **Удаление или изменение программы** и удалите следующее:

- Microsoft Azure AD Connect Agent Updater;
- агент подготовки Microsoft Azure AD Connect;
- пакет агента подготовки Microsoft Azure AD Connect.

![Удаление агента](media/how-to-automatic-upgrade/agent3.png)

## <a name="next-steps"></a>Дальнейшие действия 

- [Что такое подготовка?](what-is-provisioning.md)
- [Что такое Azure AD Connect подготовки облака?](what-is-cloud-provisioning.md)

