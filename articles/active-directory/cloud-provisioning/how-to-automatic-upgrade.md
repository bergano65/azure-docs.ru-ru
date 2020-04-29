---
title: 'Azure AD Connect агент подготовки облака: автоматическое обновление | Документация Майкрософт'
description: В этой статье описывается встроенная функция автоматического обновления в Azure AD Connect агент подготовки облака.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "78190319"
---
# <a name="azure-ad-connect-cloud-provisioning-agent-automatic-upgrade"></a>Azure AD Connect агент подготовки облака: автоматическое обновление

Проверка того, что ваша Azure Active Directory (Azure AD) Connect Cloud Подготовка агента подготовки к работе в облаке, всегда является самой простой функцией автоматического обновления.

Агент устанавливается здесь: "Program Files\azure Multi AD Connect подготовка Ажент\аадконнектпровисионингажент.ЕКСЕ"

Чтобы проверить версию, щелкните исполняемый файл правой кнопкой мыши и выберите пункт Свойства, а затем — сведения.

![Версия файла агента](media/how-to-automatic-upgrade/agent1.png)

Агент обновления устанавливается здесь: "Program Files\azure Multi AD Connect Подготовка агента Упдатер\азуреадконнектажентупдатер.ЕКСЕ"

Чтобы проверить версию, щелкните исполняемый файл правой кнопкой мыши и выберите пункт Свойства, а затем — сведения.

![Версия агента обновления](media/how-to-automatic-upgrade/agent2.png)

## <a name="uninstall-the-agent"></a>Удаление агента
Чтобы удалить агент, перейдите к разделу **Удаление или изменение программы** и удалите следующее:

- **Microsoft Azure AD Connect Agent Updater;**
- **агент подготовки Microsoft Azure AD Connect;**
- **пакет агента подготовки Microsoft Azure AD Connect.**

![Удаление агента](media/how-to-automatic-upgrade/agent3.png)

## <a name="next-steps"></a>Дальнейшие действия 

- [Что собой представляет подготовка?](what-is-provisioning.md)
- [Что такое облачная подготовка Azure AD Connect?](what-is-cloud-provisioning.md)

