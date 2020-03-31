---
title: Примеры & общих сценариев
description: Найдите примеры, общие сценарии, учебники и пошаговые руководства для приложений логики Azure
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 02/28/2020
ms.openlocfilehash: c64483f18b2deb061704a4eb7171e575fdd561e8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78164633"
---
# <a name="common-scenarios-examples-tutorials-and-walkthroughs-for-azure-logic-apps"></a>Распространенные сценарии, примеры, учебники и пошаговые руководства для Azure Logic Apps

[Azure Logic Apps](../logic-apps/logic-apps-overview.md) помогает вам организовывать и интегрировать различные службы, предоставляя [сотни готовых к использованию разъемов,](../connectors/apis-list.md)начиная от внутреннего сервера S'L или SAP и конкурируя с когнитивными службами Azure. Служба Logic Apps работает без серверов, поэтому вам не нужно заботиться об экземплярах и масштабировании. Достаточно определить рабочий процесс с триггером и действия для него. Базовая платформа обеспечивает масштабирование, доступность и производительность. Логические приложения особенно полезны для использования случаев и сценариев, где необходимо координировать действия в нескольких системах и службах.

Чтобы помочь вам узнать о возможностях и шаблонах, которые поддерживают azure Logic Apps, в этой статье описаны общие отправные точки, примеры и сценарии.

## <a name="common-starting-points-for-logic-app-workflows"></a>Общие отправные точки для рабочих процессов логических приложений

Каждое приложение логики запускается при помощи [*триггера*](../logic-apps/logic-apps-overview.md#logic-app-concepts) (это всегда только один триггер). Он инициирует рабочий процесс приложения логики и передает в него данные. Некоторые соединители предоставляют триггер одного из следующих типов:

* *Опрашивающий триггер* — регулярно проверяет конечную точку службы на наличие новых данных. Когда появляются новые данные, триггер создает и запускает новый экземпляр рабочего процесса, передавая эти данные в качестве входных.

* *Извещающий триггер* — прослушивает данные в конечной точке службы и ожидает определенного события. Когда происходит это событие, триггер немедленно срабатывает, то есть создает и запускает новый экземпляр рабочего процесса, передавая в качестве входных все доступные данные.

Вот примеры, описывающие широко используемые триггеры:

* *Триггеры опроса:*

  * [ **Повторяющийся** триггер](../connectors/connectors-native-recurrence.md) позволяет установить дату и время начала, а также повторение для запуска приложения логики. Например, вы можете выбрать дни недели и время суток для запуска приложения логики. Дополнительные сведения см. в следующих статьях:<p>

    * [Планирование и выполнение повторяющихся автоматизированных задач, процессов и рабочих процессов с помощью Azure Logic Apps](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md)
    * [Учебник: Создавайте автоматизированные повторяющиеся рабочие процессы на основе графика с помощью приложений Логики Azure](../logic-apps/tutorial-build-schedule-recurring-logic-app-workflow.md)

  * **Когда письмо получено** триггер позволяет логике приложение проверить новую электронную почту от любого поставщика почты, который поддерживается Logic Apps, например, [Office 365 Outlook](../connectors/connectors-create-api-office365-outlook.md), [Gmail](https://docs.microsoft.com/connectors/gmail/), [Outlook.com](https://docs.microsoft.com/connectors/outlook/), и так далее. Дополнительные сведения см. в следующих статьях:<p>

    * [Учебник: Создание автоматизированных рабочих процессов на основе утверждений с помощью приложений логики Azure](../logic-apps/tutorial-process-mailing-list-subscriptions-workflow.md)
    * [Учебник: Автоматизация задач для обработки сообщений электронной почты с помощью приложений логики Azure, функций Azure и хранилища Azure](../logic-apps/tutorial-process-email-attachments-workflow.md)

  * [ **Триггер HTTP** ](../connectors/connectors-native-http.md) может вызвать конечную точку службы через HTTP или HTTPS. Для получения дополнительной информации [см. Call, trigger или nest workflows с помощью конечных точек HTTP.](../logic-apps/logic-apps-http-endpoint.md)

* *Нажмите* триггеры:

  * Триггер [ **запроса** ](../connectors/connectors-native-reqres.md) может получать входящие запросы HTTPS.

  * [Триггер **Веб-перехватчик HTTP**](../connectors/connectors-native-webhook.md) подписывается на конечную точку службы, регистрируя в ней свой *URL-адрес обратного вызова*. В этом сценарии служба сама уведомляет триггер о том, что произошло указанное событие, то есть триггер может не опрашивать службу.

После того, как указанное событие произойдет, триггер срабатывает, что создает новую экземпляр рабочего процесса приложения логики и запускает действия в рабочем процессе. Вам будут доступны все данные триггера на любом этапе рабочего процесса. Например, Twitter **На новом триггере твита** передает содержимое твита в логическое приложение. Чтобы начать работу с azure Logic Apps, попробуйте следующие темы быстрого запуска:

* [Быстрый запуск: Создайте свой первый автоматизированный рабочий процесс с помощью приложений Логика Azure - Azure](../logic-apps/quickstart-create-first-logic-app-workflow.md)
* [Быстрый запуск: Создавайте автоматизированные задачи, процессы и рабочие процессы с помощью приложений Логика Azure - Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)
* [Быстрый запуск: Создание и управление автоматизированными рабочими процессами приложения логики с помощью Visual Studio Code](../logic-apps/quickstart-create-logic-apps-visual-studio-code.md)

## <a name="control-flow-and-error-handling-capabilities"></a>Возможности управления потоками и обработкой ошибок

Логические приложения включают богатые возможности для расширенного потока управления, такие как условия, коммутаторы, циклы и области. Чтобы обеспечить устойчивость решений, в рабочих процессах можно также реализовать обработку ошибок и исключений.

* Выполнение различных действий на основе [условных операторов](../logic-apps/logic-apps-control-flow-conditional-statement.md) и [операторов switch](../logic-apps/logic-apps-control-flow-switch-statement.md).
* [Повторяющиеся действия или обработка элементов в массивах и коллекциях с помощью циклов](../logic-apps/logic-apps-control-flow-loops.md).
* [Группировка действий с использованием областей](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md).
* [Добавление обработки ошибок и исключений в рабочий процесс](../logic-apps/logic-apps-exception-handling.md)
* [Вариант использования. Как компания в сфере здравоохранения использует обработку исключений в приложении логики для рабочих процессов HL7 FHIR](../logic-apps/logic-apps-scenario-error-and-exception-handling.md)

## <a name="create-custom-apis-and-connectors"></a>Создание пользовательских AIS и разъемов

Для систем и служб, которые не опубликовали разъемы, можно также расширить логические приложения.

* [Создание пользовательских AI для вызова из приложений логики Azure](../logic-apps/logic-apps-create-api-app.md)
* [Регулярно проверяйте наличие новых данных или событий с помощью шаблона триггера опроса](../logic-apps/logic-apps-create-api-app.md#polling-triggers)
* [Подождите и слушайте новые данные или события с помощью шаблона триггера Webhook](../logic-apps/logic-apps-create-api-app.md#webhook-triggers)
* [Выполняйте длительные выполнения задач с помощью шаблона действий опроса](../logic-apps/logic-apps-create-api-app.md#async-pattern)
* [Выполняйте длительные задачи с помощью шаблона действий webhook](../logic-apps/logic-apps-create-api-app.md#webhook-actions)
* [Пользовательские разъемы в приложениях логики Azure](../logic-apps/custom-connector-overview.md)

## <a name="build-business-to-business-b2b-solutions"></a>Создание бизнес-решений (B2B)

Для корпоративных интеграционных решений и беспрепятственной коммуникации между организациями можно создавать автоматизированные масштабируемые рабочие процессы для этих сценариев с помощью пакета интеграции предприятий (EIP) с приложениями Azure Logic Apps. Хотя организации используют разные протоколы и форматы, они могут обмениваться сообщениями в электронном формате. EIP преобразует различные форматы в формат, который системы организаций могут обрабатывать и поддерживает отраслевые протоколы, включая AS2, X12, EDIFACT и RosettaNet. Для создания этих решений необходимо создать учетную запись интеграции, которая является отдельным ресурсом Azure, который обеспечивает безопасный, масштабируемый и управляемый контейнер для артефактов, которые вы определяете и используете с помощью рабочих процессов приложения логики. Например, артефакты включают торговых партнеров, соглашения, карты, схемы, сертификаты и пакетные конфигурации.

* [Обзор: Решения для корпоративной интеграции B2B с приложениями Azure Logic Apps и пакетом корпоративной интеграции](../logic-apps/logic-apps-enterprise-integration-overview.md)
* [Создание и управление учетными записями интеграции для интеграции B2B в приложениях логики Azure](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)

## <a name="access-azure-virtual-network-resources"></a>Доступ к виртуальным сетевым ресурсам Azure

Иногда вашим логическим приложениям и учетным записям интеграции необходим доступ к защищенным ресурсам, таким как виртуальные машины (Виртуальные технологии) и другим системам или службам, которые находятся в виртуальной сети Azure. Чтобы настроить этот доступ, можно создать среду интеграционных служб (ISE), где можно создавать и запускать логические приложения. ISE — это частный и изолированный экземпляр службы Logic Apps, которая использует выделенные ресурсы, такие как хранение, и работает отдельно от публичной, «глобальной» мультитенантной службы логических приложений. Разделение изолированного частного экземпляра и общедоступного глобального экземпляра также помогает уменьшить влияние других клиентов Azure на производительность приложений, которое также известно как эффект «шумных соседей».

* [Обзор: Доступ к виртуальным сетевым ресурсам Azure из приложений логики Azure](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)
* [Подключение к виртуальным сетям Azure из Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)

## <a name="deploy-manage-and-monitor-logic-apps"></a>Развертывание, управление и мониторинг логических приложений

Можно полностью разработать и развернуть приложения логики с помощью Visual Studio, Azure DevOps или любых других инструментов системы управления версиями и автоматической сборки. Для поддержки развертывания рабочих процессов и зависимых подключений в шаблоне ресурсов приложения логики используют шаблоны развертывания ресурсов Azure. Средства Visual Studio автоматически создают эти шаблоны, которые можно записать после изменения в систему управления версиями для управления версиями. Для ведения журналов уведомлений и диагностики состояния рабочего процесса служба Azure Logic Apps обеспечивает средства мониторинга и уведомления.

### <a name="deploy"></a>Развертывание

* [Быстрый запуск: Создавайте автоматизированные задачи, процессы и рабочие процессы с помощью приложений Логика Azure - Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)
* [Обзор: Автоматизация развертывания приложения логики](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)
* [Создание шаблонов управления ресурсами Azure для автоматизации развертывания для приложений логики Azure](../logic-apps/logic-apps-create-azure-resource-manager-templates.md)
* [Развертывание шаблонов диспетчера ресурсов Azure для логических приложений Azure](../logic-apps/logic-apps-deploy-azure-resource-manager-templates.md)
* [Пример: Подключение к очередям в шины Azure из приложений логики Azure и развертывание с помощью проводов Azure в Azure DevOps](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-azure-service-bus-queues-from-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Пример: Подключение к учетным записям хранения Azure из приложений логики Azure и развертывание с помощью конвейеров Azure в Azure DevOps](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-azure-storage-accounts-from-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Пример: Настройка действия приложения функции для приложений Логики Azure и развертывание с помощью проводов Azure в Azure DevOps](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/set-up-an-azure-function-app-action-for-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Пример: Подключение к учетной записи интеграции из приложений логики Azure и развертывание с помощью проводов Azure в Azure DevOps](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-an-integration-account-from-azure-logic-apps-and-deploy-by-using-azure-devops-pipelines/)
* [Пример: Оркестровые трубопроводы Azure с помощью приложений Логики Azure](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-pipeline-orchestration/azure-devops-orchestration-with-logic-apps/)

### <a name="manage"></a>Управление

* [Управление логическими приложениями с помощью Visual Studio](../logic-apps/manage-logic-apps-with-visual-studio.md)
* [Создание и управление учетными записями интеграции для интеграции B2B корпоративных](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)
* [Управление средой интеграционных служб (ISE) в приложениях логики Azure](../logic-apps/ise-manage-integration-service-environment.md)

### <a name="monitor"></a>Монитор

* [Мониторинг состояния выполнения, просмотр истории триггеров и настройка оповещений для приложений логики Azure](../logic-apps/monitor-logic-apps.md)
* [Настройка журналов Azure Monitor и сбор диагностических данных для приложений логики Azure](../logic-apps/monitor-logic-apps-log-analytics.md)
* [Настройка журналов Azure Monitor и сбор диагностических данных для B2B-сообщений в приложениях логики Azure](../logic-apps/monitor-b2b-messages-log-analytics.md)
* [Просмотр и создание запросов для мониторинга и отслеживания в журналах Azure Monitor для приложений логики Azure](../logic-apps/create-monitoring-tracking-queries.md)

## <a name="handle-content-types-conversions-and-transformations"></a>Обработка типов содержимого, преобразований и преобразований

Можно использовать и преобразовывать различные типы содержимого с помощью многих функций, написанных на [языке определения рабочего процесса](https://aka.ms/logicappsdocs) Azure Logic Apps. Например, можно осуществлять взаимное преобразование строк, JSON и XML с помощью выражений рабочего процесса `@json()` и `@xml()`. Обработчик Logic Apps сохраняет типы содержимого, чтобы обеспечить передачу содержимого между службами без потерь.

* [Обработка типов содержимого в приложениях логики Azure,](../logic-apps/logic-apps-content-type.md)таких как, `application/` `application/octet-stream`и`multipart/formdata`
* [Справочное руководство по использованию функций в выражениях для приложений логики Azure и автоматизации питания](../logic-apps/workflow-definition-language-functions-reference.md)
* [Схема языка определения рабочих процессов в Azure Logic Apps](../logic-apps/logic-apps-workflow-definition-language.md)

## <a name="other-integrations-and-capabilities"></a>Прочие возможности интеграции и функции

Логические приложения Azure интегрируются со многими службами, такими как Функции Azure, управление API-изапи-наудейок Azure, служба приложений Azure и пользовательские конечные точки HTTP, например, REST и SOAP.

* [Вызов функций Azure из приложений логики Azure](../logic-apps/logic-apps-azure-functions.md)
* [Учебник: Вызов или триггер логических приложений с помощью функций Azure и шины службы Azure](../logic-apps/logic-apps-scenario-function-sb-trigger.md)
* [Учебник: Создание потоковой панели мониторинга клиентов с помощью приложений логики Azure и функций Azure](../logic-apps/logic-apps-scenario-social-serverless.md)
* [Учебник: Создайте функцию, которая интегрируется с приложениями Azure Logic Apps и Azure Cognitive Services для анализа настроений в Twitter](../azure-functions/functions-twitter-email.md)
* [Учебник: Создайте социальную приборную панель с питанием от ИИ с помощью приложений Power BI и Azure Logic Apps](https://aka.ms/logicappsdemo)
* [Учебник: Мониторинг изменений виртуальной машины с помощью Azure Event Grid и логических приложений](../event-grid/monitor-virtual-machine-changes-event-grid-logic-app.md)
* [Руководство по удаленному мониторингу и отправке уведомлений в Центре Интернета вещей с помощью службы Azure Logic Apps, обеспечивающей подключение между Центром Интернета вещей и почтовым ящиком](../iot-hub/iot-hub-monitoring-notifications-with-azure-logic-apps.md)
* [Блог: Вызов услуг SOAP с помощью приложений Логика Azure](https://blogs.msdn.microsoft.com/logicapps/2016/04/07/using-soap-services-with-logic-apps/)

## <a name="end-to-end-scenarios"></a>Комплексные сценарии

* [Документация по интеграции комплексного управления случаями с помощью таких служб Azure, как Logic Apps](https://aka.ms/enterprise-integration-e2e-case-management-utilities-logic-apps)

## <a name="customer-stories"></a>Истории клиентов

Узнайте, как Azure Logic Apps, а также другие службы Azure и продукты Майкрософт помогли [этим компаниям](https://aka.ms/logic-apps-customer-stories) повысить гибкость и сосредоточиться на основных бизнес-целях благодаря упрощению, организации, автоматизации и оркестрации сложных процессов.

## <a name="next-steps"></a>Дальнейшие действия

* Узнайте больше о [разъемах для логических приложений](../connectors/apis-list.md)
* Узнайте о [сценариях интеграции предприятий B2B с помощью приложений Azure Logic](../logic-apps/logic-apps-enterprise-integration-overview.md)
