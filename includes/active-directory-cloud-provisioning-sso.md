---
author: billmath
ms.service: active-directory
ms.subservice: cloud-provisioning
ms.topic: include
ms.date: 10/16/2019
ms.author: billmath
ms.openlocfilehash: 3aa1571b46938b03f556fa124d3f0a2a70f2c5c3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "79504368"
---
## <a name="steps-to-enable-single-sign-on"></a>Шаги для включения единого включения
Облачное подготовка работает с однозначным вводом.  В настоящее время нет возможности включить SSO при установке агента, однако вы можете использовать шаги ниже, чтобы включить SSO и использовать его. 

### <a name="step-1-download-and-extract-azure-ad-connect-files"></a>Шаг 1: Скачать и извлечь файлы Azure AD Connect
1.  Во-первых, загрузите последнюю версию [Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594)
2.  Откройте запрос команды, используя административные привилегии, и перейдите к только что загруженной msi.
3.  Выполнить следующее:`msiexec /a C:\filepath\AzureADConnect.msi /qb TARGETDIR=C:\filepath\extractfolder`
4. Измените файлопат и экскновировать, чтобы соответствовать пути файла и названию папки извлечения.  Содержимое теперь должно быть в папке экстракции.

### <a name="step-2-import-the-seamless-sso-powershell-module"></a>Шаг 2: Импорт бесшовный модуль SSO PowerShell

1. Скачать и установить [Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/overview).
2. Перейдите в папку `%programfiles%\Microsoft Azure Active Directory Connect`.
3. Импортируйте модуль PowerShell для простого единого входа с помощью следующей команды: `Import-Module .\AzureADSSO.psd1`.

### <a name="step-3-get-the-list-of-active-directory-forests-on-which-seamless-sso-has-been-enabled"></a>Шаг 3: Получить список активных лесов каталога, на котором Seamless SSO была включена

1. Откройте сеанс PowerShell от имени администратора. В PowerShell вызовите `New-AzureADSSOAuthenticationContext`. При запросе введите учетные данные глобального администратора своего клиента.
2. Вызовите процедуру `Get-AzureADSSOStatus`. Эта команда выводит список лесов Active Directory (см. список "Домены"), в которых включена эта функция.

### <a name="step-4-enable-seamless-sso-for-each-active-directory-forest"></a>Шаг 4. Включение простого единого входа для каждого леса Active Directory

1. Вызовите процедуру `Enable-AzureADSSOForest`. При запросе введите свои учетные данные администратора домена для нужного леса Active Directory.

   > [!NOTE]
   >Имя пользователя domain administrator учетных данных должно быть внесено в формат sam имя учетной записи (contoso'johndoe или contoso.com'johndoe). Мы используем доменную часть имени пользователя для поиска контроллера домена администратора домена с помощью DNS.

   >[!NOTE]
   >Используемая учетная запись администратора домена не должна быть членом группы защищенных пользователей. Если это так, операция завершится неудачей.

2. Повторите предыдущие шаги для каждого леса Active Directory, в котором должна быть настроена эта функция.

### <a name="step-5-enable-the-feature-on-your-tenant"></a>Шаг 5. Включить функцию в своем клиенте

Чтобы включить эту функцию на клиенте, вызовите `Enable-AzureADSSO -Enable $true`.
