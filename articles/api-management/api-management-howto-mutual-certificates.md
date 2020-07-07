---
title: Защита серверных служб с помощью проверки подлинности на основе сертификата клиента
titleSuffix: Azure API Management
description: Узнайте, как защитить фоновые службы посредством проверки подлинности с помощью сертификата клиента в службе Azure API Management
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/08/2020
ms.author: apimpm
ms.openlocfilehash: b0ddf6dda99ee666e3052b5a70e51c7e4208a374
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "80347111"
---
# <a name="how-to-secure-back-end-services-using-client-certificate-authentication-in-azure-api-management"></a>Защита фоновых служб посредством проверки подлинности с помощью сертификата клиента в службе Azure API Management

Управление API позволяет защитить доступ к внутренней службе API с помощью сертификатов клиента. В этом руководстве описывается, как на портале Azure управлять сертификатами в экземпляре службы управления API Azure. Также объясняется, как настроить API для доступа к внутренней службе с помощью сертификата.

Сведения об управлении сертификатами с помощью REST API службы управления API см. в статье, посвященной <a href="https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-certificate-entity">сущности сертификата REST API службы управления API Azure</a>.

## <a name="prerequisites"></a><a name="prerequisites"> </a>Предварительные требования

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

В этом руководстве описано, как настроить для экземпляра службы API Management проверку подлинности с помощью сертификата клиента при доступе из API к фоновой службе. Перед выполнением действий, описанных в этой статье, необходимо настроить серверную службу для проверки подлинности с использованием сертификата клиента ([для настройки проверки подлинности сертификата в службе приложений Azure см. эту статью][to configure certificate authentication in Azure WebSites refer to this article]). Вам потребуется доступ к сертификату и паролю, чтобы передать их в службу управления API.

## <a name="upload-a-certificate"></a><a name="step1"> </a>Отправка сертификата

> [!NOTE]
> Вместо отправленного сертификата можно использовать сертификат, хранящийся в службе [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) , как показано в этом [примере](https://github.com/galiniliev/api-management-policy-snippets/blob/galin/AkvCert/examples/Look%20up%20Key%20Vault%20certificate%20using%20Managed%20Service%20Identity%20and%20call%20backend.policy.xml).

![Добавление сертификатов клиента](media/api-management-howto-mutual-certificates/apim-client-cert-new.png)

Чтобы передать новый сертификат клиента, выполните действия ниже. Если экземпляр службы управления API еще не создан, выполните инструкции из руководства [Создание экземпляра службы управления API Azure][Create an API Management service instance].

1. На портале Azure перейдите к экземпляру службы управления API Azure.
2. В меню выберите **Сертификаты** .
3. Щелкните **+ Добавить**.
    ![Добавление сертификатов клиента](media/api-management-howto-mutual-certificates/apim-client-cert-add.png)
4. Выберите сертификат и укажите его идентификатор и пароль.
5. Нажмите кнопку **Создать**.

> [!NOTE]
> Сертификат должен быть в формате **PFX** . Разрешено использовать самозаверяющие сертификаты.

После отправки сертификат отображается в **сертификатах**.  Если у вас несколько сертификатов, сохраните отпечаток необходимого сертификата, чтобы [настроить API для аутентификации шлюза на основе сертификата клиента][Configure an API to use a client certificate for gateway authentication].

> [!NOTE]
> Чтобы отключить проверку цепочки сертификатов при использовании, например, самозаверяющего сертификата, выполните действия, описанные в [этом разделе](api-management-faq.md#can-i-use-a-self-signed-tlsssl-certificate-for-a-back-end) часто задаваемых вопросов.

## <a name="delete-a-client-certificate"></a><a name="step1a"> </a>Удаление сертификата клиента

Чтобы удалить сертификат, щелкните контекстное меню **...** и выберите **Удалить** рядом с именем сертификата.

![Удаление сертификатов клиента](media/api-management-howto-mutual-certificates/apim-client-cert-delete-new.png)

Если сертификат используется интерфейсом API, появляется предупреждение. Перед удалением сертификата нужно сначала удалить его из всех API, которые настроены на его использование.

![Ошибка при удалении сертификата клиента](media/api-management-howto-mutual-certificates/apim-client-cert-delete-failure.png)

## <a name="configure-an-api-to-use-a-client-certificate-for-gateway-authentication"></a><a name="step2"> </a>Настройка API для проверки подлинности шлюза с помощью сертификата клиента

1. Щелкните **Интерфейсы API** в меню **Управление API** слева и перейдите к API.
    ![Включение сертификатов клиента](media/api-management-howto-mutual-certificates/apim-client-cert-enable.png)

2. На вкладке **Конструктор** щелкните значок карандаша в разделе **Внутренняя служба**.
3. Измените значение параметра **Учетные данные шлюза** на **Сертификат клиента** и выберите свой сертификат в раскрывающемся списке.
    ![Включение сертификатов клиента](media/api-management-howto-mutual-certificates/apim-client-cert-enable-select.png)

4. Нажмите кнопку **Сохранить**.

> [!WARNING]
> Изменение вступает в силу немедленно, и для проверки подлинности на фоновом сервере при вызове операций этого API теперь будет использоваться сертификат.


> [!TIP]
> Если сертификат настроен для проверки подлинности шлюза при доступе к серверной службе API, он становится частью политики для этого API, и его можно увидеть в редакторе политики.

## <a name="self-signed-certificates"></a>Самозаверяющие сертификаты

Если вы используете самозаверяющие сертификаты, вам потребуется отключить проверку цепочки сертификатов, чтобы служба управления API могла взаимодействовать с серверной системой. В противном случае будет возвращена ошибка с кодом 500. Для этого можно использовать [`New-AzApiManagementBackend`](https://docs.microsoft.com/powershell/module/az.apimanagement/new-azapimanagementbackend) командлеты PowerShell (для новой серверной части) или [`Set-AzApiManagementBackend`](https://docs.microsoft.com/powershell/module/az.apimanagement/set-azapimanagementbackend) (для существующих серверных интерфейсов) и задать `-SkipCertificateChainValidation` для параметра значение `True` .

```powershell
$context = New-AzApiManagementContext -resourcegroup 'ContosoResourceGroup' -servicename 'ContosoAPIMService'
New-AzApiManagementBackend -Context  $context -Url 'https://contoso.com/myapi' -Protocol http -SkipCertificateChainValidation $true
```

[How to add operations to an API]: api-management-howto-add-operations.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: ../api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[Get started with Azure API Management]: get-started-create-service-instance.md
[API Management policy reference]: api-management-policy-reference.md
[Caching policies]: api-management-policy-reference.md#caching-policies
[Create an API Management service instance]: get-started-create-service-instance.md

[Azure API Management REST API Certificate entity]: https://msdn.microsoft.com/library/azure/dn783483.aspx
[WebApp-GraphAPI-DotNet]: https://github.com/AzureADSamples/WebApp-GraphAPI-DotNet
[to configure certificate authentication in Azure WebSites refer to this article]: ../app-service/app-service-web-configure-tls-mutual-auth.md

[Prerequisites]: #prerequisites
[Upload a client certificate]: #step1
[Delete a client certificate]: #step1a
[Configure an API to use a client certificate for gateway authentication]: #step2
[Test the configuration by calling an operation in the Developer Portal]: #step3
[Next steps]: #next-steps
