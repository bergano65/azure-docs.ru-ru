---
title: Использование API Graph Azure AD
description: API Graph Azure Active Directory (Azure AD) обеспечивает программный доступ к Azure AD через конечные точки REST API OData. Приложения могут использовать API Azure AD Graph для выполнения операций создания, чтения, обновления и удаления (CRUD) с данными и объектами каталогов.
services: active-directory
documentationcenter: n/a
author: rwike77
manager: CelesteDG
editor: ''
tags: ''
ms.assetid: 9dc268a9-32e8-402c-a43f-02b183c295c5
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/28/2019
ms.author: ryanwi
ms.reviewer: sureshja
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: 92a88b1e17812b9dc99fd1d5b391d95ba541f48a
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/26/2019
ms.locfileid: "74533068"
---
# <a name="how-to-use-the-azure-ad-graph-api"></a>Использование API Graph Azure AD

> [!IMPORTANT]
> Для доступа к ресурсам Azure Active Directory (Azure AD) настоятельно рекомендуется использовать [Microsoft Graph](https://developer.microsoft.com/graph) , а не API Graph Azure AD. В настоящее время усилия наших разработчиков направлены на Microsoft Graph, и дальнейшие усовершенствования API Azure AD Graph не планируются. Существует ограниченное количество сценариев, в которых API Graph Azure AD все еще может быть подходящими. Дополнительные сведения см. в блоге [Microsoft Graph или Azure AD Graph](https://dev.office.com/blogs/microsoft-graph-or-azure-ad-graph) , а также [в статье Перенос приложений Azure ad Graph в Microsoft Graph](https://docs.microsoft.com/graph/migrate-azure-ad-graph-overview).

Azure AD API Graph предоставляет программный доступ к Azure AD через REST API конечные точки OData. Приложения могут использовать API Azure AD Graph для выполнения операций создания, чтения, обновления и удаления (CRUD) с данными и объектами каталогов. Например, можно использовать API Azure AD Graph для создания нового пользователя, просмотра или обновления свойств пользователя, изменения пароля пользователя, проверки членства в группе для доступа на основе ролей, а также отключения или удаления пользователя. Дополнительные сведения о функциях и сценариях приложений Azure AD API Graph см. в статье [API Graph Azure AD](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog) и [Предварительные требования для API Graph Azure AD](https://msdn.microsoft.com/library/hh974476.aspx). API Graph Azure AD работает только с рабочими или учебными учетными записями или организациями.

В этой статье рассматривается API Graph Azure AD. Аналогичные сведения, связанные с API Microsoft Graph, см. в статье [Использование API Microsoft Graph](https://developer.microsoft.com/graph/docs/concepts/use_the_api).

## <a name="how-to-construct-a-graph-api-url"></a>Как составить URL-адрес Graph API

В API Graph для доступа к данным и объектам каталога (другими словами, к ресурсам и сущностям), с которыми требуется выполнять операции CRUD, можно использовать URL-адреса на основе протокола OData. URL-адреса, используемые в API Graph, состоят из четырех основных частей — корня службы, идентификатора клиента, пути к ресурсу и строки параметров запроса: `https://graph.windows.net/{tenant-identifier}/{resource-path}?[query-parameters]`. Возьмем, к примеру, следующий URL-адрес: `https://graph.windows.net/contoso.com/groups?api-version=1.6`.

* **Корень службы**. В API Azure AD Graph корнем службы всегда является https://graph.windows.net.
* **Идентификатор клиента**. Этот раздел может содержать проверенное (зарегистрированное) доменное имя; в примере выше — это contoso.com. Это также может быть идентификатор объекта клиента или псевдоним myorganization или me. Дополнительные сведения см. [в статье адресация сущностей и операций в Azure AD API Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-operations-overview).
* **Путь к ресурсу**. Этот раздел URL-адреса определяет ресурс, с которым будет взаимодействовать (пользователи, группы, конкретный пользователь или конкретная группа и т. д.). В приведенном выше примере это группы верхнего уровня для адресации этого набора ресурсов. Можно также обращаться к конкретной сущности, например users/{objectId} или users/userPrincipalName.
* **Параметры запроса**. Знак вопроса (?) отделяет раздел пути ресурса от раздела параметров запроса. Параметр запроса api-version необходим для всех запросов в API Azure AD Graph. API Azure AD Graph также поддерживает следующие параметры запроса OData: **$filter**, **$orderby**, **$expand**, **$top** и **$format**. В настоящее время не поддерживаются следующие параметры запроса: **$count**, **$inlinecount** и **$skip**. Дополнительные сведения см. в статье [Поддерживаемые запросы, фильтры и операции разбиения на страницы в Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-supported-queries-filters-and-paging-options).

## <a name="graph-api-versions"></a>Версии API Graph

Версия для запроса API Graph указывается в параметре запроса api-version. Для версии 1.5 и более поздних используется числовое значение версии: api-version=1.6. Для предыдущих версий используйте строку даты, соответствующую формату ГГГГ-ММ-ДД. Пример: api-version=2013-11-08. Для компонентов предварительной версии используйте строку beta. Пример: api-version=beta. Дополнительные сведения о различиях между версиями API Graph см. в статье [Управление версиями API Graph Azure AD](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-versioning).

## <a name="graph-api-metadata"></a>Метаданные API Graph

Чтобы получить файл метаданных API Azure AD Graph, добавьте сегмент $metadata после идентификатора клиента в URL-адресе. Например, следующий URL-адрес возвращает метаданные для демонстрационной компании: `https://graph.windows.net/GraphDir1.OnMicrosoft.com/$metadata?api-version=1.6`. Можно ввести этот URL-адрес в адресной строке веб-браузера для просмотра метаданных. Возвращаемый документ метаданных с расширением CSDL описывает сущности и сложные типы, их свойства, а также функции и действия, представляемые запрошенной версией API Graph. Если параметр api-version не задан, то возвращаются метаданные для самой последней версии.

## <a name="common-queries"></a>Стандартные запросы

В [общих запросах Azure ad API Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-supported-queries-filters-and-paging-options#CommonQueries) перечислены общие запросы, которые можно использовать с Azure AD Graph, включая запросы, которые можно использовать для доступа к ресурсам верхнего уровня в каталоге и запросы для выполнения операций в каталоге.

Например, `https://graph.windows.net/contoso.com/tenantDetails?api-version=1.6` возвращает сведения об организации для каталога contoso.com.

Или `https://graph.windows.net/contoso.com/users?api-version=1.6` перечисляет все объекты пользователя в каталоге contoso.com.

## <a name="using-the-azure-ad-graph-explorer"></a>Использование Azure AD Graph Explorer
Можно использовать Azure AD Graph Explorer для API Azure AD Graph, чтобы запрашивать данные каталога по мере создания приложения.

Ниже показан снимок экрана результата, который отобразится, если войти в Azure AD Graph Explorer и ввести `https://graph.windows.net/GraphDir1.OnMicrosoft.com/users?api-version=1.6`, чтобы отобразить всех пользователей в каталоге пользователя, выполнившего вход.

![Пример выходных данных в обозревателе API Graph Azure AD](./media/active-directory-graph-api-quickstart/graph_explorer.png)

**Скачайте Azure AD Graph Explorer**. Чтобы скачать этот инструмент, перейдите по адресу: [https://graphexplorer.azurewebsites.net/](https://graphexplorer.azurewebsites.net/). Выполните **вход** с использованием данных учетной записи Azure AD, чтобы запустить Azure AD Graph Explorer для работы с клиентом. При запуске Azure AD Graph Explorer для своего собственного клиента вам или вашему администратору потребуется предоставить согласие в процессе входа. Если у вас есть подписка Office 365, у вас также есть клиент Azure AD. Учетные данные, используемые для входа в Office 365, в действительности являются учетными записями Azure AD, и эти учетные данные можно использовать в Azure AD Graph Explorer.

**Выполнение запроса**. Для выполнения запроса введите запрос в текстовом поле запроса и нажмите кнопку **ПОЛУЧИТЬ** или клавишу **ВВОД**. Результаты появятся в поле ответа. Например, с помощью `https://graph.windows.net/myorganization/groups?api-version=1.6` можно получить список всех объектов групп в каталоге пользователя, выполнившего вход.

Обратите внимание на следующие возможности и ограничения Azure AD Graph Explorer.

* Возможность автозаполнения в наборах ресурсов. Чтобы воспользоваться этой функцией, щелкните в текстовом поле запроса (где отображается URL-адрес компании). В раскрывающемся списке можно выбрать набор ресурсов.
* Журнал запросов.
* Поддерживает псевдонимы адресации me и myorganization. Например, можно использовать `https://graph.windows.net/me?api-version=1.6` для возврата объекта пользователя, выполнившего вход, или `https://graph.windows.net/myorganization/users?api-version=1.6` для возврата всех пользователей в каталоге выполнившего вход пользователя.
* Поддерживает полный набор операций CRUD для собственного каталога с помощью `POST`, `GET`, `PATCH` и `DELETE`.
* Раздел заголовков ответа. Этот раздел можно использовать для устранения проблем, возникающих при выполнении запросов.
* Средство просмотра JSON для ответа с возможностью развертывания и свертывания.
* Отображение или передача эскизов фотографий не поддерживается.

## <a name="using-fiddler-to-write-to-the-directory"></a>Использование Fiddler для записи в каталог

При работе с этим кратким руководством можно использовать веб-отладчик Fiddler, чтобы попрактиковаться в выполнении операций записи в каталоге Azure AD. Например, можно получить и передать фотографию профиля пользователя (что не поддерживается в Azure AD Graph Explorer). Дополнительные сведения о Fiddler и его установке см. по адресу [https://www.telerik.com/fiddler](https://www.telerik.com/fiddler).

В следующем примере веб-отладчик Fiddler используется для создания новой группы безопасности MyTestGroup в каталоге Azure AD.

**Получение маркера доступа**. Чтобы получить доступ к Graph Azure AD, клиенты сначала должны успешно пройти проверку подлинности в Azure AD. Дополнительные сведения см. в статье [сценарии проверки подлинности в Azure AD](v1-authentication-scenarios.md).

**Составление и выполнение запроса**. Выполните следующие шаги:

1. Откройте веб-отладчик Fiddler и перейдите на вкладку **Composer** (Средство создания).
2. Так как требуется создать новую группу безопасности, выберите в раскрывающемся меню вариант **Post** в качестве метода HTTP. Дополнительные сведения об операциях и разрешениях на объекте группы см. в разделе [Группа](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#group-entity) в [справочнике по Azure AD Graph REST API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog).
3. В поле рядом с методом **Post** введите следующий URL-адрес запроса: `https://graph.windows.net/{mytenantdomain}/groups?api-version=1.6`.
   
   > [!NOTE]
   > Необходимо заменить {mytenantdomain} доменным именем собственного каталога Azure AD.

4. В поле непосредственно под методом Post введите приведенный ниже заголовок HTTP.
   
    ```
   Host: graph.windows.net
   Authorization: Bearer <your access token>
   Content-Type: application/json
   ```
   
   > [!NOTE]
   > Замените &lt;your access token&gt; маркером доступа для своего каталога Azure AD.

5. В поле **Request body** (Текст запроса) введите следующий код JSON.
   
    ```
        {
            "displayName":"MyTestGroup",
            "mailNickname":"MyTestGroup",
            "mailEnabled":"false",
            "securityEnabled": true
        }
   ```
   
    Дополнительные сведения о создании групп см. в разделе [Создание группы](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/groups-operations#CreateGroup).

Дополнительные сведения о сущностях и типах Azure AD, предоставляемых графом, и сведения об операциях, которые можно выполнять с ними с помощью Graph, см. в [справочнике по Azure AD graph REST API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog).

## <a name="next-steps"></a>Дальнейшие действия

* Узнайте больше о [API Graph Azure AD](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog)
* Дополнительные сведения об [областях разрешений API Graph Azure AD](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-permission-scopes)
