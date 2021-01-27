---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 01/27/2021
ms.author: mimart
ms.openlocfilehash: 3458a4f3ba337d4c470c21fa5d89b28f5f2701a3
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/27/2021
ms.locfileid: "98916932"
---
Если у вас еще нет сертификата, для работы с этим учебником можно использовать самозаверяющий сертификат. Самозаверяющий сертификат — это сертификат безопасности, который не подписан центром сертификации (ЦС). Они не предоставляют все гарантии безопасности сертификата, подписанного центром сертификации. 

# <a name="windows"></a>[Windows](#tab/windows)

В Windows используйте командлет [New-SelfSignedCertificate](/powershell/module/pkiclient/new-selfsignedcertificate) PowerShell, чтобы создать сертификат.

1. Выполните эту команду PowerShell, чтобы создать самозаверяющий сертификат. Измените аргумент `-Subject`, указав реальные значения приложения и имени клиента Azure  AD B2C. Можно также скорректировать дату `-NotAfter`, чтобы указать другой срок действия сертификата.

    ```PowerShell
    New-SelfSignedCertificate `
        -KeyExportPolicy Exportable `
        -Subject "CN=yourappname.yourtenant.onmicrosoft.com" `
        -KeyAlgorithm RSA `
        -KeyLength 2048 `
        -KeyUsage DigitalSignature `
        -NotAfter (Get-Date).AddMonths(12) `
        -CertStoreLocation "Cert:\CurrentUser\My"
    ```

1. Откройте **Управление сертификатами пользователя** > **Текущий пользователь** > **Персональные** > **Сертификаты** > *yourappname.yourtenant.onmicrosoft.com*.
1. Выберите сертификат > **Действие** > **Все задачи** > **Экспорт**.
1. Нажмите **Да** > **Далее** > **Да, экспортировать закрытый ключ** > **Далее**.
1. Примите значения по умолчанию для **формата файла экспорта**.
1. Укажите пароль для сертификата.

Чтобы Azure AD B2C мог принять пароль PFX-файла, пароль должен быть зашифрован с помощью параметра TripleDES – SHA1 в служебной программе экспорта хранилища сертификатов Windows, а не AES256-SHA256.

# <a name="macos"></a>[macOS](#tab/macos)

В macOS для создания сертификата используйте [Помощник по сертификатам](https://support.apple.com/guide/keychain-access/aside/glosa3ed0609/11.0/mac/11.0) в цепочке ключей.

1. Следуйте инструкциям, чтобы [Создать Самозаверяющие сертификаты с доступом к цепочке ключей на Mac](https://support.apple.com/guide/keychain-access/kyca8916/mac).
1. В приложении для доступа к цепочке ключей на компьютере Mac выберите созданный сертификат.
1. Выберите файл > экспорт элементов.
1. Выберите имя файла для сохранения сертификата. Например, **Self-заверенный-Certificate. p12**. 
1. В качестве **формата файла** выберите файл **обмена личной информацией (P12)**.
1. Щелкните **Сохранить**.
1. Введите **пароль**, а затем **Проверьте** его пароль.
1. Замените расширение файла на `.pfx` . Например, **Селф-сигнед-цертификате. pfx**. 

---
