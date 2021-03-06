---
title: Миграция из планировщика Azure в Azure Logic Apps
description: Узнайте, как можно заменить задания в планировщике Azure, поддержка которого будет прекращена, на задания Azure Logic Apps.
services: scheduler
ms.service: scheduler
ms.suite: infrastructure-services
author: derek1ee
ms.author: deli
ms.reviewer: klam, LADocs
ms.topic: article
ms.date: 09/23/2019
ms.openlocfilehash: 3d748e1625f27be27b7f403fbab3e213b16c4dd6
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/09/2019
ms.locfileid: "73890178"
---
# <a name="migrate-azure-scheduler-jobs-to-azure-logic-apps"></a>Перемещение заданий из планировщика Azure в Azure Logic Apps

> [!IMPORTANT]
> [Azure Logic Apps](../logic-apps/logic-apps-overview.md) заменяет планировщик Azure, который выводится из [эксплуатации](#retire-date). Чтобы продолжить работу с заданиями, настроенными в планировщике, перейдите в Azure Logic Apps как можно скорее, следуя этой статье. 

В этой статье показано, как можно планировать одноразовые и повторяющиеся задания, создавая автоматические рабочие процессы в Azure Logic Apps вместо планировщика Azure. Когда вы создаете запланированные задания в Logic Apps, вы получаете следующие преимущества:

* Создавайте свои задания с помощью визуального конструктора и [готовых соединителей](../connectors/apis-list.md) из сотен служб, таких как хранилище BLOB-объектов Azure, служебная шина Azure, Office 365 Outlook и SAP.

* Управление каждым запланированным рабочим процессом в качестве ресурса Azure первого класса. Вам не нужно беспокоиться о концепции *коллекции заданий* , так как каждое приложение логики является отдельным ресурсом Azure.

* Выполнение нескольких одноразовых заданий с помощью одного приложения логики.

* Установите расписания, поддерживающие часовые пояса, и автоматически подкорректируйте летнее время (DST).

Дополнительные сведения см. в статьях [что такое Azure Logic Apps?](../logic-apps/logic-apps-overview.md) или попробуйте создать первое приложение логики в этом кратком руководстве: [Создание первого приложения логики](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="prerequisites"></a>предварительным требованиям

* Подписка Azure. Если у вас еще нет подписки Azure, [зарегистрируйтесь для получения бесплатной учетной записи Azure](https://azure.microsoft.com/free/).

* Чтобы активировать приложение логики с помощью HTTP-запросов, используйте средство, такое как [классическое приложение Postman](https://www.getpostman.com/apps).

## <a name="migrate-by-using-a-script"></a>Миграция с помощью скрипта

Каждое задание планировщика является уникальным, поэтому для переноса заданий планировщика в Azure Logic Apps не подходит ни одного размера. Однако [этот сценарий можно изменить](https://github.com/Azure/logicapps/tree/master/scripts/scheduler-migration) в соответствии с вашими потребностями.

## <a name="schedule-one-time-jobs"></a>Планирование одноразовых заданий

Вы можете запускать несколько одноразовых заданий с помощью единого приложения логики. 

1. На [портале Azure](https://portal.azure.com) создайте пустое приложение логики в конструкторе приложений логики. 

   Основные шаги описаны в статье [Краткое руководство. Создание первого автоматизированного рабочего процесса с помощью Azure Logic Apps на портале Azure](../logic-apps/quickstart-create-first-logic-app-workflow.md).

1. В поле поиска введите фильтр "когда HTTP-запрос". В списке триггеров выберите триггер **При получении HTTP-запроса**. 

   ![Добавление триггера запросов](./media/migrate-from-scheduler-to-logic-apps/request-trigger.png)

1. Для триггера запросов можно дополнительно предоставить схему JSON, которая помогает конструктору приложений логики понять структуру входных данных входящего запроса и облегчит вам выбор выходных данных в рабочем процессе.

   Чтобы указать схему, введите ее в поле **Схема JSON текста запроса**, например: 

   ![Схема запроса](./media/migrate-from-scheduler-to-logic-apps/request-schema.png)

   Если у вас нет схемы, но у вас есть пример полезных данных в формате JSON, можно создать схему на основе полезных данных.

   1. В триггере запросов выберите **Использовать пример полезной нагрузки, чтобы создать схему**.

   1. В разделе **введите или вставьте образец полезных данных JSON**, укажите полезные данные примера, а затем выберите **Готово**, например:

      ![Пример полезных данных](./media/migrate-from-scheduler-to-logic-apps/sample-payload.png)

1. В разделе триггера выберите **следующий шаг**. 

1. В поле поиска введите "задержка до" в качестве условия фильтра. В списке действий выберите действие **Задержка до**.

   Это действие приостанавливает рабочий процесс вашего приложения логики до указанной даты и времени.

   ![Добавление действия "Задержка до"](./media/migrate-from-scheduler-to-logic-apps/delay-until.png)

1. Введите отметку времени, когда требуется запустить рабочий процесс приложения логики. 

   Когда вы щелкаете внутри поля **Метка времени**, появляется список динамического содержимого, и вы можете выбрать выходные данные из триггера.

   ![Указание сведений "Задержка до"](./media/migrate-from-scheduler-to-logic-apps/delay-until-details.png)

1. Добавьте любые другие действия, которые необходимо выполнить, выбрав один из [сотен готовых соединителей](../connectors/apis-list.md). 

   Например, вы можете включить действие HTTP, которое отправляет запрос на URL-адрес, или действия, которые работают с очередями хранения, очередями или темами служебной шины: 

   ![Действие HTTP](./media/migrate-from-scheduler-to-logic-apps/request-http-action.png)

1. Сохраните приложение логики, когда закончите.

   ![Сохранение приложения логики](./media/migrate-from-scheduler-to-logic-apps/save-logic-app.png)

   Когда вы впервые сохраняете свое приложение логики, URL-адрес конечной точки для триггера запросов вашего приложения появляется в поле **URL-адрес HTTP POST**. 
   Если вы хотите вызвать приложение логики и отправить в него входные данные для обработки, используйте в качестве назначения вызова этот URL-адрес.

   ![Сохранение URL-адреса конечной точки триггера запроса](./media/migrate-from-scheduler-to-logic-apps/request-endpoint-url.png)

1. Скопируйте и сохраните этот URL-адрес конечной точки, чтобы впоследствии вы могли вручную отправить запрос, который запускает ваше приложение логики. 

## <a name="start-a-one-time-job"></a>Запуск одноразового задания

Чтобы вручную запустить или активировать одноразовое задание, отправьте вызов на адрес конечной точки для триггера запроса приложения логики. В этом вызове укажите входные или полезные данные для отправки, которые вы могли описать ранее, указав схему. 

Например, с помощью приложения POST можно создать запрос POST с параметрами, аналогичными приведенным в этом примере, а затем нажать кнопку **Отправить** , чтобы выполнить запрос.

| Метод запроса | URL-адрес | Текст | Заголовки |
|----------------|-----|------|---------|
| **POST** | <*endpoint-URL*> | **raw** (Без форматирования) <p>**JSON (application/json)** <p>В поле **необработанные** введите полезные данные, которые необходимо отправить в запросе. <p>**Примечание**. Этот параметр автоматически настраивает значения **Headers** (Заголовки). | **Ключ**: Content-Type <br>**Значение**: application/json |
|||||

![Отправка запроса на запуск приложения логики вручную](./media/migrate-from-scheduler-to-logic-apps/postman-send-post-request.png)

После отправки вызова ответ из приложения логики появится в поле **raw** (Без форматирования) на вкладке **Body** (Текст). 

<a name="workflow-run-id"></a>

> [!IMPORTANT]
>
> Если вы хотите отменить задание позже, перейдите на вкладку **заголовки** . Найдите и скопируйте значение заголовка **x-MS-Workflow-Run-ID** в ответе. 
>
> ![Ответ](./media/migrate-from-scheduler-to-logic-apps/postman-response.png)

## <a name="cancel-a-one-time-job"></a>Отмена одноразового задания

В Logic Apps каждое одноразовое задание выполняется как отдельный экземпляр запуска приложения логики. Чтобы отменить одноразовое задание, можно использовать команды [отмены выполнений рабочего процесса](https://docs.microsoft.com/rest/api/logic/workflowruns/cancel) в REST API Logic Apps. Когда вы отправляете вызов триггеру, укажите [идентификатор выполнения рабочего процесса](#workflow-run-id).

## <a name="schedule-recurring-jobs"></a>Планирование повторяющихся заданий

1. На [портале Azure](https://portal.azure.com) создайте пустое приложение логики в конструкторе приложений логики. 

   Основные шаги описаны в статье [Краткое руководство. Создание первого автоматизированного рабочего процесса с помощью Azure Logic Apps на портале Azure](../logic-apps/quickstart-create-first-logic-app-workflow.md).

1. В поле поиска введите слово "повторение" в качестве фильтра. В списке триггеров выберите триггер **Периодичность**. 

   ![Добавление триггера повторения](./media/migrate-from-scheduler-to-logic-apps/recurrence-trigger.png)

1. При желании можно настроить более расширенное расписание.

   ![Расширенное расписание](./media/migrate-from-scheduler-to-logic-apps/recurrence-advanced-schedule.png)

   Дополнительные сведения о расширенных параметрах планирования см. [в статье Создание и запуск повторяющихся задач и рабочих процессов с помощью Azure Logic Apps](../connectors/connectors-native-recurrence.md).

1. Добавьте другие необходимые действия, выбрав их из [сотен готовых к использованию](../connectors/apis-list.md). В разделе триггера выберите **следующий шаг**. Найдите и выберите нужные действия.

   Например, вы можете включить действие HTTP, которое отправляет запрос на URL-адрес, или действия, которые работают с очередями хранения, очередями или темами служебной шины: 

   ![Действие HTTP](./media/migrate-from-scheduler-to-logic-apps/recurrence-http-action.png)

1. Сохраните приложение логики, когда закончите.

   ![Сохранение приложения логики](./media/migrate-from-scheduler-to-logic-apps/save-logic-app.png)

## <a name="advanced-setup"></a>Расширенная настройка

Здесь вы можете настроить свои задания.

### <a name="retry-policy"></a>Политика повтора

Чтобы контролировать способ повторного запуска действия в вашем приложении логики, когда происходят прерывистые сбои, вы можете установить [политику повтора](../logic-apps/logic-apps-exception-handling.md#retry-policies) в параметрах каждого действия, например:

1. Откройте меню с многоточием ( **...** ) действия и выберите **Параметры**.

   ![Открытие параметров действия](./media/migrate-from-scheduler-to-logic-apps/action-settings.png)

1. Выберите нужную политику повтора. Дополнительные сведения см. в разделе [Политики повтора](../logic-apps/logic-apps-exception-handling.md#retry-policies).

   ![Выбор политики повтора](./media/migrate-from-scheduler-to-logic-apps/retry-policy.png)

## <a name="handle-exceptions-and-errors"></a>Обработка ошибок и исключений

В планировщике Azure, если действие по умолчанию не выполняется, вы можете запустить альтернативное действие, которое учитывает условие ошибки. В Azure Logic Apps вы можете выполнить ту же задачу.

1. В конструкторе приложений логики над действием, которое необходимо обработано, переместите указатель на стрелку между шагами и выберите **добавить параллельную ветвь**. 

   ![Добавление параллельной ветви](./media/migrate-from-scheduler-to-logic-apps/add-parallel-branch.png)

1. Найдите и выберите действие, которое нужно использовать в качестве альтернативного действия.

   ![Добавление параллельного действия](./media/migrate-from-scheduler-to-logic-apps/add-parallel-action.png)

1. В альтернативном действии откройте меню с многоточием ( **...** ) и выберите пункт **настроить выполнение после**.

   ![Настройка последующего запуска](./media/migrate-from-scheduler-to-logic-apps/configure-run-after.png)

1. Снимите флажок свойства **выполнено**. Выберите эти свойства: **завершился сбоем**, **пропущен** и **прекращено по таймауту**.

   ![Настройка свойств последующего запуска](./media/migrate-from-scheduler-to-logic-apps/select-run-after-properties.png)

1. По завершении нажмите кнопку **Готово**.

Дополнительные сведения об обработке исключений см. в разделе [Перехват и обработка ошибок с помощью свойства RunAfter](../logic-apps/logic-apps-exception-handling.md#catch-and-handle-failures-with-the-runafter-property).

## <a name="faq"></a>часто задаваемые вопросы

<a name="retire-date"></a>

**В**. Когда прекращается поддержка планировщика Azure? <br>
Ответ. запланировано полное прекращение **использования**планировщика Azure 31 декабря 2019 г. Важные шаги, которые необходимо выполнить до этой даты и подробной временной шкалы, см. в разделе Расширение даты прекращения действия [для планировщика до 31 декабря 2019](https://azure.microsoft.com/updates/extending-retirement-date-of-scheduler/). Общие сведения об обновлениях см. в разделе [планировщик обновлений Azure](https://azure.microsoft.com/updates/?product=scheduler).

**Вопрос**. что происходит с моими коллекциями заданий и заданиями после завершения службы? <br>
Ответ **. все**коллекции заданий и задания планировщика перестают выполняться и удаляются из системы.

**В**. Нужно ли выполнять резервное копирование или какие-либо другие задачи перед переносом заданий планировщика в Logic Apps? <br>
**О**. Рекомендуется всегда создавать резервную копию своей работы. Убедитесь, что созданные вами приложения логики работают, как ожидалось, прежде чем удалять или отключать задания планировщика. 

**В**. Есть ли инструмент, который помогает перенести задания из планировщика в Logic Apps? <br>
**О**. Каждое задание планировщика уникально, поэтому не существует универсального средства. Однако в зависимости от ваших потребностей вы можете [изменить этот скрипт, чтобы перенести задания планировщика Azure в Azure Logic Apps](https://github.com/Azure/logicapps/tree/master/scripts/scheduler-migration).

**В**. Где я могу получить поддержку, чтобы перенести свои задания планировщика? <br>
**О**. Ниже приведены некоторые способы получения поддержки. 

**портал Azure**

Если ваша подписка Azure имеет платный план поддержки, вы можете создать запрос в службу технической поддержки на портале Azure. В противном случае вы можете выбрать другой вариант поддержки.

1. В главном меню на [портале Azure](https://portal.azure.com) выберите **Справка + поддержка**.

1. В меню **Поддержка** выберите пункт **новый запрос в службу поддержки**. Укажите следующие сведения о запросе:

   | Свойство | Значение |
   |---------|-------|
   | **Тип проблемы** | **Технические требования** |
   | **Подписка** | <*ваша_подписка_Azure*> |
   | **Служба** | В разделе **Мониторинг и управление** выберите **Планировщик**. Если вы не можете найти **планировщик**, сначала выберите **все службы** . |
   ||| 

1. Выберите нужный вариант поддержки. Если у вас есть платный план поддержки, нажмите кнопку **Далее**.

**Сообщество**

* [Форум Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-scheduler)

## <a name="next-steps"></a>Дополнительная информация

* [Создание регулярно выполняющихся рабочих процессов и задач с помощью Azure Logic Apps](../connectors/connectors-native-recurrence.md)
* [Проверка трафика при помощи приложения логики, которое выполняется по расписанию](../logic-apps/tutorial-build-schedule-recurring-logic-app-workflow.md)
