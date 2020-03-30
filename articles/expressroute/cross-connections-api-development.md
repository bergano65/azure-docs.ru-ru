---
title: Разработка и интеграция API API Azure ExpressRoute CrossConnnections
description: В этой статье содержится подробный обзор для партнеров ExpressRoute о типе ресурса expressRouteCrossConnections.
services: expressroute
author: mialdrid
ms.service: expressroute
ms.topic: conceptual
ms.date: 02/06/2020
ms.author: mialdrid
ms.openlocfilehash: b4a83013d2cbeb2871a3963ec0c95144c02f4d66
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77187014"
---
# <a name="expressroute-crossconnnections-api-development-and-integration"></a>Разработка и интеграция API API ExpressRoute CrossConnnections

API-менеджер ресурсов партнеров ExpressRoute позволяет партнерам ExpressRoute управлять конфигурацией схем экспресс-маршрута с слоем-2 и слоем-3. Менеджер ресурсов партнеров ExpressRoute представляет новый тип ресурса, **expressRouteCrossConnections.** Партнеры используют этот ресурс для управления контурами ЭкспрессРута.

## <a name="workflow"></a>Рабочий процесс

Ресурс ExpressRouteCrossConnections является теневым ресурсом для схемы ExpressRoute. Когда клиент Azure создает схему ExpressRoute и выбирает конкретного партнера ExpressRoute, корпорация Майкрософт создает ресурс expressRouteCrossConnections в подписке на управление Azure ExpressRoute. При этом корпорация Майкрософт определяет группу ресурсов для создания ресурса expressRouteCrossConnections. Стандартом именования для группы ресурсов является *«CrossConnection-* PeeringLocation»; где PeeringLocation - Расположение ExpressRoute. Например, если клиент создает схему ExpressRoute в Денвере, CrossConnection будет создан в подписке на Azure партнера в следующей группе ресурсов: **CrossConnnection-Denver.**

Партнеры ExpressRoute управляют конфигурацией уровня-2 и уровня-3, выпуская операции REST на ресурсе expressRouteCrossConnections.

## <a name="benefits"></a>Преимущества

Преимущества перехода на ресурс expressRouteCrossConnections:

* Любые будущие усовершенствования для партнеров ExpressRoute будут доступны на ресурсе ExpressRouteCrossConnection.

* Партнеры могут применять [элементы управления доступом на основе ролей](https://docs.microsoft.com/azure/role-based-access-control/overview) к ресурсу expressRouteCrossConnection. Эти элементы управления могут определить разрешения, для которых учетные записи пользователей могут изменять ресурс expressRouteCrossConnection и добавлять/обновлять/удалять конфигурации пиринга.

* Ресурс ExpressRouteCrossConnection предоставляет AIS, которые могут быть полезны при устранении неполадок подключений ExpressRoute. Это включает таблицу ARP, резюме таблицы BGP Route И таблице BGP и детали таблицы BGP Route Table. Эта возможность не поддерживается классическими ABY развертывания.

* Партнеры также могут найти рекламируемые сообщества в пиринге Майкрософт с помощью ресурса *RouteFilter.*

## <a name="api-development-and-integration-steps"></a>Шаги развития ИПИ и интеграции

Чтобы развиваться в отношении API-партнера партнера по partner, партнеры ExpressRoute используют тестового клиента и установку тестового партнера. Установка тестового клиента будет использоваться для создания схем ExpressRoute в местах тестирования пиринга, которые отображают на фиктивные устройства и порты. Настройка тестового партнера используется для управления схемами ExpressRoute, созданными в месте тестирования.

### <a name="1-enlist-subscriptions"></a>1. Заручитесь подпиской

Чтобы запросить тестовый партнер и настроить настройку клиента, зарегистрируйтесь в инженерном контакте ExpressRoute с двумя подписками Pay-As-You-Go На ваш инженерный контакт:
* **ExpressRoute_API_Dev_Provider_Sub:** Эта подписка будет использоваться для управления схемами ExpressRoute, созданными в тестовых местах для тестирования на фиктивных устройствах и портах.

* **ExpressRoute_API_Dev_Customer_Sub:** Эта подписка будет использоваться для создания схем ExpressRoute в тестовых местах пиринга, которые отображают на фиктивные устройства и порты.

Местоположения тестовых пирингов: фиктивные устройства и порты по умолчанию не подвергаются воздействию производственных клиентов. Для создания схем ExpressRoute, которые отображают на тестовую установку, необходимо создать флаг функции подписки.

### <a name="2-register-the-dev_provider-subscription-to-access-the-expressroutecrossconnections-api"></a>2. Зарегистрируйте подписку Dev_Provider для доступа к API expressRouteCrossConnections

Для доступа к API expressRouteCrossConnections партнерская подписка должна быть зарегистрирована в **провайдере ресурсов Microsoft.Network.** Выполните действия в [поставщиках ресурсов Azure и нанизим](/azure/azure-resource-manager/management/resource-providers-and-types#azure-portal) статью для завершения процесса регистрации.

### <a name="3-set-up-authentication-for-azure-resource-manager-rest-api-calls"></a>3. Настройка аутентификации для вызовов REST API-ам-центра ресурсного отдела ресурсов Azure

Большинство служб Azure требуют проверки подлинности кода клиента с помощью менеджера ресурсов, используя действительные учетные данные, до вызова AIS службы. Аутентификация координируется между различными субъектами Azure AD и предоставляет клиенту токен доступа в качестве доказательства проверки подлинности.

Процесс аутентификации включает в себя два основных шага:

1. [Регистрация клиента](https://docs.microsoft.com/rest/api/azure/#register-your-client-application-with-azure-ad).
2. [Создайте запрос доступа.](https://docs.microsoft.com/rest/api/azure/#create-the-request)

### <a name="4-provide-network-contributor-permission-to-the-client-application"></a>4. Предоставить автору сетевого приложения

После успешной настройки аутентификации необходимо предоставить пользователю сети доступ к клиенту- ит-приложению в соответствии с Dev_Provider_Sub. Чтобы получить разрешение, войдите на [портал Azure](https://ms.portal.azure.com/#home) и выполните следующие действия:

1. Перейдите к подпискам и выберите Dev_Provider_Sub
2. Перейдите к управлению доступом (IAM)
3. Добавление назначения ролей
4. Выберите роль сетевого вкладчика
5. Назначить доступ к пользователю, группе или службе Azure
6. Выберите клиентское приложение
7. Сохранение изменений

### <a name="5-develop"></a>5. Разработка

Развивайся против [API expressRouteCrossConnections.](https://docs.microsoft.com/rest/api/expressroute/expressroutecrossconnections)

## <a name="rest-api"></a>REST API

Смотрите [API ExpressRoute CrossConnections REST](https://docs.microsoft.com/rest/api/expressroute/expressroutecrossconnections) для документации REST API.

## <a name="next-steps"></a>Дальнейшие действия

Для получения дополнительной информации обо всех AA- инотах [ExpressRoute](https://docs.microsoft.com/rest/api/expressroute/)REST см.