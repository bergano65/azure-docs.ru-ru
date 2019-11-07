---
title: Добавление Google в качестве поставщика удостоверений для B2B-Azure Active Directory | Документация Майкрософт
description: Установите федерацию с Google, чтобы гостевые пользователи входили в свои приложения Azure AD с помощью учетных записей Gmail.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 11/1/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 68acf32660fe36ddd4c2982b818ce21adde7ddab
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/05/2019
ms.locfileid: "73603593"
---
# <a name="add-google-as-an-identity-provider-for-b2b-guest-users"></a>Добавление Google в качестве поставщика удостоверений для гостевых пользователей B2B

Настроив Федерацию с помощью Google, можно разрешить приглашенным пользователям входить в ваши общие приложения и ресурсы с помощью собственных учетных записей Gmail, не создавая учетные записи Майкрософт (MSAs). 

> [!NOTE]
> Google Federation предназначен специально для пользователей Gmail. Чтобы создать федерацию с доменами G Suite, используйте [функцию Direct Federation](direct-federation.md).

## <a name="what-is-the-experience-for-the-google-user"></a>Как эта возможность выглядит для пользователя Google?
Когда вы отправляете приглашение пользователю Google Gmail, гостевой пользователь должен получить доступ к вашим общим приложениям или ресурсам, используя ссылку, которая содержит контекст клиента. Способ взаимодействия с пользователем зависит от того, выполнил ли он вход в Google:
  - Если гостевой пользователь не выполнил вход в Google, ему предлагается выполнить его.
  - Если гостевой пользователь уже зарегистрирован в Google, ему будет предложено выбрать учетную запись, которую он хочет использовать. Он должен выбрать учетную запись, которую вы использовали, чтобы пригласить его.

Если гостевой пользователь видит ошибку, связанную со слишком длинным заголовком, можно попытаться очистить файлы cookie или открыть окно в анонимном режиме и повторить попытку входа.

![Снимок экрана, показывающий страницу входа Google](media/google-federation/google-sign-in.png)

## <a name="limitations"></a>Ограничения

Группы полностью поддерживают гостевых пользователей Google на всех устройствах. Пользователи Google могут входить в команды из общей конечной точки, например `https://teams.microsoft.com`.

Общие конечные точки других приложений могут не поддерживать пользователей Google. Пользователи Google Guest должны войти, используя ссылку, содержащую сведения о клиенте. Ниже приведены примеры.
  * `https://myapps.microsoft.com/?tenantid=<your tenant id>`
  * `https://portal.azure.com/<your tenant id>`
  * `https://myapps.microsoft.com/<your verified domain>.onmicrosoft.com`

   Если гостевые пользователи Google пытаются использовать такую ссылку, как `https://myapps.microsoft.com` или `https://portal.azure.com`, они получат ошибку.

Вы также можете предоставить гостевым пользователям Google прямую ссылку на приложение или ресурс, если эта ссылка включает сведения о клиенте, например `https://myapps.microsoft.com/signin/Twitter/<application ID?tenantId=<your tenant ID>`. 

## <a name="step-1-configure-a-google-developer-project"></a>Шаг 1. Настройка проекта разработчика Google
Сначала создайте новый проект в консоли разработчиков Google, чтобы получить идентификатор и секрет клиента, которые впоследствии можно добавить в Azure AD. 
1. Перейдите к API-интерфейсам Google на странице https://console.developers.google.com и войдите с помощью учетной записи Google. Мы рекомендуем использовать общую учетную запись Google, используемую командой.
2. Создайте новый проект: на панели мониторинга выберите **Создать проект**, а затем нажмите кнопку **Создать**. На странице "Создание проекта" введите **имя проекта**, а затем нажмите кнопку **Создать**.
   
   ![Снимок экрана, показывающий новую страницу проекта для Google](media/google-federation/google-new-project.png)

3. Убедитесь, что в меню проектов выбран ваш новый проект. Затем откройте меню в верхнем левом углу и выберите **API и сервисы** > **Учетные данные**.

   ![Снимок экрана с параметром учетных данных Google API](media/google-federation/google-api.png)
 
4. Щелкните вкладку с **окном получения согласия OAuth** и введите значение в поле **Имя приложения**. (Оставьте другие параметры без изменений.)

   ![Снимок экрана с параметром экрана подтверждения Google OAuth](media/google-federation/google-oauth-consent-screen.png)

5. Прокрутите страницу до раздела **Авторизованные домены** и введите microsoftonline.com.

   ![Снимок экрана, показывающий раздел "полномочные домены"](media/google-federation/google-oauth-authorized-domains.png)

6. Щелкните **Сохранить**.

7. Перейдите на вкладку **учетные данные** . В меню **Создание учетных данных** выберите **идентификатор клиента OAuth**.

   ![Снимок экрана, показывающий параметр создания учетных данных Google API](media/google-federation/google-api-credentials.png)

8. В разделе **Тип приложения** выберите **Веб-приложение**, а затем в поле **Разрешенные URI перенаправления** ведите следующие URI:
   - `https://login.microsoftonline.com` 
   - `https://login.microsoftonline.com/te/<directory id>/oauth2/authresp` <br>(где `<directory id>` является идентификатором каталога).
   
     > [!NOTE]
     > Чтобы найти идентификатор каталога, перейдите к https://portal.azure.com, в разделе **Azure Active Directory** выберите **Свойства** и скопируйте **идентификатор каталога**.

   ![Снимок экрана, показывающий раздел идентификаторов URI для подправки](media/google-federation/google-create-oauth-client-id.png)

9. Нажмите кнопку **Создать**. Скопируйте идентификатор и секрет клиента, которые будут использоваться при добавлении поставщика удостоверений на портале Azure AD.

   ![Снимок экрана, показывающий идентификатор клиента OAuth и секрет клиента](media/google-federation/google-auth-client-id-secret.png)

## <a name="step-2-configure-google-federation-in-azure-ad"></a>Шаг 2. Настройка федерации с Google в Azure AD 
Теперь необходимо установить идентификатор и секрет клиента Google, введя их на портале Azure AD или с помощью PowerShell. Не забудьте проверить конфигурацию федерации Google. Для этого пригласите себя, используя адрес электронной почты Gmail, и попробуйте активировать приглашение с помощью учетной записи Google, указанной в приглашении. 

#### <a name="to-configure-google-federation-in-the-azure-ad-portal"></a>Настройка федерации с Google на портале Azure AD 
1. Перейдите на [портал Azure](https://portal.azure.com). В области слева выберите **Azure Active Directory**. 
2. Выберите **Организационные связи**.
3. Выберите **Поставщики удостоверений**, а затем нажмите кнопку **Google**.
4. Введите имя. Затем введите идентификатор и секрет клиента, полученные ранее. Щелкните **Сохранить**. 

   ![Снимок экрана, показывающий страницу добавления поставщика удостоверений Google](media/google-federation/google-identity-provider.png)

#### <a name="to-configure-google-federation-by-using-powershell"></a>Настройка федерации Google с помощью PowerShell
1. Установите последнюю версию модуля Azure AD PowerShell для Graph ([AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview)).
2. Выполните следующую команду: `Connect-AzureAD`.
3. При появлении запроса на вход войдите с помощью управляемой учетной записи глобального администратора.  
4. Выполните следующую команду: 
   
   `New-AzureADMSIdentityProvider -Type Google -Name Google -ClientId [Client ID] -ClientSecret [Client secret]`
 
   > [!NOTE]
   > Используйте идентификатор и секрет клиента из приложения, созданного в разделе "Шаг 1. Настройка проекта разработчика Google". Дополнительные сведения см. в статье [New-AzureADMSIdentityProvider](https://docs.microsoft.com/powershell/module/azuread/new-azureadmsidentityprovider?view=azureadps-2.0-preview). 
 
## <a name="how-do-i-remove-google-federation"></a>Как удалить федерацию с Google?
Вы можете удалить настройку федерации с Google. Если вы это сделаете, пользователи Google, которые уже активировали свое приглашение, не смогут войти в систему, но вы можете снова предоставить им доступ к своим ресурсам, удалив их из каталога и повторно пригласив их. 
 
### <a name="to-delete-google-federation-in-the-azure-ad-portal"></a>Чтобы удалить федерацию с Google на портале Azure AD: 
1. Перейдите на [портал Azure](https://portal.azure.com). В области слева выберите **Azure Active Directory**. 
2. Выберите **Организационные связи**.
3. Выберите **поставщики удостоверений**.
4. В строке **Google** выберите контекстное меню ( **...** ) и нажмите кнопку **Удалить**. 
   
   ![Снимок экрана с параметром "Удалить" для поставщика удостоверений социальных сетей](media/google-federation/google-social-identity-providers.png)

1. Выберите **Да**, чтобы подтвердить удаление. 

### <a name="to-delete-google-federation-by-using-powershell"></a>Чтобы удалить федерацию с Google с помощью PowerShell: 
1. Установите последнюю версию модуля Azure AD PowerShell для Graph ([AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview)).
2. Запустите `Connect-AzureAD`.  
4. При появлении запроса на вход войдите с помощью управляемой учетной записи глобального администратора.  
5. Введите следующую команду:

    `Remove-AzureADMSIdentityProvider -Id Google-OAUTH`

   > [!NOTE]
   > Дополнительные сведения см. в статье [Remove-AzureADMSIdentityProvider](https://docs.microsoft.com/powershell/module/azuread/Remove-AzureADMSIdentityProvider?view=azureadps-2.0-preview). 
