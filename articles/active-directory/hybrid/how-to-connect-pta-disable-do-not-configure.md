---
title: Отключение PTA при использовании Azure AD Connect "Не настраивайтесь" Документы Майкрософт
description: В этой статье описывается, как отключить PTA с функцией Azure AD Connect "не настраивать".
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.topic: reference
ms.workload: identity
ms.date: 04/20/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: fa1046dc64fed3edb6c9d04f76a96f488769ff42
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81726804"
---
# <a name="disable-pta-when-using-azure-ad-connect-do-not-configure"></a>Отключение PTA при использовании Azure AD Connect "Не настраивайтесь"

Если вы используете pass-through Authentication с Azure AD Connect и у вас есть набор для "Не настраивать", вы можете отключить его. Отключение ПТА может быть сделано с помощью следующих cmdlets. 

## <a name="prerequisites"></a>Предварительные требования
Ниже перечислены необходимые компоненты.
- Любая машина окон, которая установлена агентом PTA. 
- Агент должен быть в версии 1.5.1742.0 или позже. 
- Глобальная учетная запись администратора Azure для запуска cmdlets PowerShell для отключить PTA.

>[!NOTE]
> Если ваш агент старше, то он не может иметь cmdlets, необходимых для завершения этой операции. Вы можете получить новый агент от Azure Portal установить его на любой машине Windows и предоставить учетные данные админа. (Установка агента не влияет на состояние PTA в облаке)

> [!IMPORTANT]
> Если вы используете облако Azure Government, то вам придется пройти в параметр ENVIRONMENTNAME со следующим значением. 
>
>| Имя среды | Облако |
>| - | - |
>| AzureUSGovernment; | US Gov|


## <a name="to-disable-pta"></a>Чтобы отключить ЗПТ
Из сеанса PowerShell используйте следующее, чтобы отключить PTA:
1. PS C: «Файлы программы»Microsoft Azure AD Connect Агент по аутентификации>`Import-Module .\Modules\PassthroughAuthPSModule`
2. `Get-PassthroughAuthenticationEnablementStatus -Feature PassthroughAuth` или `Get-PassthroughAuthenticationEnablementStatus -Feature PassthroughAuth -EnvironmentName <identifier>`
3. `Disable-PassthroughAuthentication  -Feature PassthroughAuth` или `Disable-PassthroughAuthentication -Feature PassthroughAuth -EnvironmentName <identifier>`

## <a name="if-you-dont-have-access-to-an-agent"></a>Если у вас нет доступа к агенту

Если у вас нет агента машины вы можете использовать следующие команды для установки агента.

1. Скачать последний Агент Аут из portal.azure.com.
2. Установить функцию: `.\AADConnectAuthAgentSetup.exe` или`.\AADConnectAuthAgentSetup.exe ENVIRONMENTNAME=<identifier>`


## <a name="next-steps"></a>Следующие шаги

- [Вход пользователей с помощью сквозной проверки подлинности Azure Active Directory](how-to-connect-pta.md)