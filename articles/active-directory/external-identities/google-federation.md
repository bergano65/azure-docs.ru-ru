---
title: Добавление Google в качестве поставщика удостоверений для B2B — Azure AD
description: Установите федерацию с Google, чтобы гостевые пользователи входили в свои приложения Azure AD с помощью учетных записей Gmail.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 05/11/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: a8c07a6a67e97deafa2f611b297574a801aa356d
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/23/2020
ms.locfileid: "92441952"
---
# <a name="add-google-as-an-identity-provider-for-b2b-guest-users"></a>Добавление Google в качестве поставщика удостоверений для гостевых пользователей B2B

Настроив федерацию с Google, можно разрешить приглашенным пользователям входить в общие приложения и ресурсы с помощью собственных учетных записей Gmail без необходимости создавать учетные записи Майкрософт (MSA). 

> [!NOTE]
> Федерация Google предназначена специально для пользователей Gmail. Чтобы создать федерацию с доменами G Suite, используйте [функцию "Прямая федерация"](direct-federation.md).

## <a name="what-is-the-experience-for-the-google-user"></a>Как эта возможность выглядит для пользователя Google?
Когда вы отправляете приглашение пользователю Google Gmail, гостевой пользователь должен получить доступ к вашим общим приложениям или ресурсам, используя ссылку, которая содержит контекст клиента. Способ взаимодействия с пользователем зависит от того, выполнил ли он вход в Google:
  - Если гостевой пользователь не выполнил вход в Google, ему предлагается выполнить его.
  - Если гостевой пользователь уже зарегистрирован в Google, ему будет предложено выбрать учетную запись, которую он хочет использовать. Он должен выбрать учетную запись, которую вы использовали, чтобы пригласить его.

Если гостевой пользователь видит ошибку, связанную со слишком длинным заголовком, можно попытаться очистить файлы cookie или открыть окно в анонимном режиме и повторить попытку входа.

![Снимок экрана, показывающий страницу входа Google](media/google-federation/google-sign-in.png)

## <a name="limitations"></a>Ограничения

Группы полностью поддерживают гостевых пользователей Google на всех устройствах. Пользователи Google могут входить в службу Teams из общей конечной точки, например `https://teams.microsoft.com`.

Общие конечные точки других приложений могут не поддерживать пользователей Google. Гостевые пользователи Google должны войти используя ссылку, содержащую сведения о клиенте. Ниже приведены примеры.
  * `https://myapps.microsoft.com/?tenantid=<your tenant id>`
  * `https://portal.azure.com/<your tenant id>`
  * `https://myapps.microsoft.com/<your verified domain>.onmicrosoft.com`

   Если гостевые пользователи Google пытаются использовать такую ссылку, как `https://myapps.microsoft.com` или `https://portal.azure.com`, они получат ошибку.

Вы также можете предоставить гостевым пользователям Google прямую ссылку на приложение или ресурс, если эта ссылка включает сведения о клиенте, например `https://myapps.microsoft.com/signin/Twitter/<application ID?tenantId=<your tenant ID>`. 

## <a name="step-1-configure-a-google-developer-project"></a>Шаг 1. Настройка проекта разработчика Google
Сначала создайте новый проект в консоли разработчиков Google, чтобы получить идентификатор и секрет клиента, которые впоследствии можно добавить в Azure AD. 
1. Перейдите к API-интерфейсам Google на странице https://console.developers.google.com и войдите с помощью учетной записи Google. Мы рекомендуем использовать общую учетную запись Google, используемую командой.
2. При появлении запроса примите условия предоставления услуг
3. Создание нового проекта. на панели мониторинга выберите **создать проект**, присвойте проекту имя (например, "Azure AD B2B"), а затем выберите **создать**. 
   
   ![Снимок экрана, показывающий страницу "Новый проект" для Google](media/google-federation/google-new-project.png)

4. На странице **API-интерфейсы & служб** , которая теперь отображается, щелкните **Просмотр** в новом проекте.

5. Щелкните **Переход к API обзор** на карточке API. Выберите **экран согласия OAuth**.

6. Щелкните **Внешний**, а затем — **Создать**. 

7. В **экране согласия OAuth** введите значение в поле **Имя приложения**. 

   ![Снимок экрана с параметром "Google: экран согласия OAuth"](media/google-federation/google-oauth-consent-screen.png)

8. Прокрутите страницу до раздела **Авторизованные домены** и введите microsoftonline.com.

   ![Снимок экрана, показывающий раздел "Авторизованные домены"](media/google-federation/google-oauth-authorized-domains.PNG)

9. Щелкните **Сохранить**.

10. Выберите **Учетные данные**. В меню **Создать учетные данные** выберите **Идентификатор клиента OAuth**.

    ![Снимок экрана, показывающий параметр создания учетных данных Google API](media/google-federation/google-api-credentials.png)

11. В разделе **Тип приложения**выберите **веб-приложение** и присвойте приложению подходящее имя, например "Azure AD B2B", а затем в разделе "допустимые **URI перенаправления**" введите следующие URI:
    - `https://login.microsoftonline.com` 
    - `https://login.microsoftonline.com/te/<tenant id>/oauth2/authresp` <br>(где `<tenant id>` — идентификатор клиента)
   
    > [!NOTE]
    > Чтобы найти идентификатор клиента, перейдите на страницу https://portal.azure.com и в разделе **Azure Active Directory**выберите **Свойства** и скопируйте **идентификатор клиента**.

    ![Снимок экрана, показывающий раздел "Авторизованные URI-коды перенаправления"](media/google-federation/google-create-oauth-client-id.png)

12. Нажмите кнопку **создания**. Скопируйте идентификатор и секрет клиента, которые будут использоваться при добавлении поставщика удостоверений на портале Azure AD.

    ![Снимок экрана, показывающий идентификатор и секрет клиента OAuth](media/google-federation/google-auth-client-id-secret.png)

## <a name="step-2-configure-google-federation-in-azure-ad"></a>Шаг 2. Настройка федерации с Google в Azure AD 
Теперь необходимо установить идентификатор и секрет клиента Google, введя их на портале Azure AD или с помощью PowerShell. Не забудьте проверить конфигурацию федерации Google. Для этого пригласите себя, используя адрес электронной почты Gmail, и попробуйте активировать приглашение с помощью учетной записи Google, указанной в приглашении. 

#### <a name="to-configure-google-federation-in-the-azure-ad-portal"></a>Настройка федерации с Google на портале Azure AD 
1. Перейдите на [портал Azure](https://portal.azure.com). В области слева выберите **Azure Active Directory**. 
2. Выберите **Внешние удостоверения**.
3. Выберите **Все поставщики удостоверений**, а затем нажмите кнопку **Google**.
4. Затем введите идентификатор и секрет клиента, полученные ранее. Щелкните **Сохранить**. 

   ![Снимок экрана: страница добавления поставщика удостоверений Google](media/google-federation/google-identity-provider.png)

#### <a name="to-configure-google-federation-by-using-powershell"></a>Настройка федерации Google с помощью PowerShell
1. Установите последнюю версию модуля Azure AD PowerShell для Graph ([AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview)).
2. Выполните следующую команду: `Connect-AzureAD`.
3. При появлении запроса на вход войдите с помощью управляемой учетной записи глобального администратора.  
4. Выполните следующую команду: 
   
   `New-AzureADMSIdentityProvider -Type Google -Name Google -ClientId [Client ID] -ClientSecret [Client secret]`
 
   > [!NOTE]
   > Используйте идентификатор и секрет клиента из приложения, созданного в разделе "Шаг 1: настройка проекта разработчика Google". Дополнительные сведения см. в статье [New-AzureADMSIdentityProvider](/powershell/module/azuread/new-azureadmsidentityprovider?view=azureadps-2.0-preview). 
 
## <a name="how-do-i-remove-google-federation"></a>Как удалить федерацию с Google?
Вы можете удалить настройку федерации с Google. Если вы это сделаете, пользователи Google, которые уже активировали свое приглашение, не смогут войти в систему, но вы можете снова предоставить им доступ к своим ресурсам, удалив их из каталога и повторно пригласив их. 
 
### <a name="to-delete-google-federation-in-the-azure-ad-portal"></a>Чтобы удалить федерацию с Google на портале Azure AD: 
1. Перейдите на [портал Azure](https://portal.azure.com). В области слева выберите **Azure Active Directory**. 
2. Выберите **Внешние удостоверения**.
3. Выберите **Все поставщики удостоверений**.
4. В строке **Google** выберите контекстное меню ( **...** ), а затем выберите **Удалить**. 
   
   ![Снимок экрана с параметром "Удалить" для поставщика удостоверений социальных сетей](media/google-federation/google-social-identity-providers.png)

1. Выберите **Да**, чтобы подтвердить удаление. 

### <a name="to-delete-google-federation-by-using-powershell"></a>Чтобы удалить федерацию с Google с помощью PowerShell: 
1. Установите последнюю версию модуля Azure AD PowerShell для Graph ([AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview)).
2. Выполните `Connect-AzureAD`.  
4. При появлении запроса на вход войдите с помощью управляемой учетной записи глобального администратора.  
5. Введите следующую команду:

    `Remove-AzureADMSIdentityProvider -Id Google-OAUTH`

   > [!NOTE]
   > Дополнительные сведения см. в статье [Remove-AzureADMSIdentityProvider](/powershell/module/azuread/Remove-AzureADMSIdentityProvider?view=azureadps-2.0-preview).