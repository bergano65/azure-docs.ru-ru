---
title: SharePoint файлы - NA Maker
description: Добавьте защищенные источники данных SharePoint в базу знаний, чтобы обогатить базу знаний вопросами и ответами, которые могут быть обеспечены Active Directory.
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: 6f05079e39c8afb001bd4ba09d68f435c18efad5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80294877"
---
# <a name="add-a-secured-sharepoint-data-source-to-your-knowledge-base"></a>Добавление защищенного источника данных SharePoint в базу знаний

Добавьте защищенные облачные источники данных SharePoint в базу знаний, чтобы обогатить базу знаний вопросами и ответами, которые могут быть обеспечены Active Directory.

При добавлении защищенного документа SharePoint в базу знаний, как менеджер создателя, вы должны запросить разрешение Active Directory для создателя NA. После того, как это разрешение дается от менеджера Active Directory создателю для доступа к SharePoint, оно не должно быть дано снова. Каждое последующее дополнение документа к базе знаний не нуждается в авторизации, если он находится в том же ресурсе SharePoint.

Если менеджер базы знаний NA Maker не является менеджером Active Directory, вам необходимо связаться с менеджером Active Directory, чтобы завершить этот процесс.

## <a name="prerequisites"></a>Предварительные требования

* Облачный SharePoint - NA Maker использует Microsoft Graph для получения разрешений. Если ваш SharePoint находится на месте, вы не сможете извлечь из SharePoint, потому что Microsoft Graph не сможет определить разрешения.
* Формат URL - NA Maker поддерживает только URL-адреса SharePoint, которые генерируются для совместного использования и имеют формат`https://\*.sharepoint.com`

## <a name="add-supported-file-types-to-knowledge-base"></a>Добавление поддерживаемых типов файлов в базу знаний

Вы можете добавить все [типы файлов,](../Concepts/content-types.md) поддерживаемых создателем, с сайта SharePoint в базу знаний. Возможно, вам придется предоставить [разрешения,](#permissions) если ресурс файла защищен.

1. Из библиотеки с сайтом SharePoint выберите меню эллипсиса файла. `...`
1. Копируйте URL файла.

   ![Получите URL-адрес файла SharePoint, выбрав меню эллипсис файла, а затем скопируя URL.](../media/add-sharepoint-datasources/get-sharepoint-file-url.png)

1. На портале «NA Maker» на странице **«Настройки»** [добавьте URL-адрес](manage-knowledge-bases.md#edit-knowledge-base) в базу знаний.

### <a name="images-with-sharepoint-files"></a>Изображения с файлами SharePoint

Если файлы включают изображения, они не извлекаются. Вы можете добавить изображение с портала NA Maker после того, как файл будет извлечен в пары nA.

Добавьте изображение со следующим синтаксисом разметки:

```markdown
![Explanation or description of image](URL of public image)
```

Текст в квадратных скобках, `[]`объясняет изображение. URL в скобках, `()`является прямой ссылкой на изображение.

При тестировании пары qnA в интерактивной тестовой панели на портале NA Maker изображение отображается вместо текста разметки. Это проверяет, что изображение может быть публично извлечено из вашего клиентского приложения.

## <a name="permissions"></a>Разрешения

Предоставление разрешений происходит, когда защищенный файл с сервера SharePoint добавляется в базу знаний. В зависимости от того, как настроен SharePoint и разрешения лица, добавляющего файл, это может потребовать:

* никаких дополнительных шагов - лицо, добавляя файл, имеет все необходимые разрешения.
* как [менеджербазы знаний, так](#knowledge-base-manager-add-sharepoint-data-source-in-qna-maker-portal) и [менеджер Active Directory.](#active-directory-manager-grant-file-read-access-to-qna-maker)

Смотрите шаги, перечисленные ниже.

### <a name="knowledge-base-manager-add-sharepoint-data-source-in-qna-maker-portal"></a>Менеджер базы знаний: добавить источник данных SharePoint на портале «NA Maker»

Когда **менеджер snA Maker** добавляет защищенный документ SharePoint в базу знаний, менеджер базы знаний инициирует запрос на разрешение, которое должен выполнить менеджер Active Directory.

Запрос начинается с всплывающих требований для проверки подлинности учетной записи Active Directory.

![Authenticate учетная запись пользователя](../media/add-sharepoint-datasources/authenticate-user-account.png)

Как только менеджер nA Maker выберет учетную запись, администратор Active Directory Azure получит уведомление о том, что ему необходимо разрешить доступ к ресурсу SharePoint приложению «NA Maker». Менеджеру active Directory Azure необходимо будет сделать это для каждого ресурса SharePoint, но не для каждого документа в этом ресурсе.

### <a name="active-directory-manager-grant-file-read-access-to-qna-maker"></a>Активный менеджер каталога: грант файл читать доступ к NA Maker

Менеджеру Active Directory (не менеджеру создателя nA) необходимо предоставить доступ к qnA Maker для доступа к ресурсу SharePoint, выбрав [эту ссылку,](https://login.microsoftonline.com/common/oauth2/v2.0/authorize?response_type=id_token&scope=Files.Read%20Files.Read.All%20Sites.Read.All%20User.Read%20User.ReadBasic.All%20profile%20openid%20email&client_id=c2c11949-e9bb-4035-bda8-59542eb907a6&redirect_uri=https%3A%2F%2Fwww.qnamaker.ai%3A%2FCreate&state=68) чтобы разрешить корпоративному приложению SharePoint для получения разрешений на чтение файлов.

![Менеджер Active Directory Выдает разрешение в интерактивном режиме](../media/add-sharepoint-datasources/aad-manager-grants-permission-interactively.png)

<!--
The Active Directory manager must grant QnA Maker access either by application name, `QnAMakerPortalSharePoint`, or by application ID, `c2c11949-e9bb-4035-bda8-59542eb907a6`.
-->
<!--
### Grant access from the interactive pop-up window

The Active Directory manager will get a pop-up window requesting permissions to the `QnAMakerPortalSharePoint` app. The pop-up window includes the QnA Maker Manager email address that initiated the request, an `App Info` link to learn more about **QnAMakerPortalSharePoint**, and a list of permissions requested. Select **Accept** to provide those permissions.

![Azure Active Directory manager grants permission interactively](../media/add-sharepoint-datasources/aad-manager-grants-permission-interactively.png)
-->
<!--

### Grant access from the App Registrations list

1. The Active Directory manager signs in to the Azure portal and opens **[App registrations list](https://ms.portal.azure.com/#blade/Microsoft_AAD_IAM/ApplicationsListBlade)**.

1. Search for and select the **QnAMakerPortalSharePoint** app. Change the second filter box from **My apps** to **All apps**. The app information will open on the right side.

    ![Select QnA Maker app in App registrations list](../media/add-sharepoint-datasources/select-qna-maker-app-in-app-registrations.png)

1. Select **Settings**.

    [![Select Settings in the right-side blade](../media/add-sharepoint-datasources/select-settings-for-qna-maker-app-registration.png)](../media/add-sharepoint-datasources/select-settings-for-qna-maker-app-registration.png#lightbox)

1. Under **API access**, select **Required permissions**.

    ![Select 'Settings', then under 'API access', select 'Required permission'](../media/add-sharepoint-datasources/select-required-permissions-in-settings-blade.png)

1. Do not change any settings in the **Enable Access** window. Select **Grant Permission**.

    [![Under 'Grant Permission', select 'Yes'](../media/add-sharepoint-datasources/grant-app-required-permissions.png)](../media/add-sharepoint-datasources/grant-app-required-permissions.png#lightbox)

1. Select **YES** in the pop-up confirmation windows.

    ![Grant required permissions](../media/add-sharepoint-datasources/grant-required-permissions.png)
-->
### <a name="grant-access-from-the-azure-active-directory-admin-center"></a>Грант доступ из центра admin-управления Active Directory Azure

1. Менеджер Active Directory введает на портал Azure и открывает **[приложения Enterprise.](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps)**

1. Поиск `QnAMakerPortalSharePoint` выбранного приложения «NA Maker».

    [![Поиск по списку приложений «NAMakerPortalSharePoint» в списке корпоративных приложений](../media/add-sharepoint-datasources/search-enterprise-apps-for-qna-maker.png)](../media/add-sharepoint-datasources/search-enterprise-apps-for-qna-maker.png#lightbox)

1. Под **безопасностью,** перейдите на **разрешения**. Выберите **согласие админа Гранта на организацию**.

    [![Выберите аутентифицированный пользователь для активного админа каталога](../media/add-sharepoint-datasources/grant-aad-permissions-to-enterprise-app.png)](../media/add-sharepoint-datasources/grant-aad-permissions-to-enterprise-app.png#lightbox)

1. Выберите учетную запись Sign-On с разрешениями на выдачу разрешений для Active Directory.



<!--

## Add SharePoint data source with APIs

You need to get the SharePoint file's URI before adding it to QnA Maker.

## Get SharePoint File URI

Use the following steps to transform the SharePoint URL into a sharing token.

1. Encode the URL using [base64](https://en.wikipedia.org/wiki/Base64).

1. Convert the base64-encoded result to an unpadded base64url format with the following character changes.

    * Remove the equal character, `=` from the end of the value.
    * Replace `/` with `_`.
    * Replace `+` with `-`.
    * Append `u!` to be beginning of the string.

1. Sign in to Graph explorer and run the following query, where `sharedURL` is ...:

    ```
    https://graph.microsoft.com/v1.0/shares/<sharedURL>/driveitem
    ```

    Get the **@microsoft.graph.downloadUrl** and use this as `fileuri` in the QnA Maker APIs.

### Add or update a SharePoint File URI to your knowledge base

Use the **@microsoft.graph.downloadUrl** from the previous section as the `fileuri` in the QnA Maker API for [adding a knowledge base](https://go.microsoft.com/fwlink/?linkid=2092179) or [updating a knowledge base](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update). The following fields are mandatory: name, fileuri, filename, source.

```
{
    "name": "Knowledge base name",
    "files": [
        {
            "fileUri": "<@microsoft.graph.downloadURL>",
            "fileName": "filename.xlsx",
            "source": "<SharePoint link>"
        }
    ],
    "urls": [],
    "users": [],
    "hostUrl": "",
    "qnaList": []
}
```



## Remove QnA Maker app from SharePoint authorization

1. Use the steps in the previous section to find the Qna Maker app in the Active Directory admin center.
1. When you select the **QnAMakerPortalSharePoint**, select **Overview**.
1. Select **Delete** to remove permissions.

-->

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Совместная работа с базой знаний](collaborate-knowledge-base.md)
