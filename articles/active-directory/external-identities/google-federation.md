---
title: Добавление Google в качестве поставщика удостоверений для B2B — Azure AD
description: Активируйте Федерацию с Google, чтобы позволить гостевым пользователям входить в приложения Azure AD с помощью собственных учетных записей Gmail.
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
ms.openlocfilehash: ff8912794169cf61f394a097248a8476b2e0c0f3
ms.sourcegitcommit: dd45ae4fc54f8267cda2ddf4a92ccd123464d411
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/29/2020
ms.locfileid: "92926241"
---
# <a name="add-google-as-an-identity-provider-for-b2b-guest-users"></a>Добавление Google в качестве поставщика удостоверений для гостевых пользователей B2B

Настроив Федерацию с помощью Google, можно разрешить приглашенным пользователям входить в ваши общие приложения и ресурсы с собственными учетными записями Gmail, не создавая учетные записи Майкрософт. 

> [!NOTE]
> Федерация Google предназначена специально для пользователей Gmail. Чтобы создать федерацию с доменами G Suite, используйте [прямую федерацию](direct-federation.md).

## <a name="what-is-the-experience-for-the-google-user"></a>Как эта возможность выглядит для пользователя Google?
При отправке приглашения пользователям Google Gmail гостевой пользователь должен получить доступ к общим приложениям или ресурсам с помощью ссылки, включающей контекст клиента. Способ взаимодействия с пользователем зависит от того, выполнил ли он вход в Google:
  - Гостевым пользователям, которые не вошли в Google, будет предложено сделать это.
  - Гостевые пользователи, которые уже вошли в Google, получат запрос на выбор учетной записи, которую хотите использовать. Он должен выбрать учетную запись, которую вы использовали, чтобы пригласить его.

Гостевые пользователи, которые видят сообщение об ошибке "слишком длинный заголовок", могут очистить свои файлы cookie или открыть частное или режиме инкогнито окно и повторить попытку входа.

![Снимок экрана, на котором показана страница входа Google.](media/google-federation/google-sign-in.png)

## <a name="limitations"></a>Ограничения

Группы полностью поддерживают гостевых пользователей Google на всех устройствах. Пользователи Google могут входить в службу Teams из общей конечной точки, например `https://teams.microsoft.com`.

Общие конечные точки других приложений могут не поддерживать пользователей Google. Пользователи Google Guest должны войти, используя ссылку, содержащую сведения о клиенте. Ниже приведены примеры.
  * `https://myapps.microsoft.com/?tenantid=<your tenant ID>`
  * `https://portal.azure.com/<your tenant ID>`
  * `https://myapps.microsoft.com/<your verified domain>.onmicrosoft.com`

   Если гостевые пользователи Google пытаются использовать такую ссылку `https://myapps.microsoft.com` , как или `https://portal.azure.com` , то они получат сообщение об ошибке.

Вы также можете предоставить гостевым пользователям Google прямую ссылку на приложение или ресурс, если ссылка включает сведения о клиенте. Например, `https://myapps.microsoft.com/signin/Twitter/<application ID?tenantId=<your tenant ID>`. 

## <a name="step-1-configure-a-google-developer-project"></a>Шаг 1. Настройка проекта разработчика Google
Сначала создайте новый проект в консоли Google Developers, чтобы получить идентификатор клиента и секрет клиента, которые позже можно будет добавить в Azure Active Directory (Azure AD). 
1. Перейдите к API-интерфейсам Google на странице https://console.developers.google.com и войдите с помощью учетной записи Google. Мы рекомендуем использовать общую учетную запись Google, используемую командой.
2. При появлении запроса примите условия предоставления услуг.
3. Создание нового проекта. на панели мониторинга выберите **создать проект** , присвойте проекту имя (например, **Azure AD B2B** ), а затем выберите **создать** : 
   
   ![Снимок экрана, на котором показана новая страница проекта.](media/google-federation/google-new-project.png)

4. На странице **api & службы** выберите **представление** в новом проекте.

5. Выберите пункт **Переход к API обзор** на карточке API. Выберите **экран согласия OAuth** .

6. Щелкните **Внешний** , а затем — **Создать** . 

7. На **экране согласия OAuth** введите **имя приложения** :

   ![Снимок экрана с экраном согласия Google OAuth.](media/google-federation/google-oauth-consent-screen.png)

8. Перейдите к разделу " **полномочные домены** " и введите **microsoftonline.com** :

   ![Снимок экрана, показывающий раздел "полномочные домены".](media/google-federation/google-oauth-authorized-domains.PNG)

9. Щелкните **Сохранить** .

10. Выберите **Credentials** (Учетные данные). В меню **Создание учетных данных** выберите **идентификатор клиента OAuth** :

    ![Снимок экрана, показывающий меню создания учетных данных Google API.](media/google-federation/google-api-credentials.png)

11. Из списка **Application type** (Тип приложения) выберите **Web application** (Веб-приложение). Присвойте приложению подходящее имя, например **Azure AD B2B** . В разделе " **зарегистрированные URI перенаправления** " введите следующие URI:
    - `https://login.microsoftonline.com`
    - `https://login.microsoftonline.com/te/<tenant ID>/oauth2/authresp` <br>(где `<tenant ID>` — идентификатор клиента)
   
    > [!NOTE]
    > Чтобы найти идентификатор клиента, перейдите на [портал Azure](https://portal.azure.com). В разделе **Azure Active Directory** выберите **Свойства** и скопируйте **идентификатор клиента** .

    ![Снимок экрана, показывающий раздел идентификаторов URI для перенаправления.](media/google-federation/google-create-oauth-client-id.png)

12. Нажмите кнопку **создания** . Скопируйте идентификатор клиента и секрет клиента. Они будут использоваться при добавлении поставщика удостоверений в портал Azure.

    ![Снимок экрана, на котором показаны идентификатор клиента OAuth и секрет клиента.](media/google-federation/google-auth-client-id-secret.png)

## <a name="step-2-configure-google-federation-in-azure-ad"></a>Шаг 2. Настройка федерации с Google в Azure AD 
Теперь вы можете задать идентификатор клиента Google и секрет клиента. Для этого можно использовать портал Azure или PowerShell. Не забудьте проверить конфигурацию Google Federation, выполнив приглашение самостоятельно. Используйте адрес Gmail и попробуйте активировать приглашение с помощью приглашенной учетной записи Google. 

**Настройка Google Federation в портал Azure** 
1. Перейдите на [портал Microsoft Azure](https://portal.azure.com). В области слева выберите **Azure Active Directory** . 
2. Выберите **Внешние удостоверения** .
3. Выберите **все поставщики удостоверений** , а затем нажмите кнопку **Google** .
4. Введите идентификатор клиента и секрет клиента, полученные ранее. Выберите **сохранить** : 

   ![Снимок экрана, на котором показана страница добавления поставщика удостоверений Google.](media/google-federation/google-identity-provider.png)

**Настройка федерации Google с помощью PowerShell**
1. Установите последнюю версию модуля Azure AD PowerShell для Graph ([AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview)).
2. Выполните следующую команду: `Connect-AzureAD`
3. При появлении запроса на вход войдите с помощью управляемой учетной записи глобального администратора.  
4. Выполните следующую команду: 
   
   `New-AzureADMSIdentityProvider -Type Google -Name Google -ClientId <client ID> -ClientSecret <client secret>`
 
   > [!NOTE]
   > Используйте идентификатор клиента и секрет клиента из приложения, созданного на шаге 1: Настройка проекта Google Developer. Дополнительные сведения см. в разделе [New-азуреадмсидентитипровидер](/powershell/module/azuread/new-azureadmsidentityprovider?view=azureadps-2.0-preview). 
 
## <a name="how-do-i-remove-google-federation"></a>Как удалить федерацию с Google?
Вы можете удалить настройку федерации с Google. В этом случае пользователи Google Guest, которые уже активировали свои приглашения, не смогут войти в систему. Но вы можете снова предоставить им доступ к ресурсам, удалив их из каталога и повторно приглашая. 
 
**Удаление Google Federation на портале Azure AD**
1. Перейдите на [портал Microsoft Azure](https://portal.azure.com). В области слева выберите **Azure Active Directory** . 
2. Выберите **Внешние удостоверения** .
3. Выберите **Все поставщики удостоверений** .
4. В строке **Google** нажмите кнопку с многоточием ( **...** ), а затем выберите **Удалить** . 
   
   ![Снимок экрана, на котором показана кнопка "Удалить" для поставщика удостоверений социальных сетей.](media/google-federation/google-social-identity-providers.png)

1. Выберите **Да** , чтобы подтвердить удаление. 

**Удаление Google Federation с помощью PowerShell** 
1. Установите последнюю версию модуля Azure AD PowerShell для Graph ([AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview)).
2. Выполните `Connect-AzureAD`.  
4. При появлении запроса на вход войдите с помощью управляемой учетной записи глобального администратора.  
5. Введите следующую команду:

    `Remove-AzureADMSIdentityProvider -Id Google-OAUTH`

   > [!NOTE]
   > Дополнительные сведения см. в статье [Remove-AzureADMSIdentityProvider](/powershell/module/azuread/Remove-AzureADMSIdentityProvider?view=azureadps-2.0-preview).
