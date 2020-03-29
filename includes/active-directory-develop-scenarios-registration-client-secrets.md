---
title: включить файл
description: включить файл для конфиденциальных клиентсценарий страниц (daemon, веб-приложение, веб-API)
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76773383"
---
## <a name="register-secrets-or-certificates"></a>Регистрация секретов или сертификатов

Что касается любого конфиденциального заявления клиента, вам необходимо зарегистрировать секрет или сертификат. Вы можете зарегистрировать секреты приложения либо через интерактивный опыт на [портале Azure,](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredAppsPreview) либо с помощью инструментов командной строки (например, PowerShell).

### <a name="register-client-secrets-by-using-the-application-registration-portal"></a>Регистрация секретов клиента с помощью портала регистрации приложений

Управление учетных данных клиентов происходит на странице **Сертификатов & секретов** для приложения:

![Сертификаты & страница секретов](../articles/active-directory/develop/media/quickstart-update-azure-ad-app-preview/credentials-certificates-secrets.png)

- Секрет приложения (также названный секретом клиента) генерируется Azure AD во время регистрации конфиденциального приложения клиента. Это поколение происходит, когда вы **выбираете Новый секрет клиента**. В этот момент необходимо скопировать секретную строку в буфер обмена для использования в приложении, прежде чем выбрать **Save.** Эта строка больше не будет представлена.
- Во время регистрации заявки вы используете кнопку **сертификата Загрузить** для загрузки сертификата. Azure AD поддерживает только сертификаты, которые непосредственно зарегистрированы в приложении и не следуют цепочкам сертификатов.

Для получения подробной информации [см. Квикстарт: Нанастройка клиентского приложения для доступа к web-АПО (ru) Добавление учетных данных в приложение.](../articles/active-directory/develop/quickstart-configure-app-access-web-apis.md#add-credentials-to-your-web-application)



### <a name="register-client-secrets-by-using-powershell"></a>Регистрируйте секреты клиента с помощью PowerShell

Кроме того, вы можете зарегистрировать приложение с помощью инструментов командной строки. В образке [Active-directory-dotnetcore-daemon-v2](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2) показано, как зарегистрировать секрет приложения или сертификат с приложением Azure AD:

- Подробную информацию о том, как зарегистрировать секрет приложения, можно найти [в AppCreationScripts/Configure.ps1](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/5199032b352a912e7cc0fce143f81664ba1a8c26/AppCreationScripts/Configure.ps1#L190).
- Подробную информацию о том, как зарегистрировать сертификат в приложении, можно найти [в AppCreationScripts-withCert/Configure.ps1](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/5199032b352a912e7cc0fce143f81664ba1a8c26/AppCreationScripts-withCert/Configure.ps1#L162-L178).
