---
title: Подключение к Salesforce из Azure Logic Apps | Документация Майкрософт
description: Автоматизация задач и рабочих процессов, которые отслеживают и создают записи и задания Salesforce, а также управляют ими с помощью Azure Logic Apps.
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.assetid: 54fe5af8-7d2a-4da8-94e7-15d029e029bf
ms.topic: article
tags: connectors
ms.date: 08/24/2018
ms.openlocfilehash: 292d517f2c99974f4674a4c94472a0a320320ce4
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "62106021"
---
# <a name="monitor-create-and-manage-salesforce-resources-by-using-azure-logic-apps"></a>Мониторинг и создание ресурсов Salesforce, а также управление ими с помощью Azure Logic Apps

С помощью Azure Logic Apps и соединителя Salesforce можно создавать автоматизированные задачи и рабочие процессы для ресурсов Salesforce, таких как записи, задания и объекты. К примерам задач и рабочих процессов относятся:

* мониторинг создания или изменения записей; 
* создание, получение заданий и записей и управление ими, включая действия вставки, обновления и удаления.

Вы можете использовать триггеры Salesforce, которые получают ответы из Salesforce и делают выходные данные доступными для других действий. Вы можете использовать действия в своих приложениях логики для выполнения задач с ресурсами Salesforce. Если вы не знакомы с приложениями логики, ознакомьтесь со статьей [Что такое Azure Logic Apps](../logic-apps/logic-apps-overview.md).

## <a name="prerequisites"></a>Технические условия

* Подписка Azure. Если у вас еще нет подписки Azure, <a href="https://azure.microsoft.com/free/" target="_blank">зарегистрируйтесь для получения бесплатной учетной записи Azure</a>. 

* [Учетная запись Salesforce](https://salesforce.com/).

* Базовые знания [создания приложений логики](../logic-apps/quickstart-create-first-logic-app-workflow.md).

* Приложение логики, из которого необходимо получить доступ к учетной записи Salesforce. Чтобы начать работу с триггером Salesforce, [создайте пустое приложение логики](../logic-apps/quickstart-create-first-logic-app-workflow.md). Чтобы использовать действие Salesforce, запустите приложение логики с другим триггером, например триггером **Периодичность**.

## <a name="connect-to-salesforce"></a>Подключение к Salesforce

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Войдите на [портал Azure](https://portal.azure.com) и откройте свое приложение логики в конструкторе приложений логики, если оно еще не открыто.

1. Выберите путь: 

   * Для пустых приложений логики в поле поиска в качестве фильтра введите salesforce. 
   В списке триггеров выберите нужный триггер. 

     -или-

   * В имеющихся приложениях логики на шаге, в котором необходимо добавить действие, выберите **Новый шаг**. В поле поиска введите фильтр salesforce. В списке действий выберите любое необходимое действие.

1. Если будет предложено войти в Salesforce, войдите и разрешите доступ.

   Ваши учетные данные авторизуют приложение логики, чтобы оно могло создать подключение к Salesforce и получить доступ к данным.

1. Укажите необходимые сведения для выбранного триггера или действия и продолжайте создание рабочего процесса приложения логики.

## <a name="connector-reference"></a>Справочник по соединителям

Дополнительные технические сведения о триггерах, действиях и ограничениях, которые приведены в описании OpenAPI соединителя (прежнее название — Swagger), можно найти на [странице справки](/connectors/salesforce/) соединителя.

## <a name="get-support"></a>Получение поддержки

* Если у вас возникли вопросы, то посетите [форум Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Отправить идею по поводу возможности или проголосовать за нее вы можете на [сайте отзывов пользователей Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Дальнейшие действия

* См. дополнительные сведения о других [соединителях Logic Apps](../connectors/apis-list.md).