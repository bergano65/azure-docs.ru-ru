---
title: Обновить с помощью Logic Apps для моделей Azure Analysis Services | Документация Майкрософт
description: Узнайте, как код асинхронное обновление с использованием Azure Logic Apps.
author: chrislound
manager: kfile
ms.service: analysis-services
ms.topic: conceptual
ms.date: 04/26/2019
ms.author: chlound
ms.openlocfilehash: 6ffce339fe7b1a434c8f007b417ee81a42529dfc
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66142449"
---
# <a name="refresh-with-logic-apps"></a>Обновление с помощью Logic Apps

С помощью Logic Apps и вызовы REST, выполнением операций обновления данных для табличных моделей Azure Analysis, включая синхронизацию реплик только для чтения для развертывания запросов.

Дополнительные сведения об использовании интерфейсов API REST с помощью служб Azure Analysis Services, см. в разделе [асинхронное обновление с помощью REST API](analysis-services-async-refresh.md).

## <a name="authentication"></a>Authentication

Все вызовы должны пройти проверку подлинности допустимый токен Azure Active Directory (OAuth 2).  В примерах в этой статье будут выполнять проверку подлинности в Azure Analysis Services (Субъекта-службы). Дополнительные сведения см. в разделе [создать субъект-службу с помощью портала Azure](../active-directory/develop/howto-create-service-principal-portal.md).

## <a name="design-the-logic-app"></a>Проектирование приложения логики

> [!IMPORTANT]
> В следующих примерах предполагается, что брандмауэр Azure Analysis Services отключена.  Если включен брандмауэр, общедоступный IP-адрес инициатора запроса необходимо внести в список разрешений брандмауэра службы Azure Analysis Services. Дополнительные сведения о диапазонах IP-адрес приложения логики на регион, см. в разделе [ограничения и сведения о конфигурации для Azure Logic Apps](../logic-apps/logic-apps-limits-and-config.md#firewall-configuration-ip-addresses).

### <a name="prerequisites"></a>Технические условия

#### <a name="create-a-service-principal-spn"></a>Создание субъекта-службы (SPN)

Дополнительные сведения о создании субъекта-службы, см. в разделе [создать субъект-службу с помощью портала Azure](../active-directory/develop/howto-create-service-principal-portal.md).

#### <a name="configure-permissions-in-azure-analysis-services"></a>Настройка разрешений в службах Azure Analysis Services
 
Создании субъекта-службы должна иметь разрешения администратора сервера на сервере. Дополнительные сведения см. в статье [Добавление субъекта-службы к роли администратора сервера](analysis-services-addservprinc-admins.md).

### <a name="configure-the-logic-app"></a>Настройка приложения логики

В этом примере приложение логики позволяет вызвать при получении HTTP-запроса. Это позволит использовать средства оркестрации, например фабрики данных Azure, чтобы активировать обновления модели Azure Analysis Services.

После создания приложения логики:

1. В конструкторе приложений логики, выбор первого действия в качестве **получено при HTTP-запроса**.

   ![Добавьте действие HTTP получен](./media/analysis-services-async-refresh-logic-app/1.png)

Этот шаг будет заполнить URL-адрес HTTP POST, после сохранения приложения логики.

2. Добавить новый шаг и выполните поиск **HTTP**.  

   ![Добавление действия HTTP](./media/analysis-services-async-refresh-logic-app/9.png)

   ![Добавление действия HTTP](./media/analysis-services-async-refresh-logic-app/10.png)

3. Выберите **HTTP** Чтобы добавить это действие.

   ![Добавление действия HTTP](./media/analysis-services-async-refresh-logic-app/2.png)

Настройка действия HTTP следующим образом:

|Свойство  |Значение  |
|---------|---------|
|**Метод**     |POST         |
|**URI**     | https://*ваш регион сервера*/servers/*консультанты имя_сервера*/models/*имя базы данных*/ <br /> <br /> Например: https:\//westus.asazure.windows.net/servers/myserver/models/AdventureWorks/|
|**Заголовки**     |   Content-Type, application/json <br /> <br />  ![Заголовки](./media/analysis-services-async-refresh-logic-app/6.png)    |
|**Текст**     |   Дополнительные сведения о формировании текста запроса, см. в разделе [асинхронное обновление с помощью API REST - POST для/refreshes](analysis-services-async-refresh.md#post-refreshes). |
|**Проверка подлинности**     |Active Directory OAuth         |
|**Клиент**     |Заполните свой идентификатор клиента Azure Active Directory         |
|**Аудитория**     |https://*.asazure.windows.net         |
|**Идентификатор клиента**     |Введите ваш ClientID имя субъекта-службы         |
|**Тип учетных данных**     |`Secret`         |
|**Секрет**     |Введите свой секрет имя субъекта-службы         |

Пример:

![Завершенного действия HTTP](./media/analysis-services-async-refresh-logic-app/7.png)

Теперь можно Проверьте логику приложения.  В конструкторе приложений логики, щелкните **запуска**.

![Тестирование приложения логики](./media/analysis-services-async-refresh-logic-app/8.png)

## <a name="consume-the-logic-app-with-azure-data-factory"></a>Использование приложения логики с помощью фабрики данных Azure

После сохранения приложения логики, просмотрите **получено при HTTP-запроса** действия, а затем скопировать **URL-адрес HTTP POST** , теперь создается.  Это URL-адрес, который может использоваться для асинхронного вызова для триггера приложения логики с помощью фабрики данных Azure.

Ниже приведен пример действия Web фабрики данных Azure, выполняющий это действие.

![Веб-действие фабрики данных](./media/analysis-services-async-refresh-logic-app/11.png)

## <a name="use-a-self-contained-logic-app"></a>Автономное приложение логики

Если вы не планируете с помощью средства Оркестрации, например фабрики данных для запуска обновления модели, можно разместить приложение логики, чтобы активировать обновление по расписанию.

Используя приведенный выше пример, удалить первое действие и замените ее строкой **расписание** действия.

![Действие расписания](./media/analysis-services-async-refresh-logic-app/12.png)

![Действие расписания](./media/analysis-services-async-refresh-logic-app/13.png)

В этом примере будет использоваться **повторения**.

После добавления действия, настройте интервал и частоту, а затем добавьте новый параметр и выберите **в эти часы**.

![Действие расписания](./media/analysis-services-async-refresh-logic-app/16.png)

Выберите желаемую часы.

![Действие расписания](./media/analysis-services-async-refresh-logic-app/15.png)

Сохраните приложение логики.

## <a name="next-steps"></a>Дальнейшие действия

[Примеры](analysis-services-samples.md)  
[REST API](https://docs.microsoft.com/rest/api/analysisservices/servers)
