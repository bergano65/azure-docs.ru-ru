---
title: Учебник - Расписание задачи ACR
description: В этом уроке узнайте, как запустить задачу регистрации контейнеров Azure в определенном графике, установив один или несколько триггеров таймера
ms.topic: article
ms.date: 06/27/2019
ms.openlocfilehash: 3202b5d8c426165d81129f1affa69b3a3d515ce9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78402880"
---
# <a name="run-an-acr-task-on-a-defined-schedule"></a>Выполнить задачу ACR в определенном графике

В этом учебнике показано, как запустить [aCR Task](container-registry-tasks-overview.md) по расписанию. Запланируйте задачу, установив один или несколько *триггеров таймер.* Таймер триггеры могут быть использованы в одиночку, или в сочетании с другими триггерами задачи.

В этом уроке узнайте о задачах планирования и:

> [!div class="checklist"]
> * Создать задачу с помощью триггера таймера
> * Управление триггерами таймер

Планирование задачи полезно для таких сценариев, как:

* Запустите нагрузку контейнера для плановых операций по техническому обслуживанию. Например, запустите контейнерное приложение для удаления ненужных изображений из реестра.
* Выполнить набор тестов на производственном изображении в течение рабочего дня в рамках мониторинга в реальном времени.

Для запуска примеров в этой статье можно использовать оболочку Azure Cloud Shell или локальную установку Azure CLI. Если вы хотите использовать его локально, требуется версия 2.0.68 или позже. Чтобы узнать версию, выполните команду `az --version`. Если вам нужно установить или обновить, [см.][azure-cli-install]


## <a name="about-scheduling-a-task"></a>О планировании задачи

* **Триггер с выражением cron** - триггер таймер для задачи использует *выражение cron.* Выражение представляет собой строку с пятью полями, указывающими минуту, час, день, месяц и день недели для запуска задачи. Поддерживаются частоты до одного раза в минуту.

  Например, выражение `"0 12 * * Mon-Fri"` запускает задачу в полдень UTC в каждый будний день. [Подробности](#cron-expressions) смотрите позже в этой статье.
* **Допускается добавление** нескольких таймеров к задаче, если расписание будет отличаться.
    * Укажите несколько триггеров таймер при создании задачи или добавьте их позже.
    * Дополнительно назовите триггеры для более простого управления, или задачи ACR предоставят имена триггеров по умолчанию.
    * Если графики таймера перекрываются одновременно, задачи ACR запускают задачу в запланированное время для каждого таймера.
* **Другие триггеры задач** - В задаче, вызванной таймером, вы также можете включить триггеры на основе [фиксации исходного кода](container-registry-tutorial-build-task.md) или [обновления базового изображения.](container-registry-tutorial-base-image-update.md) Как и другие задачи ACR, вы также можете [вручную вызвать][az-acr-task-run] запланированную задачу.

## <a name="create-a-task-with-a-timer-trigger"></a>Создать задачу с помощью триггера таймера

При создании задачи с [задачей az acr создать команду][az-acr-task-create] можно дополнительно добавить триггер таймер. Добавьте `--schedule` параметр и передайте выражение cron для таймера.

В качестве простого примера следующая `hello-world` команда запускает изображение из Docker Hub каждый день в 21:00 UTC. Задача выполняется без контекста исходного кода.

```azurecli
az acr task create \
  --name mytask \
  --registry myregistry \
  --cmd hello-world \
  --schedule "0 21 * * *" \
  --context /dev/null
```

Выполнить [команду шоу-задачи az acr,][az-acr-task-show] чтобы увидеть, что срабатывает триггер таймер. По умолчанию также включен триггер обновления базового изображения.

```azurecli
az acr task show --name mytask --registry registry --output table
```

```output
NAME      PLATFORM    STATUS    SOURCE REPOSITORY       TRIGGERS
--------  ----------  --------  -------------------     -----------------
mytask    linux       Enabled                           BASE_IMAGE, TIMER
```

Запуск задачи вручную с [az acr задача запустить,][az-acr-task-run] чтобы убедиться, что она настроена должным образом:

```azurecli
az acr task run --name mytask --registry myregistry
```

Если контейнер работает успешно, выход аналогичен следующему:

```output
Queued a run with ID: cf2a
Waiting for an agent...
2019/06/28 21:03:36 Using acb_vol_2ca23c46-a9ac-4224-b0c6-9fde44eb42d2 as the home volume
2019/06/28 21:03:36 Creating Docker network: acb_default_network, driver: 'bridge'
[...]
2019/06/28 21:03:38 Launching container with name: acb_step_0

Hello from Docker!
This message shows that your installation appears to be working correctly.
[...]
```

После запланированного времени запустите команду [списка задач az acr,][az-acr-task-list-runs] чтобы убедиться, что таймер сработал задачу, как ожидалось:

```azurecli
az acr task list-runs --name mytask --registry myregistry --output table
```

Когда таймер успешен, выход аналогичен следующему:

```output
RUN ID    TASK     PLATFORM    STATUS     TRIGGER    STARTED               DURATION
--------  -------- ----------  ---------  ---------  --------------------  ----------
[...]
cf2b      mytask   linux       Succeeded  Timer      2019-06-28T21:00:23Z  00:00:06
cf2a      mytask   linux       Succeeded  Manual     2019-06-28T20:53:23Z  00:00:06
```

## <a name="manage-timer-triggers"></a>Управление триггерами таймер

Используйте команды [таймер задач az acr][az-acr-task-timer] для управления триггерами таймера для задачи ACR.

### <a name="add-or-update-a-timer-trigger"></a>Добавление или обновление триггера таймера

После создания задачи дополнительно добавьте триггер таймер, используя [команду добавления тайммера.][az-acr-task-timer-add] Следующий пример добавляет таймер триггера *имя timer2* к *mytask* создан ранее. Этот таймер запускает задачу каждый день в 10:30 UTC.

```azurecli
az acr task timer add \
  --name mytask \
  --registry myregistry \
  --timer-name timer2 \
  --schedule "30 10 * * *"
```

Обновите расписание существующего триггера или измените его статус, используя команду [обновления времени номлеров az acr.][az-acr-task-timer-update] Например, обновить триггер под названием *timer2,* чтобы вызвать задачу в 11:30 UTC:

```azurecli
az acr task timer update \
  --name mytask \
  --registry myregistry \
  --timer-name timer2 \
  --schedule "30 11 * * *"
```

### <a name="list-timer-triggers"></a>Срабатыватель таймер списка

Команда [таймера задач az acr][az-acr-task-timer-list] показывает триггеры таймера, настроенные для задачи:

```azurecli
az acr task timer list --name mytask --registry myregistry
```

Выходные данные примера:

```JSON
[
  {
    "name": "timer2",
    "schedule": "30 11 * * *",
    "status": "Enabled"
  },
  {
    "name": "t1",
    "schedule": "0 21 * * *",
    "status": "Enabled"
  }
]
```

### <a name="remove-a-timer-trigger"></a>Удалить триггер таймер

Используйте [таймер задач az acr,][az-acr-task-timer-remove] чтобы удалить спусковой крючок таймер из задачи. Следующий пример удаляет триггер *таймер2* из *mytask:*

```azurecli
az acr task timer remove \
  --name mytask \
  --registry myregistry \
  --timer-name timer2
```

## <a name="cron-expressions"></a>Выражения Крон

ACR Tasks использует библиотеку [NCronTab](https://github.com/atifaziz/NCrontab) для интерпретации выражений cron. Поддерживаемые выражения в задачах ACR имеют пять обязательных полей, разделенных белым пространством:

`{minute} {hour} {day} {month} {day-of-week}`

Часовой пояс, используемый с выражениями cron, является координированным универсальным временем (UTC). Часы в 24-часовом формате.

> [!NOTE]
> Задачи ACR не `{second}` поддерживают `{year}` или поле в выражениях cron. Если вы скопируете выражение cron, используемое в другой системе, не забудьте удалить эти поля, если они используются.

Каждое поле может принимать значение одного из следующих типов:

|Тип  |Пример  |Когда активируется  |
|---------|---------|---------|
|Определенное значение |<nobr>`"5 * * * *"`</nobr>|каждый час в 5 минут после часа|
|Все значения (`*`)|<nobr>`"* 5 * * *"`</nobr>|каждая минута часа, начинающаяся с 5:00 UTC (60 раз в день)|
|Диапазон (оператор `-`)|<nobr>`"0 1-3 * * *"`</nobr>|3 раза в день, в 1:00, 2:00 и 3:00 UTC|
|Набор значений (оператор `,`)|<nobr>`"20,30,40 * * * *"`</nobr>|3 раза в час, в 20 минут, 30 минут и 40 минут после часа|
|Значение интервала (оператор `/`)|<nobr>`"*/10 * * * *"`</nobr>|6 раз в час, в 10 минут, 20 минут и так далее, мимо часа

[!INCLUDE [functions-cron-expressions-months-days](../../includes/functions-cron-expressions-months-days.md)]

### <a name="cron-examples"></a>Примеры Крон

|Пример|Когда активируется  |
|---------|---------|
|`"*/5 * * * *"`|через каждые пять минут|
|`"0 * * * *"`|через каждый час|
|`"0 */2 * * *"`|через каждые 2 часа|
|`"0 9-17 * * *"`|один раз в час с 9:00 до 17:00 UTC|
|`"30 9 * * *"`|в 9:30 UTC каждый день|
|`"30 9 * * 1-5"`|в 9:30 UTC каждый будний день|
|`"30 9 * Jan Mon"`|в 9:30 UTC каждый понедельник января|

## <a name="clean-up-resources"></a>Очистка ресурсов

Чтобы удалить все ресурсы, созданные в этой серии учебников, включая реестр или реестр контейнеров, экземпляр контейнера, хранилище ключей и основной сервис, выдайте следующие команды:

```azurecli
az group delete --resource-group $RES_GROUP
az ad sp delete --id http://$ACR_NAME-pull
```

## <a name="next-steps"></a>Дальнейшие действия

В этом уроке вы узнали, как создавать задачи реестра контейнеров Azure, которые автоматически запускаются таймером. 

Например, при использовании запланированной задачи для очистки репозиториев в реестре [см.](container-registry-auto-purge.md)

Примеры задач, вызванных коммитами исходного кода или обновлениями базового изображения, смотрите другие статьи в [серии учебников ACR Tasks.](container-registry-tutorial-quick-task.md)



<!-- LINKS - External -->
[task-examples]: https://github.com/Azure-Samples/acr-tasks


<!-- LINKS - Internal -->
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-show]: /cli/azure/acr/task#az-acr-task-show
[az-acr-task-list-runs]: /cli/azure/acr/task#az-acr-task-list-runs
[az-acr-task-timer]: /cli/azure/acr/task/timer
[az-acr-task-timer-add]: /cli/azure/acr/task/timer#az-acr-task-timer-add
[az-acr-task-timer-remove]: /cli/azure/acr/task/timer#az-acr-task-timer-remove
[az-acr-task-timer-list]: /cli/azure/acr/task/timer#az-acr-task-timer-list
[az-acr-task-timer-update]: /cli/azure/acr/task/timer#az-acr-task-timer-update
[az-acr-task-run]: /cli/azure/acr/task#az-acr-task-run
[az-acr-task]: /cli/azure/acr/task
[azure-cli-install]: /cli/azure/install-azure-cli
