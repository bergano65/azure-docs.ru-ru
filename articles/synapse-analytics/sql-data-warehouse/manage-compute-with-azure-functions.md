---
title: Руководство. Управление средами вычислений с помощью функций Azure
description: Как использовать функции Azure для управления вычислением пула SQL в Azure синапсе Analytics.
services: synapse-analytics
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/27/2018
ms.author: jrasnick
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 9d680283250cc323c833f388f6b20d7fe6fa132d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85211057"
---
# <a name="use-azure-functions-to-manage-compute-resources-in-azure-synapse-analytics-sql-pool"></a>Использование функций Azure для управления ресурсами вычислений в пуле SQL Azure синапсе Analytics

В этом руководстве используются функции Azure для управления ресурсами вычислений для пула SQL в Azure синапсе Analytics.

Чтобы использовать Azure приложение-функция с пулом SQL, необходимо создать [учетную запись субъекта-службы](../../active-directory/develop/howto-create-service-principal-portal.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) с доступом участника в той же подписке, что и экземпляр пула SQL.

## <a name="deploy-timer-based-scaling-with-an-azure-resource-manager-template"></a>Развертывание масштабирования по таймеру с помощью шаблона Azure Resource Manager

Чтобы развернуть шаблон, понадобится следующая информация:

- Имя группы ресурсов, в которой находится экземпляр пула SQL
- Имя сервера, на котором находится экземпляр пула SQL
- Имя экземпляра пула SQL
- идентификатор клиента (идентификатор каталога) Azure Active Directory;
- Идентификатор подписки
- идентификатор приложения субъекта-службы;
- секретный ключ субъекта-службы.

После получения предыдущей информации разверните этот шаблон:

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FMicrosoft%2Fsql-data-warehouse-samples%2Fmaster%2Farm-templates%2FsqlDwTimerScaler%2Fazuredeploy.json" target="_blank">
<img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/>
</a>

После развертывания шаблона, необходимо найти три новых ресурса: бесплатный план обслуживания приложений Azure, план приложения-функции на основе потребления и учетную запись хранения, которая управляет журналом и очередью операций. Продолжайте изучать следующие разделы, чтобы узнать, как изменить развернутые функции в соответствии с потребностями.

## <a name="change-the-compute-level"></a>Изменение уровня вычислений

1. Перейдите к службе приложения-функции. Если вы развернули шаблон со значениями по умолчанию, эта служба должна называться *DWOperations*. Когда приложение-функция откроется, вы увидите пять развернутых функций в службе приложения-функции.

   ![Функции, развернутые с помощью шаблона](./media/manage-compute-with-azure-functions/five-functions.png)

2. Выберите *DWScaleDownTrigger* или *DWScaleUpTrigger* в зависимости от того, что нужно изменить: время увеличения или уменьшения масштаба. В раскрывающемся меню выберите пункт "Интегрировать".

   ![Выбор пункта "Интегрировать" для функции](./media/manage-compute-with-azure-functions/select-integrate.png)

3. В текущий момент должно отображаться значение *%ScaleDownTime%* или *%ScaleUpTime%*. Эти значения указывают на то, что расписание основано на значениях, определенных в [параметрах приложения](../../azure-functions/functions-how-to-use-azure-function-app-settings.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json). Пока что это значение можно проигнорировать и изменить время в расписании по своему усмотрению, основываясь на следующих шагах.

4. В области расписания добавьте время, которое должно отражаться в выражении CRON, чтобы определить частоту увеличения масштаба хранилища данных SQL.

   ![Изменение расписания функции](./media/manage-compute-with-azure-functions/change-schedule.png)

   Значение `schedule` представляет собой [выражение CRON](https://en.wikipedia.org/wiki/Cron#CRON_expression) с шестью полями:

   ```json
   {second} {minute} {hour} {day} {month} {day-of-week}
   ```

   Например, *«0 30 9 * * 1-5»* будет отражать триггер, активирующийся каждый будний день в 9:30. Дополнительные сведения см. в [примерах расписаний](../../azure-functions/functions-bindings-timer.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#example) службы "Функции Azure".

## <a name="change-the-time-of-the-scale-operation"></a>Изменение времени операции масштабирования

1. Перейдите к службе приложения-функции. Если вы развернули шаблон со значениями по умолчанию, эта служба должна называться *DWOperations*. Когда приложение-функция откроется, вы увидите пять развернутых функций в службе приложения-функции.

2. Выберите *DWScaleDownTrigger* или *DWScaleUpTrigger* в зависимости от того, что нужно изменить: значение увеличения или уменьшения масштаба вычислений. После выбора функций в области должен отобразиться файл *index.js*.

   ![Изменение уровня вычислений триггера функции](././media/manage-compute-with-azure-functions/index-js.png)

3. Замените значение параметра *ServiceLevelObjective* на значение необходимого уровня и нажмите кнопку "Сохранить". Это значение задает уровень вычислений, на основе которого будет масштабироваться ваш экземпляр хранилища данных в соответствии с расписанием, определенным в разделе «Интеграция».

## <a name="use-pause-or-resume-instead-of-scale"></a>Использование приостановки или возобновления работы вместо масштабирования

Сейчас по умолчанию включены функции *DWScaleDownTrigger* и *DWScaleUpTrigger*. Если вместо функции масштабирования вам нужно использовать приостановку или возобновление работы, можете включить *DWPauseTrigger* или *DWResumeTrigger*.

1. Перейдите на панель "Функции".

   ![Панель "Функции"](./media/manage-compute-with-azure-functions/functions-pane.png)

2. Передвиньте переключатели для соответствующих триггеров, которые нужно включить.

3. Перейдите на вкладки *интеграции* для соответствующих триггеров, чтобы изменить их расписание.

   > [!NOTE]
   > Функциональная разница между триггерами масштабирования и триггерами паузы или возобновления — это сообщение, отправленное в очередь. Дополнительные сведения см. в разделе [Добавление новой функции триггера](manage-compute-with-azure-functions.md#add-a-new-trigger-function).

## <a name="add-a-new-trigger-function"></a>Добавление новой функции триггера

Сейчас в шаблоне содержатся только две функции масштабирования. С помощью этих функций в течение дня можно только один раз уменьшить масштаб и один раз увеличить его. Для более детального контроля, например для уменьшения масштаба несколько раз в день или настройки другого режима масштабирования на выходные дни, нужно добавить еще один триггер.

1. Создайте пустую функцию. Нажмите *+* кнопку рядом с расположением функций, чтобы отобразить панель шаблон функции.

   ![Создание функции](./media/manage-compute-with-azure-functions/create-new-function.png)

2. На языке выберите *JavaScript*, а затем выберите *TimerTrigger*.

   ![Создание функции](./media/manage-compute-with-azure-functions/timertrigger-js.png)

3. Присвойте функции имя и настройте шаблон. На рисунке показано, как активировать функцию каждую субботу в полночь (поздним вечером в пятницу).

   ![Уменьшение масштаба в субботу](./media/manage-compute-with-azure-functions/scale-down-saturday.png)

4. Скопируйте содержимое файла *index.js* из других функций триггера.

   ![Копирование содержимого файла index.js](././media/manage-compute-with-azure-functions/index-js.png)

5. Установите для переменной операции требуемое поведение следующим образом:

   ```JavaScript
   // Resume the SQL pool instance
   var operation = {
       "operationType": "ResumeDw"
   }

   // Pause the SQL pool instance
   var operation = {
       "operationType": "PauseDw"
   }

   // Scale the SQL pool instance to DW600
   var operation = {
       "operationType": "ScaleDw",
       "ServiceLevelObjective": "DW600"
   }
   ```

## <a name="complex-scheduling"></a>Создание сложного расписания

В этом разделе кратко показано, что необходимо для более комплексного планирования возможностей паузы, возобновления и масштабирования.

### <a name="example-1"></a>Пример 1

Ежедневное увеличение масштаба до значения DW600 в 8:00 и уменьшение масштаба до DW200 в 20:00.

| Функция  | Расписание     | Операция                                |
| :-------- | :----------- | :--------------------------------------- |
| Функция 1 | 0 0 8 * * *  | `var operation = {"operationType": "ScaleDw",    "ServiceLevelObjective": "DW600"}` |
| Функция 2 | 0 0 20 * * * | `var operation = {"operationType": "ScaleDw", "ServiceLevelObjective": "DW200"}` |

### <a name="example-2"></a>Пример 2

Ежедневное увеличение масштаба в 8:00 до DW1000, однократное уменьшение до DW600 в 16:00 и уменьшение масштаба в 22:00 до DW200.

| Функция  | Расписание     | Операция                                |
| :-------- | :----------- | :--------------------------------------- |
| Функция 1 | 0 0 8 * * *  | `var operation = {"operationType": "ScaleDw",    "ServiceLevelObjective": "DW1000"}` |
| Функция 2 | 0 0 16 * * * | `var operation = {"operationType": "ScaleDw", "ServiceLevelObjective": "DW600"}` |
| Функция 3 | 0 0 22 * * * | `var operation = {"operationType": "ScaleDw", "ServiceLevelObjective": "DW200"}` |

### <a name="example-3"></a>Пример 3

Увеличение масштаба до DW1000 в 8:00 и уменьшение масштаба до DW600 один раз в 16:00 в рабочие дни. Приостановка работы в пятницу в 23:00 и возобновление работы в понедельник в 7:00.

| Функция  | Расписание       | Операция                                |
| :-------- | :------------- | :--------------------------------------- |
| Функция 1 | 0 0 8 * * 1-5  | `var operation = {"operationType": "ScaleDw",    "ServiceLevelObjective": "DW1000"}` |
| Функция 2 | 0 0 16 * * 1-5 | `var operation = {"operationType": "ScaleDw", "ServiceLevelObjective": "DW600"}` |
| Функция 3 | 0 0 23 * * 5   | `var operation = {"operationType": "PauseDw"}` |
| Функция 4 | 0 0 7 * * 0    | `var operation = {"operationType": "ResumeDw"}` |

## <a name="next-steps"></a>Дальнейшие шаги

Дополнительные сведения о функциях Azure триггера с таймером см. [здесь](../../azure-functions/functions-create-scheduled-function.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).

Извлеките [репозиторий образцов](https://github.com/Microsoft/sql-data-warehouse-samples)пула SQL.
