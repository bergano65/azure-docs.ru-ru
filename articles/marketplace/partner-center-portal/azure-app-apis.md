---
title: API отправки центра партнеров для подключения приложений Azure в коммерческом магазине Майкрософт
description: Ознакомьтесь с предварительными требованиями для использования API отправки центра партнеров для приложений Azure в коммерческом магазине в центре партнеров Майкрософт.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 12/10/2019
ms.openlocfilehash: 2c37e51ad0c6618e20d9445fab7472b1a3a72ab9
ms.sourcegitcommit: 3beb067d5dc3d8895971b1bc18304e004b8a19b3
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/04/2020
ms.locfileid: "82744897"
---
# <a name="partner-center-submission-api-to-onboard-azure-apps-in-partner-center"></a>API отправки центра партнеров для подключения приложений Azure в центре партнеров

Используйте *API отправки центра партнеров* для программного запроса, создания отправок и публикации предложений Azure.  Этот API полезен, если ваша учетная запись управляет множеством предложений и вы хотите автоматизировать и оптимизировать процесс отправки для этих предложений.

## <a name="api-prerequisites"></a>Предварительные требования для API

Для использования API центра партнеров для продуктов Azure требуется несколько программных ресурсов: 

- приложение Azure Active Directory.
- маркер доступа Azure Active Directory (Azure AD).

### <a name="step-1-complete-prerequisites-for-using-the-partner-center-submission-api"></a>Шаг 1. завершение предварительных требований для использования API отправки центра партнеров

Прежде чем приступить к написанию кода для вызова API отправки центра партнеров, убедитесь, что выполнены следующие предварительные требования.

- У вас (или у вашей организации) должен быть каталог Azure AD, а также разрешение [глобального администратора](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) для этого каталога. Если вы уже используете Office 365 или другие бизнес-службы Майкрософт, то у вас уже есть катало Azure AD. В противном случае вы можете [создать новую службу Azure AD в центре партнеров](https://docs.microsoft.com/windows/uwp/publish/associate-azure-ad-with-partner-center#create-a-brand-new-azure-ad-to-associate-with-your-partner-center-account) без дополнительной платы.

- Необходимо [связать приложение Azure AD с учетной записью центра партнеров](https://docs.microsoft.com/windows/uwp/monetize/create-and-manage-submissions-using-windows-store-services#associate-an-azure-ad-application-with-your-windows-partner-center-account) и получить идентификатор клиента, идентификатор и ключ клиента. Эти данные вам понадобятся для получения маркера доступа Azure AD, который будет использоваться в вызовах к API отправки в Microsoft Store.

#### <a name="how-to-associate-an-azure-ad-application-with-your-partner-center-account"></a>Как связать приложение Azure AD с учетной записью центра партнеров

Чтобы использовать API отправки Microsoft Store, необходимо связать приложение Azure AD с учетной записью центра партнеров, получить идентификатор клиента и идентификатор клиента для приложения и создать ключ. Приложение Azure AD представляет приложение или службу, из которых вы хотите вызвать API отправки центра партнеров. Чтобы получить маркер доступа Azure AD, который вы передадите в API, необходимо иметь в наличии идентификатор владельца, идентификатор клиента и ключ.

>[!Note]
>Эту задачу необходимо выполнить только один раз. После того как вы получите идентификатор владельца, идентификатор клиента и ключ, их можно будет использовать повторно в любое время для создания нового маркера доступа Azure AD.

1. В центре партнеров [свяжите учетную запись центра партнеров Организации с каталогом Azure AD вашей организации](https://docs.microsoft.com/windows/uwp/publish/associate-azure-ad-with-partner-center).
1. Затем на странице **Пользователи** в разделе " **Параметры учетной записи** " центра партнеров [добавьте приложение Azure AD](https://docs.microsoft.com/windows/uwp/publish/add-users-groups-and-azure-ad-applications#add-azure-ad-applications-to-your-partner-center-account) , которое представляет приложение или службу, которые будут использоваться для доступа к отправку учетной записи центра партнеров. Обязательно назначьте этому приложению роль **Менеджер**. Если приложение еще не существует в каталоге Azure AD, вы можете [создать новое приложение Azure AD в центре партнеров](https://docs.microsoft.com/windows/uwp/publish/add-users-groups-and-azure-ad-applications#create-a-new-azure-ad-application-account-in-your-organizations-directory-and-add-it-to-your-partner-center-account).
1. Вернитесь на страницу **Пользователи**, щелкните имя приложения Azure AD, чтобы перейти к параметрам приложения, и скопируйте значения **идентификатора владельца** и **идентификатора клиента**.
1. Нажмите кнопку **Добавить новый ключ**. На следующем экране скопируйте **ключ**. Вы не сможете получить доступ к этой информации после закрытия страницы. Дополнительные сведения см. в разделе [Управление ключами для приложения Azure AD](https://docs.microsoft.com/windows/uwp/publish/add-users-groups-and-azure-ad-applications#manage-keys).

### <a name="step-2-obtain-an-azure-ad-access-token"></a>Шаг 2. Получение маркера доступа Azure AD

Перед вызовом любого из методов в API отправки центра партнеров необходимо сначала получить маркер доступа Azure AD, который передается в заголовок **authorization** для каждого метода в API. После получения маркера доступа у вас будет 60 минут, чтобы использовать его до окончания срока действия маркера. По истечении срока действия маркера можно обновить маркер, чтобы его можно было использовать в последующих вызовах API.

Чтобы получить маркер доступа, следуйте инструкциям из статьи [вызовы службы, используя учетные данные клиента](https://azure.microsoft.com/documentation/articles/active-directory-protocols-oauth-service-to-service/) для отправки `HTTP POST` в `https://login.microsoftonline.com/<tenant_id>/oauth2/token` конечную точку. Ниже приведен пример запроса.

жсонкопи
```Json
POST https://login.microsoftonline.com/<tenant_id>/oauth2/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded; charset=utf-8

grant_type=client_credentials
&client_id=<your_client_id>
&client_secret=<your_client_secret>
&resource= https://api.partner.microsoft.com
```

Для *tenant_id* значений в параметрах `POST URI` и *client_id* и *client_secret* укажите идентификатор клиента, идентификатор клиента и ключ для приложения, полученные из центра партнеров в предыдущем разделе. Для параметра *resource* укажите `https://api.partner.microsoft.com`.

### <a name="step-3-use-the-microsoft-store-submission-api"></a>Шаг 3. Использование API отправки в Microsoft Store

После получения маркера доступа Azure AD вы можете вызвать методы в API отправки центра партнеров. Для создания или обновления отправок обычно вызывается несколько методов в API отправки центра партнеров в определенном порядке. Сведения о каждом сценарии и синтаксисе каждого метода см. в разделе Swagger API приема.

https://apidocs.microsoft.com/services/partneringestion/

## <a name="next-steps"></a>Дальнейшие действия

* Узнайте, как создать [технический ресурс виртуальной машины Azure](create-azure-container-technical-assets.md) .
* Узнайте, описывается создать [предложение контейнера Azure](create-azure-container-offer.md)
