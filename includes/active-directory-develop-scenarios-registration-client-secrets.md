---
title: включить файл
description: включить файл для целевых страниц сценария конфиденциального клиента (управляющая программа, веб-приложение, веб-API)
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/18/2018
ms.author: jmprieur
ms.custom: include file
ms.openlocfilehash: a5d34ac7eea50b67bd679d8cb8ddecf7ca277abd
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/28/2020
ms.locfileid: "76773383"
---
## <a name="register-secrets-or-certificates"></a>Регистрация секретов или сертификатов

Как и для любого конфиденциального клиентского приложения, необходимо зарегистрировать секрет или сертификат. Вы можете зарегистрировать секреты приложения с помощью интерактивного интерфейса в [портал Azure](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredAppsPreview) или с помощью средств командной строки (например, PowerShell).

### <a name="register-client-secrets-by-using-the-application-registration-portal"></a>Регистрация секретов клиента с помощью портала регистрации приложений

Управление учетными данными клиента происходит на странице **сертификаты & секреты** для приложения:

![Сертификат & секретной страницы](../articles/active-directory/develop/media/quickstart-update-azure-ad-app-preview/credentials-certificates-secrets.png)

- Секрет приложения (также называемый секретом клиента) создается Azure AD во время регистрации приложения конфиденциального клиента. Это поколение происходит при выборе **нового секрета клиента**. На этом этапе необходимо скопировать строку секрета в буфер обмена для использования в приложении перед нажатием **сохранить**. Эта строка больше не будет представлена.
- Во время регистрации приложения для отправки сертификата используется кнопка **отправить сертификат** . Azure AD поддерживает только сертификаты, которые непосредственно зарегистрированы в приложении, и не подчиняются цепочкам сертификатов.

Дополнительные сведения см. в разделе [Краткое руководство. Настройка клиентского приложения для доступа к веб-API | Добавьте учетные данные в приложение](../articles/active-directory/develop/quickstart-configure-app-access-web-apis.md#add-credentials-to-your-web-application).



### <a name="register-client-secrets-by-using-powershell"></a>Регистрация секретов клиента с помощью PowerShell

Кроме того, вы можете зарегистрировать приложение в Azure AD с помощью программ командной строки. В примере [Active-Directory-команда dotnetcore-демон-v2](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2) показано, как зарегистрировать секрет приложения или сертификат в приложении Azure AD:

- Дополнительные сведения о регистрации секрета приложения см. в разделе [аппкреатионскриптс/configure. ps1](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/5199032b352a912e7cc0fce143f81664ba1a8c26/AppCreationScripts/Configure.ps1#L190).
- Дополнительные сведения о регистрации сертификата в приложении см. в разделе [аппкреатионскриптс-висцерт/configure. ps1](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/5199032b352a912e7cc0fce143f81664ba1a8c26/AppCreationScripts-withCert/Configure.ps1#L162-L178).
