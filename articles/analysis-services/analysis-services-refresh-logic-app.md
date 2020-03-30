---
title: Обновление с логикой приложений для моделей анализа Azure (ru) Документы Майкрософт
description: В этой статье описывается, как кодировать асинхронное обновление для служб анализа Azure с помощью приложений Azure Logic Apps.
author: chrislound
ms.service: analysis-services
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: chlound
ms.openlocfilehash: 78bc629598c0635b7760285d0507b7a85a4ab551
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127010"
---
# <a name="refresh-with-logic-apps"></a>Обновление с помощью Logic Apps

Используя Logic Apps и REST-звонки, можно выполнять автоматизированные операции обновления данных на табулярных моделях Azure Analysis, включая синхронизацию только ремиссий для масштабирования запросов.

Чтобы узнать больше об использовании REST API с помощью служб анализа Azure, [см.](analysis-services-async-refresh.md)

## <a name="authentication"></a>Проверка подлинности

Все вызовы должны быть проверены с помощью действительного маркера Active Directory Azure (OAuth 2).  Примеры в этой статье будут использовать директора службы (SPN) для проверки подлинности служб анализа Azure. Чтобы узнать больше, смотрите [Создайте основную службу с помощью портала Azure](../active-directory/develop/howto-create-service-principal-portal.md).

## <a name="design-the-logic-app"></a>Дизайн приложения логики

> [!IMPORTANT]
> Следующие примеры предполагают, что брандмауэр Azure Analysis Services отключен. Если брандмауэр включен, общедоступный IP-адрес инициатора запроса должен быть внесен в белый список в брандмауэре Azure Analysis Services. Чтобы узнать больше о диапазонах IP-приложений Azure Logic Apps в регионе, [см.](../logic-apps/logic-apps-limits-and-config.md#configuration)

### <a name="prerequisites"></a>Предварительные требования

#### <a name="create-a-service-principal-spn"></a>Создание директора службы (SPN)

Чтобы узнать о создании директора службы, [см.](../active-directory/develop/howto-create-service-principal-portal.md)

#### <a name="configure-permissions-in-azure-analysis-services"></a>Настройка разрешений в службах анализа Azure
 
Директор службы, который вы создаете, должен иметь разрешения администратора сервера на сервере. Дополнительные сведения см. в статье [Добавление субъекта-службы к роли администратора сервера](analysis-services-addservprinc-admins.md).

### <a name="configure-the-logic-app"></a>Настройка логики App

В этом примере logic App предназначен для запуска при получении запроса HTTP. Это позволит использовать инструмент оркестровки, такой как Azure Data Factory, для запуска обновления модели анализа Azure.

После того как вы создали Logic App:

1. В logic App дизайнер, выбрать первое действие, как **при получении запроса HTTP**.

   ![Добавление действия HTTP получено](./media/analysis-services-async-refresh-logic-app/1.png)

Этот шаг будет заполнить с HTTP POST URL после логики App сохраняется.

2. Добавьте новый шаг и ищите **HTTP.**  

   ![Добавление действия HTTP](./media/analysis-services-async-refresh-logic-app/9.png)

   ![Добавление действия HTTP](./media/analysis-services-async-refresh-logic-app/10.png)

3. Выберите **HTTP,** чтобы добавить это действие.

   ![Добавление действия HTTP](./media/analysis-services-async-refresh-logic-app/2.png)

Налаживание действия HTTP следующим образом:

|Свойство  |Значение  |
|---------|---------|
|**Метод**     |POST         |
|**URI**     | https://*области сервера*/серверы/*aas имя сервера*/модели/*имя базы данных*/обновляет <br /> <br /> Например: https:\//westus.asazure.windows.net/servers/myserver/models/AdventureWorks/refreshes|
|**Заголовки**     |   Тип контента, приложение/json <br /> <br />  ![Заголовки](./media/analysis-services-async-refresh-logic-app/6.png)    |
|**Текст**     |   Чтобы узнать больше о формировании тела запроса, [см. Асинхронное обновление с помощью REST API - POST /refreshes](analysis-services-async-refresh.md#post-refreshes). |
|**Проверка подлинности**     |Active Directory OAuth         |
|**Арендатор**     |Заполните свой Активный каталог Azure TenantId         |
|**Аудитории**     |https://.asazure.windows.net         |
|**Идентификатор клиента**     |Введите свой основной клиент-клиент         |
|**Тип учетных данных**     |Секрет         |
|**Секрет**     |Введите свой секрет имени службы         |

Пример

![Завершено действие HTTP](./media/analysis-services-async-refresh-logic-app/7.png)

Теперь проверьте Logic App.  В logic App дизайнер, нажмите **Run**.

![Тестирование приложения логики](./media/analysis-services-async-refresh-logic-app/8.png)

## <a name="consume-the-logic-app-with-azure-data-factory"></a>Потребляйте logic App с фабрикой данных Azure

После того, как logic App сохраняется, **просмотрите, когда запрос HTTP получен** деятельности, а затем скопировать HTTP POST **URL,** который в настоящее время генерируется.  Это URL, который может быть использован Azure Data Factory для асинхронного вызова для запуска Logic App.

Вот пример веб-активности Фабрики данных Azure Data Factory, которая выполняет это действие.

![Веб-активность фабрики данных](./media/analysis-services-async-refresh-logic-app/11.png)

## <a name="use-a-self-contained-logic-app"></a>Используйте автономное приложение Логика

Если вы не планируете использовать инструмент оркестровки, такой как Data Factory, для запуска обновления модели, можно настроить приложение логики для запуска обновления на основе расписания.

Используя приведенный выше пример, удалите первое действие и замените его действием **Расписание.**

![Расписание действий](./media/analysis-services-async-refresh-logic-app/12.png)

![Расписание действий](./media/analysis-services-async-refresh-logic-app/13.png)

В этом примере будет использоваться **recurrence**.

После добавления действия настройте Интервал и частоту, затем добавьте новый параметр и выберите **в эти часы.**

![Расписание действий](./media/analysis-services-async-refresh-logic-app/16.png)

Выберите нужные часы.

![Расписание действий](./media/analysis-services-async-refresh-logic-app/15.png)

Сохранить логику App.

## <a name="next-steps"></a>Дальнейшие действия

[Примеры](analysis-services-samples.md)  
[REST API](https://docs.microsoft.com/rest/api/analysisservices/servers)
