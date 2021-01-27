---
title: Создание конечной точки SCIM для подготовки пользователей к приложениям из Azure Active Directory
description: System for Cross-domain Identity Management (SCIM) стандартизирует автоматическую подготовку пользователей. Научитесь разрабатывать конечную точку SCIM, интегрировать API SCIM с Azure Active Directory и начать автоматизировать подготовку пользователей и групп в облачных приложениях с помощью Azure Active Directory.
services: active-directory
author: kenwith
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: conceptual
ms.date: 01/27/2021
ms.author: kenwith
ms.reviewer: arvinh
ms.openlocfilehash: 34fa76197c4e08cffd1d8c66d6877b3e427e9fd6
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/27/2021
ms.locfileid: "98918150"
---
# <a name="tutorial-develop-a-sample-scim-endpoint"></a>Руководство. Разработка образца конечной точки SCIM

Никто не хочет создавать новую конечную точку с нуля, поэтому мы создали некоторый [справочный код](https://aka.ms/scimreferencecode) , чтобы приступить к работе с [scim](https://aka.ms/scimoverview). В этом руководстве описывается, как развернуть справочный код SCIM в Azure и протестировать его с помощью процедуры POST или путем интеграции с клиентом Azure AD SCIM. Вы можете получить и запустить конечную точку SCIM без кода всего за 5 минут. Этот учебник предназначен для разработчиков, желающих начать работу с SCIM или другими пользователями, заинтересованными в тестировании конечной точки СИКМ. 

Из этого руководства вы узнаете, как выполнять следующие задачи:

> [!div class="checklist"]
> * Развертывание конечной точки SCIM в Azure
> * Тестирование конечной точки SCIM

## <a name="deploy-your-scim-endpoint-in-azure"></a>Развертывание конечной точки SCIM в Azure

Приведенные здесь действия позволяют развернуть конечную точку SCIM в службе с помощью [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) и [служб приложений Azure](https://docs.microsoft.com/azure/app-service/). Справочный код SCIM также можно запустить локально, разместить на локальном сервере или развернуть в другой внешней службе. 

### <a name="open-solution-and-deploy-to-azure-app-service"></a>Открытие решения и развертывание в службе приложений Azure

1. Перейдите к [коду ссылки](https://github.com/AzureAD/SCIMReferenceCode) из GitHub и выберите **клонировать или скачать**.

1. Выберите либо **Открыть в рабочем столе**, либо скопировать ссылку, открыть **Visual Studio** и выбрать Копировать **или извлечь код** , чтобы ввести скопированную ссылку и создать локальную копию.

1. В **Visual Studio** обязательно войдите в учетную запись, которая имеет доступ к ресурсам размещения.

1. В **Обозреватель решений** откройте *Microsoft. scim. sln* и щелкните правой кнопкой мыши файл *Microsoft. scim. вебхостсампле* . Нажмите кнопку **Опубликовать**.

    ![Публикация в облаке](media/use-scim-to-build-users-and-groups-endpoints/cloud-publish.png)

    > [!NOTE]
    > Чтобы запустить это решение локально, дважды щелкните проект и выберите **IIS Express** , чтобы запустить проект как веб-страницу с URL-адресом локального узла.

1. Выберите **создать профиль** и установите флажок **служба приложений** и **создать новые** .

    ![Публикация в облаке 2](media/use-scim-to-build-users-and-groups-endpoints/cloud-publish-2.png)

1. Пошаговое применение параметров диалогового окна и переименование приложения в нужное имя. Это имя используется как в приложении, так и в URL-адресе конечной точки SCIM.

    ![Публикация в облаке 3](media/use-scim-to-build-users-and-groups-endpoints/cloud-publish-3.png)

1. Выберите группу ресурсов для использования и нажмите кнопку **опубликовать**.

1. Перейдите к приложению в конфигурации **служб приложений Azure**  >   и выберите **новый параметр приложения** , чтобы добавить параметр *Token__TokenIssuer* со значением `https://sts.windows.net/<tenant_id>/` . Замените `<tenant_id>` Tenant_id Azure AD, а если вы хотите протестировать конечную точку scim с помощью [POST](https://github.com/AzureAD/SCIMReferenceCode/wiki/Test-Your-SCIM-Endpoint), добавьте также параметр *ASPNETCORE_ENVIRONMENT* со значением `Development` . 

   ![Параметры appservice](media/use-scim-to-build-users-and-groups-endpoints/app-service-settings.png)

   При тестировании конечной точки с корпоративным приложением в портал Azure выберите параметр Сохранить окружение как `Development` и предоставить маркер, созданный на основе `/scim/token` конечной точки для тестирования, или измените среду на `Production` , а поле токен оставьте пустым в корпоративном приложении в [портал Azure](https://docs.microsoft.com/azure/active-directory/app-provisioning/use-scim-to-provision-users-and-groups#step-4-integrate-your-scim-endpoint-with-the-azure-ad-scim-client). 

Вот и все! Конечная точка SCIM теперь опубликована и позволяет использовать URL-адрес службы приложений Azure для проверки конечной точки SCIM.

## <a name="test-your-scim-endpoint"></a>Тестирование конечной точки SCIM

Для запросов к конечной точке SCIM требуется авторизация, а стандарт SCIM сохраняет несколько вариантов проверки подлинности и авторизации, например файлы cookie, обычную проверку подлинности клиента TLS или любой из методов, перечисленных в [RFC 7644](https://tools.ietf.org/html/rfc7644#section-2).

Не забывайте о незащищенных методах, таких как имя пользователя и пароль, в пользу более безопасного метода, такого как OAuth. Azure AD поддерживает долгосрочные токены носителя (для приложений коллекции и не из коллекции) и предоставление авторизации OAuth (для приложений, опубликованных в коллекции приложений).

> [!NOTE]
> Методы авторизации, предоставляемые в репозитории, предназначены только для тестирования. При интеграции с Azure AD вы можете ознакомиться с руководством по авторизации в разделе [планирование подготовки для конечной точки scim](https://docs.microsoft.com/azure/active-directory/app-provisioning/use-scim-to-provision-users-and-groups#authorization-for-provisioning-connectors-in-the-application-gallery). 

Среда разработки включает функции, небезопасный для рабочей среды, такие как справочный код для управления поведением проверки маркера безопасности. Код проверки маркера настроен для использования самозаверяющего маркера безопасности, а ключ подписывания хранится в файле конфигурации, см. параметр **Token: иссуерсигнингкэй** в *appsettings.Development.jsв* файле.

```json
"Token": {
    "TokenAudience": "Microsoft.Security.Bearer",
    "TokenIssuer": "Microsoft.Security.Bearer",
    "IssuerSigningKey": "A1B2C3D4E5F6A1B2C3D4E5F6",
    "TokenLifetimeInMins": "120"
}
```

> [!NOTE]
> Отправляя в  `/scim/token` конечную точку запрос GET, маркер выдается с помощью настроенного ключа и может использоваться в качестве токена носителя для последующей авторизации.

Код проверки токена по умолчанию настроен для использования маркера, выданного Azure Active Directory, и требует, чтобы клиент, выполняющий выдачу, был настроен с помощью параметра **Token: токениссуер** в *appsettings.js* файла.

``` json
"Token": {
    "TokenAudience": "8adf8e6e-67b2-4cf2-a259-e3dc5476c621",
    "TokenIssuer": "https://sts.windows.net/<tenant_id>/"
}
```

### <a name="use-postman-to-test-endpoints"></a>Использование POST для тестирования конечных точек

После развертывания конечной точки SCIM можно проверить, что она совместима с SCIM RFC. В этом примере представлен набор тестов в **POST** для проверки операций CRUD для пользователей и групп, фильтрации, обновления членства в группах и отключения пользователей.

Конечные точки находятся в `{host}/scim/` каталоге и могут взаимодействовать с помощью стандартных HTTP-запросов. Сведения об изменении `/scim/` маршрута см. в разделе *ControllerConstant.CS* in **азуреадпровисионингсЦимреференце**  >  **сЦимреференцеапи**  >  **Controllers**.

> [!NOTE]
> Конечные точки HTTP можно использовать только для локальных тестов, так как служба подготовки Azure AD требует, чтобы конечная точка поддерживала HTTPS.

#### <a name="open-postman-and-run-tests"></a>Открыть POST и запустить тесты

1. Загрузите программу [POST](https://www.getpostman.com/downloads/) и запустите приложение.
1. Скопируйте ссылку [https://aka.ms/ProvisioningPostman](https://aka.ms/ProvisioningPostman) и вставьте ее в POST, чтобы импортировать тестовую коллекцию.

    ![Публикация коллекции](media/use-scim-to-build-users-and-groups-endpoints/postman-collection.png)

1. Создайте тестовую среду с переменными ниже:

   |Среда|Переменная|Значение|
   |-|-|-|
   |Запуск проекта локально с помощью IIS Express|||
   ||**Сервер**|`localhost`|
   ||**порт**.|`:44359`*(не забывайте **:**)*|
   ||**API**|`scim`|
   |Запуск проекта локально с помощью Kestrel|||
   ||**Сервер**|`localhost`|
   ||**порт**.|`:5001`*(не забывайте **:**)*|
   ||**API**|`scim`|
   |Размещение конечной точки в Azure|||
   ||**Сервер**|*(введите URL-адрес SCIM)*|
   ||**порт**.|*(оставьте пустым)*|
   ||**API**|`scim`|

1. Чтобы отправить запрос **Get** в конечную точку маркера и получить маркер безопасности, который будет храниться в переменной **токена** для последующих запросов, используйте для **получения ключа** из коллекции POST. 

   ![Отправить ключ получения](media/use-scim-to-build-users-and-groups-endpoints/postman-get-key.png)

   > [!NOTE]
   > Чтобы сделать конечные точки SCIM безопасными, необходимо получить маркер безопасности перед подключением, и в руководстве используется `{host}/scim/token` Конечная точка для создания самозаверяющего маркера.

Вот и все! Теперь можно запустить коллекцию **posts** для проверки функции КОНЕЧНОЙ точки scim.

## <a name="next-steps"></a>Next Steps

Сведения о разработке SCIM-совместимой конечной точки пользователя и группы с поддержкой взаимодействия для клиента см. в описании [реализации клиента scim](http://www.simplecloud.info/#Implementations2).

> [!div class="nextstepaction"]
> [Учебник. Разработка и планирование подготовки для конечной точки scim](use-scim-to-provision-users-and-groups.md) 
>  [Учебник. Настройка подготовки для приложения из коллекции](configure-automatic-user-provisioning-portal.md)