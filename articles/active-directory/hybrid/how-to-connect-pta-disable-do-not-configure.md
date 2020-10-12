---
title: Отключить PTA при использовании Azure AD Connect "не настраивать" | Документация Майкрософт
description: В этой статье описывается Azure AD Connect, как отключить PTA с помощью функции "не настраивать".
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.date: 04/20/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: f5db99f5e8ed2ea5844acba5500bc94d8fb0db2b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "85358350"
---
# <a name="disable-pta-when-using-azure-ad-connect-do-not-configure"></a>Отключить PTA при использовании Azure AD Connect "не настраивать"

Если используется сквозная проверка подлинности с Azure AD Connect и для нее задано значение "не настраивать", его можно отключить. Отключение PTA можно выполнить с помощью следующих командлетов. 

## <a name="prerequisites"></a>Предварительные требования
Ниже перечислены необходимые компоненты.
- Любой компьютер Windows, на котором установлен агент PTA. 
- Агент должен иметь версию 1.5.1742.0 или более позднюю. 
- Учетная запись глобального администратора Azure для запуска командлетов PowerShell для отключения PTA.

>[!NOTE]
> Если агент более старая, возможно, у него нет командлетов, необходимых для выполнения этой операции. Вы можете получить новый агент на портале Azure. Установите его на любой компьютер с Windows и укажите учетные данные администратора. (Установка агента не влияет на состояние PTA в облаке)

> [!IMPORTANT]
> Если вы используете облако Azure для государственных организаций, вам потребуется передать параметр ENVIRONMENTNAME со следующим значением. 
>
>| Имя среды | Облако |
>| - | - |
>| AzureUSGovernment; | US Gov|


## <a name="to-disable-pta"></a>Отключение PTA
В сеансе PowerShell используйте следующую команду, чтобы отключить PTA:
1. Агент проверки подлинности PS C:\Program Files\Microsoft Azure AD Connect> `Import-Module .\Modules\PassthroughAuthPSModule`
2. `Get-PassthroughAuthenticationEnablementStatus -Feature PassthroughAuth` или `Get-PassthroughAuthenticationEnablementStatus -Feature PassthroughAuth -EnvironmentName <identifier>`
3. `Disable-PassthroughAuthentication  -Feature PassthroughAuth` или `Disable-PassthroughAuthentication -Feature PassthroughAuth -EnvironmentName <identifier>`

## <a name="if-you-dont-have-access-to-an-agent"></a>Если у вас нет доступа к агенту

Если у вас нет компьютера агента, для установки агента можно использовать следующую команду.

1. Скачайте последнюю версию агента проверки подлинности из portal.azure.com.
2. Установите компонент или. `.\AADConnectAuthAgentSetup.exe``.\AADConnectAuthAgentSetup.exe ENVIRONMENTNAME=<identifier>`


## <a name="next-steps"></a>Дальнейшие действия

- [Вход пользователей с помощью сквозной проверки подлинности Azure Active Directory](how-to-connect-pta.md)
