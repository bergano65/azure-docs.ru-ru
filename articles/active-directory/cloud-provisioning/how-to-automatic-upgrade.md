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
ms.openlocfilehash: d3786386f75f4b85fe89562254eab63471de6cb2
ms.sourcegitcommit: ec2eacbe5d3ac7878515092290722c41143f151d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/31/2019
ms.locfileid: "75549403"
---
# <a name="azure-ad-connect-cloud-provisioning-agent-automatic-upgrade"></a>Azure AD Connect агент подготовки облака: автоматическое обновление

Проверка того, что ваша Azure Active Directory (Azure AD) Connect Cloud Подготовка агента подготовки к работе в облаке, всегда является самой простой функцией автоматического обновления. Эта функция включена по умолчанию и не может быть отключена.

Агент устанавливается здесь: "Program Files\azure Multi AD Connect подготовка Ажент\аадконнектпровисионингажент.ЕКСЕ"

Чтобы проверить версию, щелкните исполняемый файл правой кнопкой мыши и выберите пункт Свойства, а затем — сведения.

![Версия файла агента](media/how-to-automatic-upgrade/agent1.png)

Агент обновления устанавливается здесь: "Program Files\azure Multi AD Connect Подготовка агента Упдатер\азуреадконнектажентупдатер.ЕКСЕ"

Чтобы проверить версию, щелкните исполняемый файл правой кнопкой мыши и выберите пункт Свойства, а затем — сведения.

![Версия агента обновления](media/how-to-automatic-upgrade/agent2.png)

## <a name="uninstall-the-agent"></a>Удаление агента
Чтобы удалить агент, перейдите к разделу **Удаление или изменение программы** и удалите следующее:

- **Microsoft Azure AD подключить обновление агента**
- **Агент подготовки Microsoft Azure AD подключения**
- **Пакет агента подготовки Microsoft Azure AD подключения**

![Удаление агента](media/how-to-automatic-upgrade/agent3.png)

## <a name="next-steps"></a>Дальнейшие действия 

- [Что собой представляет подготовка?](what-is-provisioning.md)
- [What is Azure AD Connect cloud provisioning?](what-is-cloud-provisioning.md) (Что такое подготовка облака Azure AD Connect?)

