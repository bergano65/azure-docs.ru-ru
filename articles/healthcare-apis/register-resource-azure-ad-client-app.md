---
title: Регистрация приложения-ресурса в Azure AD с помощью API Azure для FHIR
description: Зарегистрируйте приложение ресурсов (или API) в Azure Active Directory, чтобы клиентские приложения могли запрашивать доступ к ресурсу при проверке подлинности.
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: matjazl
ms.openlocfilehash: e92fab392dc73d8de0b7b2547e38b3f345562930
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/13/2020
ms.locfileid: "91975864"
---
# <a name="register-a-resource-application-in-azure-active-directory"></a>Регистрация приложения-ресурса в Azure Active Directory

В этой статье вы узнаете, как зарегистрировать приложение ресурсов (или API) в Azure Active Directory. Приложение ресурсов — это Azure Active Directory представление самого сервера API FHIR, а клиентские приложения могут запрашивать доступ к ресурсу при проверке подлинности. Приложение ресурса также называется *аудиторией* в OAuth терминах.

## <a name="azure-api-for-fhir"></a>Azure API для FHIR

При использовании API Azure для FHIR приложение ресурсов автоматически создается при развертывании службы. Пока вы используете API Azure для FHIR в том же клиенте Azure Active Directory, что и при развертывании приложения, вы можете пропустить это руководство и развернуть API Azure для FHIR, чтобы приступить к работе.

Если вы используете другой клиент Azure Active Directory (не связанный с вашей подпиской), вы можете импортировать Azure API для приложения ресурсов FHIR в клиент с помощью PowerShell:

```azurepowershell-interactive
New-AzADServicePrincipal -ApplicationId 4f6778d8-5aef-43dc-a1ff-b073724b9495
```

также можно использовать Azure CLI:

```azurecli-interactive
az ad sp create --id 4f6778d8-5aef-43dc-a1ff-b073724b9495
```

## <a name="fhir-server-for-azure"></a>Сервер FHIR для Azure

Если вы используете сервер FHIR с открытым исходным кодом для Azure, выполните следующие действия, чтобы зарегистрировать приложение-ресурс.

### <a name="app-registrations-in-azure-portal"></a>Регистрация приложений в портал Azure

1. На [портале Azure](https://portal.azure.com) в области навигации слева щелкните **Azure Active Directory**.

2. В колонке **Azure Active Directory** щелкните **Регистрация приложений**:

    ![портал Azure. Регистрация нового приложения.](media/how-to-aad/portal-aad-new-app-registration.png)

3. Щелкните **новую регистрацию**.

### <a name="add-a-new-application-registration"></a>Добавление регистрации нового приложения

Введите сведения о новом приложении. Никаких особых требований к отображаемому имени нет, но задание его в универсальном коде ресурса (URI) сервера FHIR упрощает поиск:

![Регистрация нового приложения](media/how-to-aad/portal-aad-register-new-app-registration-NAME.png)

### <a name="set-identifier-uri-and-define-scopes"></a>Задать URI идентификатора и определить области

Приложение ресурса имеет URI идентификатора (URI идентификатора приложения), который клиенты могут использовать при запросе доступа к ресурсу. Это значение заполнит `aud` утверждение маркера доступа. Рекомендуется задать этот универсальный код ресурса (URI) сервера FHIR. Для ИНТЕЛЛЕКТУАЛЬНых приложений на FHIR предполагается, что *аудитория* является универсальным кодом ресурса (URI) сервера FHIR.

1. Щелкните **предоставить API** .

2. Нажмите кнопку **задать** рядом с *URI идентификатора приложения*.

3. Введите URI идентификатора и нажмите кнопку **сохранить**. Хорошим URI идентификатора будет URI сервера FHIR.

4. Щелкните **Добавить область** и добавьте все области, которые вы хотите определить для API. Чтобы предоставить разрешения на доступ к приложению ресурсов в будущем, необходимо добавить хотя бы одну область. Если у вас нет каких-либо конкретных областей, которые вы хотите добавить, можно добавить user_impersonation в качестве области.

![Аудитория и область](media/how-to-aad/portal-aad-register-new-app-registration-AUD-SCOPE.png)

### <a name="define-application-roles"></a>Определение ролей приложения

API Azure для FHIR и сервер OSS FHIR для Azure используют [Azure Active Directory роли приложений](https://docs.microsoft.com/azure/architecture/multitenant-identity/app-roles) для управления доступом на основе ролей. Чтобы определить, какие роли должны быть доступны для API сервера FHIR, откройте [Манифест](https://docs.microsoft.com/azure/active-directory/active-directory-application-manifest/)приложения ресурсов:

1. Щелкните **Манифест**:

    ![Роли приложений](media/how-to-aad/portal-aad-register-new-app-registration-APP-ROLES.png)

2. В `appRoles` свойстве добавьте роли, которые должны иметь пользователи или приложения:

    ```json
    "appRoles": [
      {
        "allowedMemberTypes": [
          "User",
          "Application"
        ],
        "description": "FHIR Server Administrators",
        "displayName": "admin",
        "id": "1b4f816e-5eaf-48b9-8613-7923830595ad",
        "isEnabled": true,
        "value": "admin"
      },
      {
        "allowedMemberTypes": [
          "User"
        ],
        "description": "Users who can read",
        "displayName": "reader",
        "id": "c20e145e-5459-4a6c-a074-b942bbd4cfe1",
        "isEnabled": true,
        "value": "reader"
      }
    ],
    ```

## <a name="next-steps"></a>Дальнейшие действия

В этой статье вы узнали, как зарегистрировать приложение-ресурс в Azure Active Directory. Затем зарегистрируйте конфиденциальное клиентское приложение.
 
>[!div class="nextstepaction"]
>[Регистрация конфиденциального клиентского приложения](register-confidential-azure-ad-client-app.md)