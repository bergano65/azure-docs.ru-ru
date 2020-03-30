---
title: API-найддинг для приложений Azure на коммерческом рынке
description: Предпосылки API для приложений Azure на коммерческом рынке в Партнерской центре Майкрософт.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 12/10/2019
ms.openlocfilehash: cc4d56058ce3985ec3a1d9124ef4ec73ff6be1a2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80279765"
---
# <a name="api-for-onboarding-azure-apps-in-partner-center"></a>API для борта приложений Azure в партнерской сети

Используйте *API представления Partner Center* для программного запроса, создания представлений и публикации предложений Azure.  Этот API полезен, если ваша учетная запись управляет многими предложениями, и вы хотите автоматизировать и оптимизировать процесс представления этих предложений.

## <a name="api-prerequisites"></a>Предпосылки API

Есть несколько программных ресурсов, которые необходимы для использования API-центра partner Center для продуктов Azure: 

- приложение Active Directory Azure.
- доступ к консета доступа Active Directory (Azure AD).

### <a name="step-1-complete-prerequisites-for-using-the-partner-center-submission-api"></a>Шаг 1: Полные предпосылки для использования API представления Центра партнеров

Перед тем, как начать писать код для вызова API представления Центра партнеров, убедитесь, что вы выполнили следующие предпосылки.

- У вас (или у вашей организации) должен быть каталог Azure AD, а также разрешение [глобального администратора](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) для этого каталога. Если вы уже используете Office 365 или другие бизнес-службы Майкрософт, то у вас уже есть катало Azure AD. В противном случае можно [создать новый Azure AD в центре partner](https://docs.microsoft.com/windows/uwp/publish/associate-azure-ad-with-partner-center#create-a-brand-new-azure-ad-to-associate-with-your-partner-center-account) без дополнительной платы.

- Вы должны [связать приложение Azure AD с учетной записью Partner Center](https://docs.microsoft.com/windows/uwp/monetize/create-and-manage-submissions-using-windows-store-services#associate-an-azure-ad-application-with-your-windows-partner-center-account) и получить идентификатор клиента, идентификатор клиента и ключ. Эти данные вам понадобятся для получения маркера доступа Azure AD, который будет использоваться в вызовах к API отправки в Microsoft Store.

#### <a name="how-to-associate-an-azure-ad-application-with-your-partner-center-account"></a>Как связать приложение Azure AD с учетной записью Центра партнеров

Чтобы использовать API представления Microsoft Store, необходимо связать приложение Azure AD с учетной записью Partner Center, получить идентификатор клиента и идентификатор клиента для приложения и создать ключ. Приложение Azure AD представляет приложение или службу, с помощью которой вы хотите вызвать API представления Центра-партнера. Чтобы получить маркер доступа Azure AD, который вы передадите в API, необходимо иметь в наличии идентификатор владельца, идентификатор клиента и ключ.

>[!Note]
>Вам нужно выполнить эту задачу только один раз. После того как вы получите идентификатор владельца, идентификатор клиента и ключ, их можно будет использовать повторно в любое время для создания нового маркера доступа Azure AD.

1. В Partner Center [свяжете учетную запись Центра партнеров организации с каталогом Azure AD вашей организации.](https://docs.microsoft.com/windows/uwp/publish/associate-azure-ad-with-partner-center)
1. Далее, со страницы **пользователей** в разделе **настроек учетной записи** В Центре Партнеров, [добавьте приложение Azure AD,](https://docs.microsoft.com/windows/uwp/publish/add-users-groups-and-azure-ad-applications#add-azure-ad-applications-to-your-partner-center-account) которое представляет приложение или услугу, которые вы будете использовать для доступа к материалам для вашей учетной записи Partner Center. Обязательно назначьте этому приложению роль **Менеджер**. Если приложение еще не существует в каталоге Azure AD, можно [создать новое приложение Azure AD в Partner Center.](https://docs.microsoft.com/windows/uwp/publish/add-users-groups-and-azure-ad-applications#create-a-new-azure-ad-application-account-in-your-organizations-directory-and-add-it-to-your-partner-center-account)
1. Вернитесь на страницу **Пользователи**, щелкните имя приложения Azure AD, чтобы перейти к параметрам приложения, и скопируйте значения **идентификатора владельца** и **идентификатора клиента**.
1. Нажмите кнопку **Добавить новый ключ**. На следующем экране скопируйте **ключ**. Вы не сможете получить доступ к этой информации после закрытия страницы. Дополнительные сведения см. в разделе [Управление ключами для приложения Azure AD](https://docs.microsoft.com/windows/uwp/publish/add-users-groups-and-azure-ad-applications#manage-keys).

### <a name="step-2-obtain-an-azure-ad-access-token"></a>Шаг 2. Получение маркера доступа Azure AD

Прежде чем вызвать любой из методов в API представления Центра партнеров, необходимо сначала получить токен доступа Azure AD, который вы передаете заголовку **авторизации** каждого метода В API. После получения маркера доступа у вас будет 60 минут, чтобы использовать его до окончания срока действия маркера. После истечения срока действия токена можно обновить токен, чтобы продолжить использовать его в будущих вызовах в API.

Чтобы получить токен доступа, следуйте инструкциям в [Службе обслуживания вызовов с использованием учетных данных клиентов](https://azure.microsoft.com/documentation/articles/active-directory-protocols-oauth-service-to-service/) для отправки в конечную `HTTP POST` `https://login.microsoftonline.com/<tenant_id>/oauth2/token` точку. Ниже приведен пример запроса.

JSONCopy
```Json
POST https://login.microsoftonline.com/<tenant_id>/oauth2/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded; charset=utf-8

grant_type=client_credentials
&client_id=<your_client_id>
&client_secret=<your_client_secret>
&resource= https://api.partner.microsoft.com
```

Для *tenant_id* значения `POST URI` в *client_id* и *client_secret* параметров, укажите идентификатор клиента, идентификатор клиента и ключ для вашего приложения, который вы получили из Partner Center в предыдущем разделе. Для параметра *resource* укажите `https://api.partner.microsoft.com`.

### <a name="step-3-use-the-microsoft-store-submission-api"></a>Шаг 3. Использование API отправки в Microsoft Store

После того, как у вас есть конкен доступа Azure AD, вы можете вызвать методы в API представления Partner Center. Для создания или обновления представлений обычно вы вызываете несколько методов в API представления Центра партнеров в определенном порядке. Для получения информации о каждом сценарии и синтаксисе каждого метода, см.

https://apidocs.microsoft.com/services/partneringestion/
