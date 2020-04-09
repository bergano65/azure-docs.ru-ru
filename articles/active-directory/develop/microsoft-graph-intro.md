---
title: API Microsoft Graph
description: Microsoft Graph API — это RESTful web API, позволяющий получить доступ к ресурсам облачных служб Майкрософт.
author: davidmu1
services: active-directory
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 02/13/2020
ms.author: davidmu
ms.custom: aaddev
ms.openlocfilehash: 67dbf696903e7a930d75762deb00ad58ed1a4f69
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80886472"
---
# <a name="microsoft-graph-api"></a>API Microsoft Graph

Microsoft Graph API — это RESTful web API, позволяющий получить доступ к ресурсам облачных служб Майкрософт. После регистрации приложения и получения токенов аутентификации для пользователя или службы можно сделать запросы в API Microsoft Graph. Для получения дополнительной [информации](https://docs.microsoft.com/graph/overview)смотрите обзор графика Microsoft Graph .

Microsoft Graph предоставляет REST AIS и клиентские библиотеки для доступа к данным следующих служб Microsoft 365:
- Службы Office 365: Delve, Excel, Microsoft Bookings, Microsoft Teams, OneDrive, OneNote, Outlook/Exchange, Planner и SharePoint
- Службы мобильности и безопасности предприятий: расширенная аналитика угроз, расширенная защита угроз, активный каталог Azure, менеджер идентификации и Intune
- Службы Windows 10: действия, устройства, уведомления
- Dynamics 365 Business Central

## <a name="versions"></a>Версии

Microsoft Graph в настоящее время поддерживает две версии: v1.0 и бета-версию. Версия v1.0 включает в себя общедоступные AA. Используйте версию v1.0 для всех производственных приложений. Бета-версия включает В пкей, которые в настоящее время находятся в предварительном просмотре. Поскольку мы можем внести изменения в наши бета-аПО, мы рекомендуем использовать бета-версию только для тестирования приложений, наиболее напрягаемых; не используйте бета-аДИ в производственных приложениях. Для получения дополнительной информации [см. Версию, поддержку и нарушение политики изменений для Microsoft Graph.](https://docs.microsoft.com/graph/versioning-and-support)

Для начала использования бета-аПО [см.](https://docs.microsoft.com/graph/api/overview?view=graph-rest-beta)

Чтобы начать использовать API v1.0, см. [ссылку Microsoft Graph REST API v1.0](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)

## <a name="get-started"></a>Начало работы

Чтобы прочитать или написать на ресурс, такой как пользователь или сообщение электронной почты, вы создайте запрос, который выглядит следующим образом:

`{HTTP method} https://graph.microsoft.com/{version}/{resource}?{query-parameters}`

Для получения дополнительной информации об элементах построенного запроса [см.](https://docs.microsoft.com/graph/use-the-api)

Для того, чтобы получить доступ к мощности API Microsoft Graph, доступны образцы быстрого запуска. Доступные образцы имеют доступ к двум службам с одной аутентификацией: учетная запись Майкрософт и Outlook. Каждый быстрый запуск получает доступ к информации из профилей пользователей учетной записи Майкрософт и отображает события из их календаря.
Быстрые старты включают четыре шага:
- Выберите платформу
- Получите идентификатор приложения (идентификатор клиента)
- Сборка примера
- Войти в систему и просмотреть события в календаре

Когда вы завершаете quickstart, у вас есть приложение, которое готово к запуску. Для получения дополнительной информации, [см.](https://docs.microsoft.com/graph/quick-start-faq) Чтобы начать работу с образцами, [см.](https://developer.microsoft.com/graph/quick-start)

## <a name="tools"></a>Инструменты

Microsoft Graph Explorer — это веб-инструмент, который можно использовать для создания и тестирования запросов с использованием AA-иносов Microsoft Graph. Вы можете получить доступ `https://developer.microsoft.com/graph/graph-explorer`к Microsoft Graph Explorer по адресу: .

Почтальон — это инструмент, который можно также использовать для создания и тестирования запросов с помощью AA-аПО Microsoft Graph. Вы можете скачать `https://www.getpostman.com/`Почтальон по адресу: . Для взаимодействия с Microsoft Graph в Postman, вы используете коллекцию Microsoft Graph в Postman. Для получения дополнительной информации [см.](/graph/use-postman?context=graph%2Fapi%2Fbeta&view=graph-rest-beta)
