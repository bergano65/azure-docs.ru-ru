---
title: Обзор портала разработчиков Azure API Management
titleSuffix: Azure API Management
description: Узнайте о портале разработчиков в API Management.
services: api-management
documentationcenter: API Management
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 03/15/2020
ms.author: apimpm
ms.openlocfilehash: fefa5ff5d112b479110d484ee0ea4c358b5c88a7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335914"
---
# <a name="azure-api-management-developer-portal-overview"></a>Обзор портала разработчика в службе управления API Azure

Портал разработчика — это автоматически созданный, полностью настраиваемый веб-сайт с документацией ваших AA. Это место, где потребители API могут открыть для себя ваши API, узнать, как их использовать, запросить доступ и попробовать их.

В этой статье описаны различия между самоходной и управляемой версиями портала разработчиков в API Management. Он также объясняет свою архитектуру и дает ответы на часто задаваемые вопросы.

![Портал разработчика API Management](media/api-management-howto-developer-portal/cover.png)

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="managed-and-self-hosted-versions"></a><a name="managed-vs-self-hosted"></a>Управляемые и самоуправляемые версии

Вы можете создать портал разработчика двумя способами:

- **Управляемая версия** - путем редактирования и настройки портала, который встроен в экземпляр Управления API и доступен через URL. `<your-api-management-instance-name>.developer.azure-api.net` Обратитесь к [этой статье документации,](api-management-howto-developer-portal-customize.md) чтобы узнать, как получить доступ и настроить управляемый портал.
- **Самохлаженная версия** - путем развертывания и самостоятельного размещения портала за пределами экземпляра Управления API. Такой подход позволяет отсеивать кодовую базу портала и расширять предоставленную основную функциональность - например, реализовывать пользовательские виджеты для интеграции с сторонними системами. В этом случае вы являетесь сопровождающим портала и отвечаете за обновление портала до последней версии. Для получения подробной информации и инструкций обратитесь в [репозиторий GitHub с исходным кодом портала][1] и [учебником по реализации виджета.][3] [Учебник для управляемой версии](api-management-howto-developer-portal-customize.md) проходит через административную панель портала, которая является общей для управляемых и самоуправляемых версий.

## <a name="portal-architectural-concepts"></a>Архитектурные концепции портала

Компоненты портала можно логически разделить на две категории: *код* и *контент.*

*Код* сохраняется в [репозитории GitHub][1] и включает в себя:

- Виджеты - которые представляют визуальные элементы и сочетают HTML, JavaScript, способность укладки, настройки и отображение содержимого. Примерами являются изображение, текстовый абзац, форма, список AA и т.д.
- Определения стиля - которые указывают, как виджеты могут быть стилизованы
- Двигатель - который генерирует статические веб-страницы из содержимого портала и написан в JavaScript
- Визуальный редактор - который позволяет в браузере настройки и авторизации опыт

*Содержимое* делится на две подкатегории: *содержание портала* и *содержание Управления API.*

*Содержимое портала* характерно для портала и включает в себя:

- Страницы - например, целевая страница, API учебники, сообщения в блоге
- Медиа - изображения, анимация и другой контент на основе файлов
- Макеты - шаблоны, которые сопоставляются с URL-адресом и определяют, как отображаются страницы
- Стили - значения для определений стиля, например, шрифты, цвета, границы
- Настройки - конфигурация, например, значок, метаданные веб-сайта

*Содержание портала,* за исключением средств массовой информации, выражается в документах JSON.

*Содержание API Management* включает такие объекты, как API, Операции, Продукты, Подписки.

Портал основан на адаптированной вилке [рамки Paperbits.](https://paperbits.io/) Первоначальная функциональность Paperbits была расширена, чтобы предоставить виджеты для управления API (например, список API, список продуктов) и разъем для службы управления API для сохранения и извлечения содержимого.

## <a name="frequently-asked-questions"></a><a name="faq"></a>Часто задаваемые вопросы

В этом разделе мы отвечаем на общие вопросы о портале разработчиков, которые носят общий характер. Для вопросов, характерных для самостоятельной версии, обратитесь к [разделу вики репозитория GitHub](https://github.com/Azure/api-management-developer-portal/wiki).

### <a name="how-can-i-migrate-from-the-preview-version-of-the-portal"></a><a id="preview-to-ga"/>Как перейти из предварительной версии портала?

Используя предварительную версию портала разработчика, вы обукомплектовали содержимое предварительного просмотра в службе управления API. Содержимое по умолчанию было значительно изменено в общедоступной версии для улучшения пользовательского опыта. Она также включает в себя новые виджеты.

Если вы используете управляемую версию, сбросить содержимое портала, нажав **содержимое сбросить** в разделе меню **операций.** Подтверждение этой операции удалит все содержимое портала и предоставит новое содержимое по умолчанию. Движок портала был автоматически обновлен в службе управления API.

![Сбросить содержимое портала](media/api-management-howto-developer-portal/reset-content.png)

Если вы используете самоходной версию, `scripts/generate.bat` и из `scripts/cleanup.bat` репозитория GitHub, чтобы удалить существующий контент и предоставить новое содержимое. Заранее убедись, что код портала обновился до последнего выпуска из репозитория GitHub.

Если вы не хотите сбросить содержимое портала, вы можете использовать новые виджеты на страницах. Существующие виджеты были автоматически обновлены до последних версий.

Если ваш портал был подготовлен после объявления общего доступности, он уже должен иметь новое содержимое по умолчанию. Никаких действий с вашей стороны не требуется.

### <a name="how-can-i-migrate-from-the-old-developer-portal-to-the-developer-portal"></a>Как перейти с старого портала разработчиков на портал разработчиков?

Порталы несовместимы, и необходимо перенести содержимое вручную.

### <a name="does-the-portal-have-all-the-features-of-the-old-portal"></a>Есть ли у портала все особенности старого портала?

Портал разработчика больше не поддерживает *приложения* и *проблемы.*

Аутентификация с OAuth в интерактивной консоли разработчика пока не поддерживается. Вы можете отслеживать прогресс через [проблему GitHub.](https://github.com/Azure/api-management-developer-portal/issues/208)

### <a name="has-the-old-portal-been-deprecated"></a>Старый портал был обезвлечен?

Старые порталы разработчика и издателя теперь являются *устаревшими* функциями - они будут получать только обновления безопасности. Новые функции будут реализованы только на новом портале разработчиков.

Отдельно будет объявлено об устаревшем портале. Если у вас есть вопросы, проблемы или комментарии, поднимите их [в специальном выпуске GitHub.](https://github.com/Azure/api-management-developer-portal/issues/121)

### <a name="functionality-i-need-isnt-supported-in-the-portal"></a>Функциональность, которая мне нужна, не поддерживается на портале

Вы можете открыть [запрос функции](https://aka.ms/apimwish) или [реализовать недостающие функциональные возможности самостоятельно.][3] При реализации функции можно либо самостоятельно разместить портал разработчика, либо открыть запрос на участие в GitHub, чтобы включить изменения в управляемую версию.

### <a name="how-can-i-automate-portal-deployments"></a>Как автоматизировать развертывание портала?

Вы можете программно получать доступ к содержимому портала разработчика через REST API, независимо от того, используете ли вы управляемую или самоходной версию.

API задокументирован в [разделе вики-изображения репозитория GitHub.][2] Его можно использовать для автоматизации миграций содержимого портала между средами - например, от тестовой среды к производственной среде. Вы можете узнать больше об этом процессе [в этой статье документации](https://aka.ms/apimdocs/migrateportal) на GitHub.

### <a name="does-the-portal-support-azure-resource-manager-templates-andor-is-it-compatible-with-api-management-devops-resource-kit"></a>Поддерживает ли портал шаблоны управления ресурсами Azure и/или совместим с набором ресурсов API Management DevOps?

Нет.

### <a name="do-i-need-to-enable-additional-vnet-connectivity-for-the-managed-portal-dependencies"></a>Нужно ли включать дополнительное подключение VNet для управляемых зависимостей портала?

В большинстве случаев - нет.

Если служба управления API находится во внутренней сети VNet, ваш портал разработчиков доступен только внутри сети. Имя хоста управления должно быть разослано с внутренним VIP-персоной службы с машины, которую вы используете для доступа к административному интерфейсу портала. Убедитесь, что конечная точка управления зарегистрирована в DNS. В случае неправильной настройки, вы `Unable to start the portal. See if settings are specified correctly in the configuration (...)`увидите ошибку: .

Если служба управления API находится во внутренней Сети и вы получаете к ней доступ через Application Gateway из Интернета, убедитесь, что включить подключение к порталу разработчика и конечным точкам управления API.

### <a name="i-have-assigned-a-custom-api-management-domain-and-the-published-portal-doesnt-work"></a>Я назначил пользовательский домен Управления API и опубликованный портал не работает

После обновления домена необходимо [переиздать портал](api-management-howto-developer-portal-customize.md#publish) для вступления изменений в силу.

### <a name="i-have-added-an-identity-provider-and-i-cant-see-it-in-the-portal"></a>Я добавил поставщика идентификационных данных и не вижу его на портале

После настройки поставщика идентификационных данных (например, AAD, AAD B2C) необходимо [переиздать портал](api-management-howto-developer-portal-customize.md#publish) для вступления изменений в силу.

### <a name="i-have-set-up-delegation-and-the-portal-doesnt-use-it"></a>Я создал делегацию и портал не использует его

После настройки делегации необходимо [переиздать портал](api-management-howto-developer-portal-customize.md#publish) для вступления изменений в силу.

### <a name="my-other-api-management-configuration-changes-havent-been-propagated-in-the-developer-portal"></a>Другие изменения конфигурации управления API не были распространены на портале разработчиков

Большинство изменений конфигурации (например, VNet, условия ввоза и продукта) требуют [переиздания портала.](api-management-howto-developer-portal-customize.md#publish)

### <a name="im-getting-a-cors-error-when-using-the-interactive-console"></a><a name="cors"></a>Я получаю ошибку CORS при использовании интерактивной консоли

Интерактивная консоль делает запрос API на стороне клиента от браузера. Устраните проблему CORS, добавив [политику CORS](api-management-cross-domain-policies.md#CORS) в API(ы).

Вы можете проверить состояние политики CORS в разделе **обзора портала** службы управления API на портале Azure. Предупреждающее окно указывает на отсутствующий или неправильно настроенный политики.

![Портал разработчика API Management](media/api-management-howto-developer-portal/cors-azure-portal.png)

Автоматически применять политику CORS, нажав на кнопку **Enable CORS.**

Вы также можете включить CORS вручную.

1. Нажмите на **Руководство применить его на глобальном уровне** ссылку, чтобы увидеть сгенерированный код политики.
2. Перейдите на **все API** в разделе **API** службы управления API на портале Azure.
3. Нажмите **</>** на значок в разделе **Входящие обработки.**
4. Вставьте политику **<inbound>** в раздел файла XML. Убедитесь, **<origin>** что значение соответствует домену портала разработчика.

> [!NOTE]
> 
> Если вы применяете политику CORS в области продукта вместо области API(s) и aPI использует проверку подлинности ключей подписки через заголовок, консоль не будет работать.
>
> Браузер автоматически выдает запрос OPTIONS HTTP, который не содержит заголовок с ключом подписки. Из-за отсутствия ключа подписки Руководство API не может связать вызов OPTIONS с продуктом, поэтому оно не может применять политику CORS.
>
> В качестве обходного пути вы можете передать ключ подписки в параметре запроса.

### <a name="what-permissions-do-i-need-to-edit-the-developer-portal"></a>Какие разрешения мне нужны для отоденательного портала разработчиков?

Если вы видите `Oops. Something went wrong. Please try again later.` ошибку при открытии портала в административном режиме, возможно, вам может не хватать необходимых разрешений (RBAC).

Устаревшие порталы требовали разрешения `Microsoft.ApiManagement/service/getssotoken/action` в`/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ApiManagement/service/<apim-service-name>`области обслуживания (), чтобы разрешить администратору пользователя доступ к порталам. Новый портал требует `Microsoft.ApiManagement/service/users/token/action` разрешения `/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ApiManagement/service/<apim-service-name>/users/1`на прицел .

Для создания роли с требуемым разрешением можно использовать следующий скрипт PowerShell. Не забудьте `<subscription-id>` изменить параметр. 

```PowerShell
#New Portals Admin Role 
Import-Module Az 
Connect-AzAccount 
$contributorRole = Get-AzRoleDefinition "API Management Service Contributor" 
$customRole = $contributorRole 
$customRole.Id = $null
$customRole.Name = "APIM New Portal Admin" 
$customRole.Description = "This role gives the user ability to log in to the new Developer portal as administrator" 
$customRole.Actions = "Microsoft.ApiManagement/service/users/token/action" 
$customRole.IsCustom = $true 
$customRole.AssignableScopes.Clear() 
$customRole.AssignableScopes.Add('/subscriptions/<subscription-id>') 
New-AzRoleDefinition -Role $customRole 
```
 
После создания роли она может быть предоставлена любому пользователю из раздела **Управления доступом (IAM)** на портале Azure. Назначение этой роли пользователю присваивайте разрешение в области службы. Пользователь сможет создавать токены SAS от имени *любого* пользователя службы. Как минимум, эта роль должна быть назначена администратору службы. Следующая команда PowerShell демонстрирует, как назначить `user1` роль пользователю с наименьшей областью, чтобы избежать предоставления ненужных разрешений пользователю: 

```PowerShell
New-AzRoleAssignment -SignInName "user1@contoso.com" -RoleDefinitionName "APIM New Portal Admin" -Scope "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ApiManagement/service/<apim-service-name>/users/1" 
```

После того, как разрешения были предоставлены пользователю, пользователь должен войти и снова войти на портал Azure для вступления в силу новых разрешений.

### <a name="im-seeing-the-unable-to-start-the-portal-see-if-settings-are-specified-correctly--error"></a>Я вижу ошибку `Unable to start the portal. See if settings are specified correctly (...)`

Эта ошибка отображается `GET` при `https://<management-endpoint-hostname>/subscriptions/xxx/resourceGroups/xxx/providers/Microsoft.ApiManagement/service/xxx/contentTypes/document/contentItems/configuration?api-version=2018-06-01-preview` сбоях вызова. Звонок выдается из браузера административным интерфейсом портала.

Если служба управления API находится в VNet - обратитесь к вопросу о подключении VNet выше.

Сбой вызова также может быть вызван сертификатом TLS/SSL, который присваивается пользовательскому домену и не доверяют браузеру. В качестве меры по смягчению последствий можно удалить пользовательский домен управления - API Management вернется к конечной точке по умолчанию с надежным сертификатом.

### <a name="whats-the-browser-support-for-the-portal"></a>Какова поддержка браузера для портала?

| Браузер                     | Поддерживается       |
|-----------------------------|-----------------|
| Apple Safari;                | Да<sup>1</sup> |
| Google Chrome               | Да<sup>1</sup> |
| Microsoft Edge              | Да<sup>1</sup> |
| Microsoft Internet Explorer | нет              |
| Mozilla Firefox             | Да<sup>1</sup> |

 <small><sup>1</sup> Поддерживается в двух последних серийных версиях.</small>

## <a name="next-steps"></a>Дальнейшие действия

Узнайте больше о новом портале разработчиков:

- [Доступ и настройка управляемого портала разработчиков](api-management-howto-developer-portal-customize.md)
- [Настройка самоходной версии портала][2]
- [Реализовать свой собственный виджет][3]

Просмотр других ресурсов:

- [Репозиторий GitHub с исходным кодом][1]

[1]: https://aka.ms/apimdevportal
[2]: https://github.com/Azure/api-management-developer-portal/wiki
[3]: https://aka.ms/apimdevportal/extend