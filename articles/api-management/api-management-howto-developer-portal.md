---
title: Обзор портала разработчика управления API Azure — управление API Azure | Документация Майкрософт
description: Сведения о портале разработчика в оснастке управления API.
services: api-management
documentationcenter: API Management
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/22/2019
ms.author: apimpm
ms.openlocfilehash: 2b69fdd7abefca360433fc9fb090569cba23febe
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/24/2019
ms.locfileid: "74454386"
---
# <a name="azure-api-management-developer-portal-overview"></a>Обзор портала разработчика управления API Azure

Портал разработчика — это автоматически создаваемый, полностью настраиваемый веб-сайт с документацией по API. Именно здесь пользователи API могут обнаружить ваши API, узнать, как их использовать, запросить доступ и испытать их.

В этой статье описываются различия между самостоятельным размещением и управляемыми версиями портала разработчика в управлении API. Здесь также объясняется его архитектура и приводятся ответы на часто задаваемые вопросы.

> [!WARNING]
>
> [Узнайте, как выполнить миграцию из предварительной версии в общедоступную версию](#preview-to-ga) портала разработчика.

![Портал разработчика API Management](media/api-management-howto-developer-portal/cover.png)

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="managed-vs-self-hosted"></a>Управляемые и собственные версии

Создать портал разработчика можно двумя способами:

- **Управляемая версия** — путем изменения и настройки портала, который встроен в экземпляр управления API и доступен по URL-адресу `<your-api-management-instance-name>.developer.azure-api.net`. Сведения о том, как получить доступ и настроить управляемый портал, см. в [этой статье документации](api-management-howto-developer-portal-customize.md) .
- **Самостоятельная версия** — путем развертывания и самостоятельного размещения портала за пределами экземпляра управления API. Такой подход позволяет изменять базу кода портала и расширять предоставляемые Основные функции. Также необходимо обновить портал до последней версии. Дополнительные сведения и инструкции см. в [репозитории GitHub с исходным кодом портала][1]. [Руководство по управляемой версии](api-management-howto-developer-portal-customize.md) переходит на панель администрирования портала, которая также включена в версию на собственном сервере.

## <a name="portal-architectural-concepts"></a>Основные понятия архитектуры портала

Компоненты портала можно логически разделить на две категории: *код* и *содержимое*.

*Код* хранится в [репозитории GitHub][1] и включает в себя:

- Мини-приложения — представляют визуальные элементы и объединяют HTML, JavaScript, возможности стилизации, параметры и сопоставление содержимого. Примерами являются изображение, текстовый абзац, форма, список интерфейсов API и т. д.
- Определения стилей, которые определяют, как мини-приложения могут быть оформлены
- Механизм, который создает статические веб-страницы из содержимого портала и написан на JavaScript
- Визуальный редактор, позволяющий настраивать в браузере и создавать возможности для разработки

*Содержимое* разделено на две подкатегории: *содержимое портала* и *содержимое управления API*.

*Содержимое портала* зависит от портала и включает в себя:

- Страницы — например, Целевая страница, учебники по API, записи в блоге
- Мультимедиа-изображения, анимация и другое файлового содержимого
- Макеты — шаблоны, которые сопоставляются с URL-адресом и определяют, как отображаются страницы.
- Стили — значения для определений стилей, например шрифты, цвета, границы
- Параметры — Конфигурация, например фавикон, метаданные веб-сайта

*Содержимое портала*, за исключением носителя, выражается в виде документов JSON.

*Содержимое для управления API* включает такие сущности, как API, операции, продукты и подписки.

Портал построен на основе адаптированной вилки [папербитс Framework](https://paperbits.io/). Первоначальная функциональность Папербитс была расширена для предоставления мини-приложений для управления API (например, списка интерфейсов API, списка продуктов) и соединителя со службой управления API для сохранения и извлечения содержимого.

## <a name="faq"></a>Часто задаваемые вопросы

В этом разделе мы будем отвечать на часто задаваемые вопросы о новом портале разработчика, который является общим характером. Вопросы, относящиеся к локальной версии, см. в [разделе Wiki репозитория GitHub](https://github.com/Azure/api-management-developer-portal/wiki).

### <a name="a-idpreview-to-ga-how-can-i-migrate-from-the-preview-version-of-the-portal"></a><a id="preview-to-ga"/> как выполнить миграцию из предварительной версии портала?

С помощью предварительной версии портала разработчика вы подготовили содержимое для предварительного просмотра в службе управления API. Содержимое по умолчанию значительно изменилось в общедоступной версии для повышения удобства работы пользователей. Он также включает новые мини-приложения.

Если вы используете управляемую версию, сбросьте содержимое портала, нажав кнопку **сбросить содержимое** в разделе меню **операции** . Подтверждение этой операции приведет к удалению всего содержимого портала и подготовке нового содержимого по умолчанию. Модуль портала был автоматически обновлен в службе управления API.

![Сброс содержимого портала](media/api-management-howto-developer-portal/reset-content.png)

Если вы используете локальную версию, используйте `scripts/cleanup.bat` и `scripts/generate.bat` из репозитория GitHub для удаления существующего содержимого и наполнения им нового содержимого. Обязательно обновите код портала до последней версии из репозитория GitHub заранее.

Если вы не хотите сбрасывать содержимое портала, вы можете использовать новые доступные мини-приложения на всех страницах. Существующие мини-приложения были автоматически обновлены до последних версий.

Если портал был подготовлен после объявления общедоступной доступности, он уже должен иметь новое содержимое по умолчанию. Никаких действий с вашей стороны не требуется.

### <a name="how-can-i-migrate-from-the-old-developer-portal-to-the-new-developer-portal"></a>Как перейти со старого портала разработчика на новый портал разработчика?

Порталы несовместимы, и вам нужно перенести содержимое вручную.

### <a name="does-the-new-portal-have-all-the-features-of-the-old-portal"></a>Есть ли у нового портала все функции старого портала?

Новый портал разработчика не поддерживает *приложения* и *проблемы*. Если вы использовали *проблемы* на старом портале и потребуюте их в новой, опубликуйте комментарий в [выделенной ошибке GitHub](https://github.com/Azure/api-management-developer-portal/issues/122).

Проверка подлинности с помощью OAuth в интерактивной консоли разработчика пока не поддерживается. Ход выполнения можно отслеживать с помощью [проблемы GitHub](https://github.com/Azure/api-management-developer-portal/issues/208).

### <a name="has-the-old-portal-been-deprecated"></a>Устарел ли старый портал?

Старые порталы для разработчиков и издателей теперь являются *устаревшими* компонентами — они будут получать только обновления для системы безопасности. Новые возможности будут внедрены только на новом портале разработчика.

Устаревшие устаревшие порталы будут объявлены отдельно. Если у вас есть вопросы, проблемы или комментарии, их следует вызывать [в выделенной проблемы GitHub](https://github.com/Azure/api-management-developer-portal/issues/121).

### <a name="how-can-i-automate-portal-deployments"></a>Как автоматизировать развертывание портала?

Вы можете программно получить доступ к содержимому портала разработчика и управлять им с помощью REST API, независимо от того, используете ли вы управляемую или локальную версию.

API описан в [разделе вики репозитория GitHub][2]. Его также можно использовать для автоматизации миграции содержимого портала между средами, например из тестовой среды в рабочую среду. Дополнительные сведения об этом процессе см. [в этой статье документации](https://aka.ms/apimdocs/migrateportal) на сайте GitHub.

### <a name="does-the-portal-support-azure-resource-manager-templates-andor-is-it-compatible-with-api-management-devops-resource-kit"></a>Поддерживает ли портал Azure Resource Manager шаблоны и/или является ли он совместимым с набором средств API Management DevOps Resource Kit?

Нет

### <a name="do-i-need-to-enable-additional-vnet-connectivity-for-the-new-managed-portal-dependencies"></a>Нужно ли включать дополнительное подключение к виртуальной сети для новых зависимостей управляемого портала?

В большинстве случаев — нет.

Если служба управления API находится во внутренней виртуальной сети, портал разработчика доступен только в пределах сети. Имя узла конечной точки управления должно разрешаться во внутренний виртуальный IP-адрес службы с компьютера, используемого для доступа к административному интерфейсу портала. Убедитесь, что конечная точка управления зарегистрирована в DNS. В случае неявной настройки отображается сообщение об ошибке: `Unable to start the portal. See if settings are specified correctly in the configuration (...)`.

### <a name="i-have-assigned-a-custom-api-management-domain-and-the-published-portal-doesnt-work"></a>Я назначил пользовательский домен управления API, и опубликованный портал не работает

После обновления домена необходимо [повторно опубликовать портал](api-management-howto-developer-portal-customize.md#publish) , чтобы изменения вступили в силу.

### <a name="i-have-added-an-identity-provider-and-i-cant-see-it-in-the-portal"></a>Я добавил поставщик удостоверений и не вижу его на портале.

После настройки поставщика удостоверений (например, AAD, AAD B2C) необходимо [повторно опубликовать портал](api-management-howto-developer-portal-customize.md#publish) , чтобы изменения вступили в силу.

### <a name="i-have-set-up-delegation-and-the-portal-doesnt-use-it"></a>Я настроил делегирование, и портал не использует его

После настройки делегирования необходимо [повторно опубликовать портал](api-management-howto-developer-portal-customize.md#publish) , чтобы изменения вступили в силу.

### <a name="my-other-api-management-configuration-changes-havent-been-propagated-in-the-developer-portal"></a>Изменения конфигурации управления API не были распространены на портале разработчика

Для большинства изменений конфигурации (например, для виртуальной сети, входа и условий продукта) требуется [Повторная публикация портала](api-management-howto-developer-portal-customize.md#publish).

### <a name="im-getting-a-cors-error-when-using-the-interactive-console"></a>При использовании интерактивной консоли возникает ошибка CORS

Интерактивная консоль выполняет клиентский запрос API из браузера. Проблему CORS можно устранить, добавив [политику CORS](api-management-cross-domain-policies.md#CORS) в API-интерфейсы. Можно указать все параметры вручную или использовать `*` подстановочных знаков. Например,

```XML
<cors>
    <allowed-origins>
        <origin>*</origin>
    </allowed-origins>
    <allowed-methods>
        <method>GET</method>
        <method>POST</method>
        <method>PUT</method>
        <method>DELETE</method>
        <method>HEAD</method>
        <method>OPTIONS</method>
        <method>PATCH</method>
        <method>TRACE</method>
    </allowed-methods>
    <allowed-headers>
        <header>*</header>
    </allowed-headers>
    <expose-headers>
        <header>*</header>
    </expose-headers>
</cors>
```

> [!NOTE]
> 
> Если применить политику CORS в области продукта вместо области API, а API использует проверку подлинности ключа подписки через заголовок, консоль не будет работать.
>
> Браузер автоматически выдает HTTP-запрос OPTIONS, который не содержит заголовок с ключом подписки. Из-за отсутствующего ключа подписки службе управления API не удается связать параметры вызова с продуктом, поэтому невозможно применить политику CORS.
>
> В качестве обходного решения можно передать ключ подписки в параметре запроса.

### <a name="what-permissions-do-i-need-to-edit-the-developer-portal"></a>Какие разрешения требуются для изменения портала разработчика?

Если вы видите ошибку `Oops. Something went wrong. Please try again later.` при открытии портала в административном режиме, возможно, отсутствуют необходимые разрешения (RBAC).

Устаревшие порталы требовали разрешения `Microsoft.ApiManagement/service/getssotoken/action` в области службы (`/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ApiManagement/service/<apim-service-name>`), чтобы разрешить администратору пользователей доступ к порталам. Для нового портала требуется разрешение `Microsoft.ApiManagement/service/users/token/action` в области `/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ApiManagement/service/<apim-service-name>/users/1`.

Чтобы создать роль с требуемым разрешением, можно использовать следующий сценарий PowerShell. Не забудьте изменить параметр `<subscription-id>`. 

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
 
После создания роли она может быть предоставлена любому пользователю из раздела **управления доступом (IAM)** в портал Azure. Назначение этой роли пользователю приведет к назначению разрешения в области службы. Пользователь сможет создавать маркеры SAS от имени *любого* пользователя в службе. Как минимум, эта роль должна быть назначена администратору службы. Следующая команда PowerShell демонстрирует назначение роли пользователю, `user1` в самой нижней области, чтобы избежать предоставления пользователю ненужных разрешений: 

```PowerShell
New-AzRoleAssignment -SignInName "user1@contoso.com" -RoleDefinitionName "APIM New Portal Admin" -Scope "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ApiManagement/service/<apim-service-name>/users/1" 
```

После предоставления разрешений пользователю пользователь должен выйти из портал Azure и снова войти в него, чтобы новые разрешения вступили в силу.

### <a name="im-seeing-the-unable-to-start-the-portal-see-if-settings-are-specified-correctly--error"></a>Я вижу ошибку `Unable to start the portal. See if settings are specified correctly (...)`

Эта ошибка возникает при сбое вызова `GET` для `https://<management-endpoint-hostname>/subscriptions/xxx/resourceGroups/xxx/providers/Microsoft.ApiManagement/service/xxx/contentTypes/document/contentItems/configuration?api-version=2018-06-01-preview`. Вызов выполняется из браузера через административный интерфейс портала.

Если служба управления API находится в виртуальной сети, см. описанный выше вопрос о подключении к виртуальной сети.

Сбой вызова также может быть вызван сертификатом SSL, который назначается пользовательскому домену и не является доверенным для браузера. Для устранения этой проблемы можно удалить пользовательскую конечную точку управления. Управление доменными API будет возвращаться к конечной точке по умолчанию с доверенным сертификатом.

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о новом портале разработчика:

- [Доступ и Настройка портала управляемого разработчика](api-management-howto-developer-portal-customize.md)
- [Настройка самостоятельно размещенной версии портала][2]

Обзор других ресурсов:

- [Репозиторий GitHub с исходным кодом][1]
- [Общедоступный план проекта][3]

[1]: https://aka.ms/apimdevportal
[2]: https://github.com/Azure/api-management-developer-portal/wiki
[3]: https://github.com/Azure/api-management-developer-portal/projects