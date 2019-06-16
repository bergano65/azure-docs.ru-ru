---
title: включение файла
description: включаемый файл для сценария конфиденциальный клиент целевых страницах (управляющей программы, веб-приложения, веб-API)
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
ms.openlocfilehash: 9ee7422b372993d60c629524eb036b9678e5776c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66121844"
---
## <a name="registration-of-secrets-or-certificates"></a>Регистрация секретами и сертификатами

Как и для любого конфиденциального клиента приложения, необходимо зарегистрировать секрет или сертификат. Можно зарегистрировать свои секреты приложения либо с помощью интерактивного интерфейса в [портала Azure](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredAppsPreview), или с помощью средства командной строки (например, PowerShell)

### <a name="registering-client-secrets-using-the-application-registration-portal"></a>Регистрация секреты клиента на портале регистрации приложений

Управление учетных данных клиента происходит в **сертификаты и секреты** страница для приложения:

![image](../articles/active-directory/develop/media/quickstart-update-azure-ad-app-preview/credentials-certificates-secrets-expanded.png)

- секрет приложения (также именованным клиентом секрет) для создания Azure AD, во время регистрации конфиденциального клиентского приложения. Это поколение происходит при выборе **новый секрет клиента**. На этом этапе необходимо скопировать Секретная строка в буфере обмена для использования в приложении, прежде чем выбирать **Сохранить**. Эта строка не будет представлять больше.
- сертификат в регистрации приложения с помощью **отправка сертификата** кнопки

Дополнительные сведения см. в разделе [краткое руководство: Настройка клиентского приложения для доступа к веб-API | Добавление учетных данных в приложении](../articles/active-directory/develop/quickstart-configure-app-access-web-apis.md#add-credentials-to-your-web-application)

### <a name="registering-client-secrets-using-powershell"></a>Регистрация секреты клиента с помощью PowerShell

Кроме того можно зарегистрировать приложение с Azure AD, с помощью средства командной строки. [Active-directory-dotnetcore-daemon-v2](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2) примере показано, как зарегистрировать секрет приложения или сертификат с приложением Azure AD:

- Дополнительные сведения о том, как зарегистрировать секрет приложения, см. в разделе [AppCreationScripts/Configure.ps1](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/5199032b352a912e7cc0fce143f81664ba1a8c26/AppCreationScripts/Configure.ps1#L190)
- Дополнительные сведения о том, как зарегистрировать сертификат с приложением, см. в разделе [AppCreationScripts-withCert/Configure.ps1](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/5199032b352a912e7cc0fce143f81664ba1a8c26/AppCreationScripts-withCert/Configure.ps1#L162-L178)