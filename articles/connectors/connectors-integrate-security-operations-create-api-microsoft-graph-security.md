---
title: Интеграция и управление операциями безопасности & Microsoft Graph безопасность
description: Улучшите защиту от угроз, обнаружение и реагирование на приложения с помощью Microsoft Graph & безопасности Azure Logic Apps
services: logic-apps
ms.suite: integration
author: preetikr
ms.author: preetikr
ms.reviewer: klam, estfan, logicappspm
ms.topic: article
ms.date: 01/30/2019
tags: connectors
ms.openlocfilehash: 7e9cc2d8d38af7e5e6cf26ccc3659ee58ef17e59
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/03/2019
ms.locfileid: "74789058"
---
# <a name="improve-threat-protection-by-integrating-security-operations-with-microsoft-graph-security--azure-logic-apps"></a>Улучшение защиты от угроз путем интеграции операций обеспечения безопасности с Microsoft Graph Security — Azure Logic Apps

С помощью [Azure Logic Apps](../logic-apps/logic-apps-overview.md) и соединителя [Microsoft Graph Security](https://docs.microsoft.com/graph/security-concept-overview) можно улучшить эффективность обнаружения угроз вашего приложения, защиты от них и реагирования на них, создав автоматизированные рабочие процессы для интеграции продуктов безопасности, служб и партнеров корпорации Майкрософт. Например, вы можете создавать [сборники схем Центра безопасности Azure](../security-center/security-center-playbooks.md), которые отслеживают и управляют объектами Microsoft Graph Security, такими как оповещения. Ниже приведены некоторые сценарии, поддерживаемые соединителем Microsoft Graph Security.

* Получение оповещений на основе запросов или по идентификатору оповещения. Например, можно получить список с оповещениями высокой степени серьезности.
* Обновление оповещений. Например, можно обновить назначения оповещений, добавить комментарии к оповещениям или пометить оповещения с помощью тегов.
* Мониторинг создания или изменения оповещений путем создания [подписок на оповещения (веб-перехватчики)](https://docs.microsoft.com/graph/api/resources/webhooks).
* Управление подписками на оповещения. Например, можно получить активные подписки, продлить их срок действия или удалить.

Рабочий процесс приложения логики может использовать действия, которые получают ответы от соединителя Microsoft Graph Security и делают эти выходные данные доступными для других действий в рабочем процессе. Вы также можете применять другие действия в рабочем процессе, используя выходные данные из действий соединителя Microsoft Graph Security. Например, при получении предупреждений с высоким уровнем серьезности с помощью соединителя Microsoft Graph Security их можно отправить в сообщении электронной почты с использованием соединителя Outlook. 

Дополнительные сведения о безопасности Microsoft Graph см. в статье [Обзор API безопасности Microsoft Graph](https://aka.ms/graphsecuritydocs). Если вы не знакомы с приложениями логики, ознакомьтесь со статьей [Что такое Azure Logic Apps](../logic-apps/logic-apps-overview.md). Дополнительные сведения о Microsoft Flow или PowerApps см. на странице [Microsoft Flow](https://flow.microsoft.com/) или [PowerApps](https://powerapps.microsoft.com/).

## <a name="prerequisites"></a>Технические условия

* Подписка Azure. Если у вас еще нет подписки Azure, [зарегистрируйтесь для получения бесплатной учетной записи Azure](https://azure.microsoft.com/free/). 

* Чтобы использовать соединитель Microsoft Graph Security, необходимо *явно предоставить* согласие администратора клиента Azure Active Directory (AD), которое является частью [требований проверки подлинности Microsoft Graph Security](https://aka.ms/graphsecurityauth). Для этого согласия требуется идентификатор и имя приложения соединителя Microsoft Graph Security, которые также можно найти на портале [Azure](https://portal.azure.com).

   | Свойство | Value |
   |----------|-------|
   | **Имя приложения** | `MicrosoftGraphSecurityConnector` |
   | **Идентификатор приложения** | `c4829704-0edc-4c3d-a347-7c4a67586f3c` |
   |||

   Чтобы предоставить согласие для соединителя, администратор клиента Azure AD может выполнить следующие действия:

   * [Предоставить согласие администратора клиента для приложений Azure AD](../active-directory/develop/v2-permissions-and-consent.md).

   * Во время первого запуска приложения логики приложение может запросить согласие у администратора клиента Azure AD с помощью [интерфейса согласия на применение](../active-directory/develop/application-consent-experience.md).
   
* Базовые знания [создания приложений логики](../logic-apps/quickstart-create-first-logic-app-workflow.md).

* Приложение логики, в котором требуется получить доступ к объектам Microsoft Graph Security, например оповещениям. В настоящее время у этого соединителя нет триггеров. Чтобы использовать действие Microsoft Graph Security, запустите приложение логики с триггером, например **Периодичность**.

## <a name="connect-to-microsoft-graph-security"></a>Подключение к Microsoft Graph Security 

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Войдите на [портал Azure](https://portal.azure.com/) и откройте свое приложение логики в конструкторе приложений логики, если оно еще не открыто.

1. Для пустых приложений логики добавьте триггер и любые другие действия, прежде чем добавить действие Microsoft Graph Security.

   -или-

   В имеющихся приложениях логики на последнем шаге, на котором необходимо добавить действие Microsoft Graph Security, щелкните **Новый шаг**.

   -или-

   Чтобы добавить действие между шагами, переместите указатель на стрелку между шагами. 
   Щелкните появившийся знак плюс (+), затем выберите **Добавить действие**.

1. В поле поиска введите "microsoft graph security" в качестве фильтра. В списке действий выберите любое необходимое действие.

1. Войдите, используя учетные данные безопасности Microsoft Graph Security.

1. Укажите необходимые сведения для выбранного действия и продолжайте создание рабочего процесса приложения логики.

## <a name="add-actions"></a>Добавление действий

Ниже приведены более подробные сведения об использовании различных действий, доступных с помощью соединителя Microsoft Graph Security.

### <a name="manage-alerts"></a>Управление оповещениями

Чтобы отфильтровать, отсортировать или получить последние результаты, предоставьте *только* параметры запроса [ODATA, поддерживаемые Microsoft Graph](https://docs.microsoft.com/graph/query-parameters). *Не указывайте* полный базовый URL-адрес или действие HTTP, например, `https://graph.microsoft.com/v1.0/security/alerts`, операцию `GET` или `PATCH`. Ниже приведен конкретный пример, который показывает параметры для действия **Получение оповещений**, когда требуется получить список с оповещениями с высокой степенью серьезности.

`Filter alerts value as Severity eq 'high'`

Дополнительные сведения о запросах, которые можно использовать с этим соединителем, см. в справочной документации по [оповещениям Microsoft Graph Security](https://docs.microsoft.com/graph/api/alert-list). Чтобы улучшить взаимодействие с этим соединителем, узнайте больше об [оповещениях свойств схемы](https://docs.microsoft.com/graph/api/resources/alert), поддерживаемых соединителем.

| Действия | Описание |
|--------|-------------|
| **Получение оповещений** | Фильтрация оповещений на основе одного или нескольких свойств [оповещений](https://docs.microsoft.com/graph/api/resources/alert), например: <p>`Provider eq 'Azure Security Center' or 'Palo Alto Networks'` | 
| **Получение оповещения по идентификатору** | Получение определенного оповещения на основе идентификатора. | 
| **Обновление оповещения** | Обновление определенного оповещения на основе его идентификатора. <p>Чтобы убедиться, что вы передаете требуемые и редактируемые свойства в запросе, см. [эту статью](https://docs.microsoft.com/graph/api/alert-update). Например, чтобы назначить оповещение аналитику по системам безопасности для проведения расследования, можно обновить свойство оповещения **Кому назначено**. |
|||

### <a name="manage-alert-subscriptions"></a>Управление подписками на оповещения

Microsoft Graph поддерживает [*подписки*](https://docs.microsoft.com/graph/api/resources/subscription) или [*веб-перехватчики*](https://docs.microsoft.com/graph/api/resources/webhooks). Чтобы получить, обновить или удалить подписки, введите параметры запроса [ODATA, поддерживаемые Microsoft Graph](https://docs.microsoft.com/graph/query-parameters), в конструкцию сущности Microsoft Graph и включите в нее `security/alerts`, а затем — запрос ODATA. 
*Не следует включать* базовый URL-адрес, например `https://graph.microsoft.com/v1.0`. Вместо этого используйте формат из данного примера:

`security/alerts?$filter=status eq 'New'`

| Действия | Описание |
|--------|-------------|
| **Создание подписок** | [Создайте подписку](https://docs.microsoft.com/graph/api/subscription-post-subscriptions), уведомляющую о любых изменениях. В этой подписке можно отфильтровать определенные типы оповещений. Например, можно создать подписку, уведомляющую об оповещениях с высокой степенью серьезности. |
| **Получение активных подписок** | [Получает активные подписки](https://docs.microsoft.com/graph/api/subscription-list). | 
| **Обновление подписки** | [Обновляет подписку](https://docs.microsoft.com/graph/api/subscription-update), предоставив ее идентификатор. Например, чтобы продлить подписку, можно обновить свойство подписки `expirationDateTime`. | 
| **Удаление подписки** | [Удаляет подписку](https://docs.microsoft.com/graph/api/subscription-delete), предоставив ее идентификатор. | 
||| 

## <a name="connector-reference"></a>Справочник по соединителям

Дополнительные технические сведения о триггерах, действиях и ограничениях, которые приведены в описании OpenAPI соединителя (прежнее название — Swagger), можно найти на [странице справки](https://aka.ms/graphsecurityconnectorreference) соединителя.

## <a name="get-support"></a>Получение поддержки

Если у вас возникли вопросы, то посетите [форум Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
Отправить идею по поводу возможности или проголосовать за нее вы можете на [сайте отзывов пользователей Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Дальнейшие действия

См. дополнительные сведения о других [соединителях Logic Apps](../connectors/apis-list.md).
