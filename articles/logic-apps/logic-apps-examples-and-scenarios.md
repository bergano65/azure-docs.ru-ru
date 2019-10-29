---
title: Примеры & распространенных сценариев Azure Logic Apps
description: Примеры, распространенные сценарии, учебники и пошаговые руководства для Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: conceptual
ms.date: 07/31/2019
ms.openlocfilehash: 9e245d1a0054d9de5b8b524416c8c92eb02353a7
ms.sourcegitcommit: d47a30e54c5c9e65255f7ef3f7194a07931c27df
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/29/2019
ms.locfileid: "73025522"
---
# <a name="common-scenarios-examples-tutorials-and-walkthroughs-for-azure-logic-apps"></a>Распространенные сценарии, примеры, учебники и пошаговые руководства для Azure Logic Apps

[Azure Logic Apps](../logic-apps/logic-apps-overview.md) помогает управлять и интегрировать различные службы, предоставляя [сотни готовых соединителей](../connectors/apis-list.md), от локальных SQL Server или SAP до Azure Cognitive Services. Служба Logic Apps работает без серверов, поэтому вам не нужно заботиться об экземплярах и масштабировании. Достаточно определить рабочий процесс с триггером и действия для него. Базовая платформа обеспечивает масштабирование, доступность и производительность. Служба Logic Apps наиболее полезна в тех случаях, когда нужно координировать несколько действий в нескольких системах.

Ниже приведены распространенные примеры и сценарии, которые помогут узнать больше о многих шаблонах и возможностях, поддерживаемых Azure Logic Apps.

## <a name="popular-starting-points-for-logic-app-workflows"></a>Часто используемые отправные точки для рабочих процессов приложений логики

Каждое приложение логики запускается при помощи [*триггера*](../logic-apps/logic-apps-overview.md#logic-app-concepts) (это всегда только один триггер). Он инициирует рабочий процесс приложения логики и передает в него данные. Некоторые соединители предоставляют триггер одного из следующих типов:

* *Опрашивающий триггер* — регулярно проверяет конечную точку службы на наличие новых данных. Когда появляются новые данные, триггер создает и запускает новый экземпляр рабочего процесса, передавая эти данные в качестве входных.

* *Извещающий триггер* — прослушивает данные в конечной точке службы и ожидает определенного события. Когда происходит это событие, триггер немедленно срабатывает, то есть создает и запускает новый экземпляр рабочего процесса, передавая в качестве входных все доступные данные.

Ниже приведено несколько распространенных примеров триггеров:

* Опрашивающие триггеры:

  * [Триггер **повторения** ](../connectors/connectors-native-recurrence.md) позволяет задать дату и время начала, а также повторение для срабатывания приложения логики. Например, вы можете выбрать дни недели и время суток для запуска приложения логики. Дополнительные сведения см. в следующих статьях:

    * [Планирование и выполнение повторяющихся автоматизированных задач, процессов и рабочих процессов с помощью Azure Logic Apps](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md)
    * [Учебник. Проверка трафика по расписанию с помощью Azure Logic Apps](../logic-apps/tutorial-build-schedule-recurring-logic-app-workflow.md)

  * Триггер "При получении сообщения электронной почты" позволяет приложению логики проверять входящие сообщения электронной почты в любой почтовой службе, которую поддерживает служба Logic Apps, в том числе [Office 365 Outlook](../connectors/connectors-create-api-office365-outlook.md), [Gmail](https://docs.microsoft.com/connectors/gmail/), [Outlook.com](https://docs.microsoft.com/connectors/outlook/) и т. д. Дополнительные сведения см. в следующих статьях: 

    * [Учебник. Управление запросами списка рассылки с помощью Azure Logic Apps](../logic-apps/tutorial-process-mailing-list-subscriptions-workflow.md)
    * [Учебник. Автоматизация обработки сообщений электронной почты и вложений с помощью Azure Logic Apps](../logic-apps/tutorial-process-email-attachments-workflow.md)

  * [Триггер **HTTP**](../connectors/connectors-native-http.md) позволяет приложению логики проверять указанную конечную точку службы, обращаясь к ней по протоколу HTTP.
  
* Извещающие триггеры:

  * Триггер [ **запроса** ](../connectors/connectors-native-reqres.md) позволяет приложению логики принимать HTTP-запросы и реагировать в реальном времени на события определенным образом.

  * [Триггер **Веб-перехватчик HTTP**](../connectors/connectors-native-webhook.md) подписывается на конечную точку службы, регистрируя в ней свой *URL-адрес обратного вызова*. В этом сценарии служба сама уведомляет триггер о том, что произошло указанное событие, то есть триггер может не опрашивать службу.

Триггер срабатывает, когда получает уведомление о новых данных или определенном событии, создает новый экземпляр рабочего процесса для приложения логики и выполняет действия в этом рабочем процессе. Вам будут доступны все данные триггера на любом этапе рабочего процесса. Например, триггер может срабатывать при появлении нового твита и передавать содержимое этого твита в экземпляр приложения логики. Чтобы приступить к работе с Azure Logic Apps, воспользуйтесь следующими разделами краткого руководства:

* [Краткое руководство. Создание первого автоматизированного рабочего процесса с помощью Azure Logic Apps в портал Azure](../logic-apps/quickstart-create-first-logic-app-workflow.md)
* [Краткое руководство. Создание автоматических задач, процессов и рабочих процессов с помощью Azure Logic Apps в Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)
* [Краткое руководство. Создание автоматизированных рабочих процессов приложения логики и управление ими с помощью Visual Studio Code](../logic-apps/quickstart-create-logic-apps-visual-studio-code.md)

## <a name="respond-to-triggers-and-extend-actions"></a>Реагирование на триггеры и расширение действий

Для систем и служб, для которых соединители не опубликованы, можно также расширить приложения логики.

* [Создание настраиваемых триггеров или действий](../logic-apps/logic-apps-create-api-app.md)
* [Настройка длительных действий для запусков рабочего процесса](../logic-apps/logic-apps-create-api-app.md)
* [Реагирование на внешние события и действия с помощью объектов webhook](../logic-apps/logic-apps-create-api-app.md)
* [Вызов, активация или вложение рабочих процессов с использованием синхронных ответов на HTTP-запросы](../logic-apps/logic-apps-http-endpoint.md)
* [Руководство. Создание панели мониторинга социальных сетей на базе ИИ за несколько минут с помощью Logic Apps и Power BI](https://aka.ms/logicappsdemo)
* [Видео по реагированию на сообщения веб-перехватчиков Twilio SMS и отправка текстового ответа](https://channel9.msdn.com/Blogs/Windows-Azure/Azure-Logic-Apps-Walkthrough-Webhook-Functions-and-an-SMS-Bot)

## <a name="control-flow-error-handling-and-logging-capabilities"></a>Возможности для потока управления, обработки ошибок и ведения журнала

Служба Logic Apps предоставляет широкие возможности для расширенного потока управления, в том числе условия, выключатели, циклы и области. Чтобы обеспечить устойчивость решений, в рабочих процессах можно также реализовать обработку ошибок и исключений. Для ведения журналов уведомлений и диагностики состояния рабочего процесса служба Azure Logic Apps обеспечивает средства мониторинга и уведомления.

* Выполнение различных действий на основе [условных операторов](../logic-apps/logic-apps-control-flow-conditional-statement.md) и [операторов switch](../logic-apps/logic-apps-control-flow-switch-statement.md).
* [Повторяющиеся действия или обработка элементов в массивах и коллекциях с помощью циклов](../logic-apps/logic-apps-control-flow-loops.md).
* [Группировка действий с использованием областей](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md).
* [Реализация обработки ошибок и исключений в рабочем процессе](../logic-apps/logic-apps-exception-handling.md)
* [Вариант использования. Как компания в сфере здравоохранения использует обработку исключений в приложении логики для рабочих процессов HL7 FHIR](../logic-apps/logic-apps-scenario-error-and-exception-handling.md)
* [Включение мониторинга, ведения журнала и оповещений для существующих приложений логики](../logic-apps/logic-apps-monitor-your-logic-apps.md)
* [Включение мониторинга и ведения журнала диагностики при создании приложений логики](../logic-apps/logic-apps-monitor-your-logic-apps-oms.md)

## <a name="deploy-and-manage-logic-apps"></a>Развертывание приложений логики и управление ими

Можно полностью разработать и развернуть приложения логики с помощью Visual Studio, Azure DevOps или любых других инструментов системы управления версиями и автоматической сборки. Для поддержки развертывания рабочих процессов и зависимых подключений в шаблоне ресурсов приложения логики используют шаблоны развертывания ресурсов Azure. Средства Visual Studio автоматически создают эти шаблоны, которые можно записать после изменения в систему управления версиями для управления версиями.

* [Создание и развертывание приложений логики в Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)
* [Включение мониторинга, ведения журнала и оповещений для существующих приложений логики](../logic-apps/logic-apps-monitor-your-logic-apps.md)
* [Автоматизация развертывания приложений логики](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)
* [Пример. подключение к очередям служебной шины Azure из Azure Logic Apps и развертывание с помощью Azure Pipelines в Azure DevOps](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-azure-service-bus-queues-from-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Пример. подключение к учетным записям хранения Azure из Azure Logic Apps и развертывание с помощью Azure Pipelines в Azure DevOps](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-azure-storage-accounts-from-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Пример. Настройка действия приложения-функции для Azure Logic Apps и развертывание с помощью Azure Pipelines в Azure DevOps](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/set-up-an-azure-function-app-action-for-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Пример. подключение к учетной записи интеграции из Azure Logic Apps и развертывание с помощью Azure Pipelines в Azure DevOps](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-an-integration-account-from-azure-logic-apps-and-deploy-by-using-azure-devops-pipelines/)

## <a name="content-types-conversions-and-transformations-within-a-run"></a>Типы содержимого и их преобразование во время выполнения

Можно использовать и преобразовывать различные типы содержимого с помощью многих функций, написанных на [языке определения рабочего процесса](https://aka.ms/logicappsdocs) Azure Logic Apps. Например, можно осуществлять взаимное преобразование строк, JSON и XML с помощью выражений рабочего процесса `@json()` и `@xml()`. Обработчик Logic Apps сохраняет типы содержимого, чтобы обеспечить передачу содержимого между службами без потерь.

* [Как выражения рабочего процесса работают в приложениях логики](../logic-apps/logic-apps-author-definitions.md)
* [Обработка типов содержимого, отличных от JSON](../logic-apps/logic-apps-content-type.md), например `application/xml`, `application/octet-stream` и `multipart/formdata`
* [Схема языка определения рабочих процессов в Azure Logic Apps](https://aka.ms/logicappsdocs)

## <a name="other-integrations-and-capabilities"></a>Прочие возможности интеграции и функции

Служба Logic Apps также поддерживает интеграцию со многими службами, включая Функции Azure, управление API Azure, службы приложений Azure и пользовательские конечные точки HTTP, например REST и SOAP.

* [Создание панели мониторинга сведений о клиентах в режиме реального времени с помощью Функций Azure и Azure Logic Apps](../logic-apps/logic-apps-scenario-social-serverless.md)
* [Вызов Функций Azure из приложений логики](../logic-apps/logic-apps-azure-functions.md)
* [Руководство по активации приложений логики с помощью решения "Функции Azure"](../logic-apps/logic-apps-scenario-function-sb-trigger.md)
* [Руководство по отслеживанию изменений виртуальной машины с помощью Azure Logic Apps и службы "Сетка событий Azure"](../event-grid/monitor-virtual-machine-changes-event-grid-logic-app.md)
* [Руководство. Создание функции, которая интегрируется с Azure Logic Apps и Azure Cognitive Services для анализа записей Twitter тональности](../azure-functions/functions-twitter-email.md)
* [Руководство по удаленному мониторингу и отправке уведомлений в Центре Интернета вещей с помощью службы Azure Logic Apps, обеспечивающей подключение между Центром Интернета вещей и почтовым ящиком](../iot-hub/iot-hub-monitoring-notifications-with-azure-logic-apps.md)
* [Блог. Вызов конечных точек SOAP из приложения логики](https://blogs.msdn.microsoft.com/logicapps/2016/04/07/using-soap-services-with-logic-apps/)

## <a name="end-to-end-scenarios"></a>Комплексные сценарии

* [Документация по интеграции комплексного управления случаями с помощью таких служб Azure, как Logic Apps](https://aka.ms/enterprise-integration-e2e-case-management-utilities-logic-apps)

## <a name="customer-stories"></a>Истории клиентов

Узнайте, как Azure Logic Apps, а также другие службы Azure и продукты Майкрософт помогли [этим компаниям](https://aka.ms/logic-apps-customer-stories) повысить гибкость и сосредоточиться на основных бизнес-целях благодаря упрощению, организации, автоматизации и оркестрации сложных процессов.

## <a name="next-steps"></a>Дальнейшие действия

* Дополнительные сведения о [соединителях для Logic Apps](../connectors/apis-list.md)
* Узнайте о [сценариях интеграции B2B Enterprise с Azure Logic Apps](../logic-apps/logic-apps-enterprise-integration-overview.md)
