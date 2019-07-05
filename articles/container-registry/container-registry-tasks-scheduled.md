---
title: Планирование задач реестра контейнеров Azure
description: Устанавливать таймеры, чтобы запустить задачу реестр контейнеров Azure по определенному расписанию.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 06/27/2019
ms.author: danlep
ms.openlocfilehash: a1123a30025f9be6e994e69703f5ee1aa05d1b49
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2019
ms.locfileid: "67509707"
---
# <a name="run-an-acr-task-on-a-defined-schedule"></a>Запустить задачу ACR по заданному расписанию

В этой статье показано, как запустить [задач ACR](container-registry-tasks-overview.md) по расписанию. Планирования задач путем настройки одного или нескольких *триггеров с таймерами*. 

Планирование задания используется в сценариях, следующим образом:

* Запуск контейнера рабочей нагрузки для операции запланированного обслуживания. Например запустите контейнерного приложения для удаления ненужных образов из реестра.
* Запустите набор тестов в рабочий образ во время рабочего дня в процессе мониторинга активных сайтов.

Чтобы выполнить примеры в этой статье, можно использовать Azure Cloud Shell или локальной установки Azure CLI. Если вы хотите использовать его локально, версия 2.0.68 или более поздней. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0][azure-cli-install].


## <a name="about-scheduling-a-task"></a>О планировании задачи

* **Триггер с помощью выражений cron** -использует триггер с таймером для задачи *выражение cron*. Выражение — это строка в пять полей, указав минуту, час, день, месяц и день недели, чтобы запустить задачу. Поддерживаются частоты до одного раза в минуту. 

  Например, выражение `"0 12 * * Mon-Fri"` запускает задачу в полдень в формате UTC на каждый день недели. См. в разделе [сведения](#cron-expressions) далее в этой статье.
* **Несколько триггеров с таймерами** -Добавление нескольких таймеров в задачу может, до тех пор, пока расписаний отличаются. 
    * Укажите несколько триггеров таймера при создании задачи или добавить их позднее.
    * Имя (необязательно) триггеры для упрощенного управления, или предоставлять имена по умолчанию триггер задачи ACR.
    * Если расписание таймера перекрываются одновременно, ACR задачи запускает задачу в запланированное время для каждого таймера. 
* **Другие триггеры заданий** -в задаче, активируемую по таймеру, вы можете также включить триггеры на основе [фиксации кода источника](container-registry-tutorial-build-task.md) или [базового образа обновления](container-registry-tutorial-base-image-update.md). Как и другие задачи записи контроля доступа, вы также можете [вручную активировать][az-acr-task-run] запланированной задачи.

## <a name="create-a-task-with-a-timer-trigger"></a>Создать задачу с помощью триггера таймера

При создании задачи с помощью [az acr задача создание][az-acr-task-create] команды, при необходимости можно добавить триггер таймера. Добавление `--schedule` параметр и передайте выражение cron для таймера. 

В качестве простого примера, приведенная ниже команда запуска триггеров `hello-world` образ из Docker Hub каждый день в 21:00 UTC. Задача выполняется без контекста исходного кода.

```azurecli
az acr task create \
  --name mytask \
  --registry myregistry \
  --context /dev/null \
  --cmd hello-world \
  --schedule "0 21 * * *"
```

Запустите [az acr задач show][az-acr-task-show] команду, чтобы увидеть, что настроен триггер с таймером. По умолчанию также включен триггер обновления базового образа.

```console
$ az acr task show --name mytask --registry registry --output table
NAME      PLATFORM    STATUS    SOURCE REPOSITORY       TRIGGERS
--------  ----------  --------  -------------------     -----------------
mytask    linux       Enabled                           BASE_IMAGE, TIMER
```

Запустить задачу вручную с помощью [выполнения задачи az acr][az-acr-task-run] чтобы убедиться, что он настроен правильно:

```azurecli
az acr task run --name mytask --registry myregistry
```

Если контейнер выполняется успешно, выходные данные следующего вида:

```console
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

После запланированного времени запуска [запуске az acr задач списка][az-acr-task-list-runs] команду, чтобы убедиться, что таймер запускается задача должным образом:

```azurecli
az acr task list runs --name mytask --registry myregistry --output table
``` 

Когда таймер прошла успешно, результат аналогичен приведенному ниже:

```console
RUN ID    TASK     PLATFORM    STATUS     TRIGGER    STARTED               DURATION
--------  -------- ----------  ---------  ---------  --------------------  ----------
[...]
cf2b      mytask   linux       Succeeded  Timer      2019-06-28T21:00:23Z  00:00:06
cf2a      mytask   linux       Succeeded  Manual     2019-06-28T20:53:23Z  00:00:06
```
            
## <a name="manage-timer-triggers"></a>Управление триггеров с таймерами

Используйте [az acr задачи таймера][az-acr-task-timer] команды для управления триггерами таймера для задачи записи контроля доступа.

### <a name="add-or-update-a-timer-trigger"></a>Добавить или обновить триггер таймера

После создания задачи, при необходимости добавить триггер таймера с помощью [az acr задачи таймера добавьте][az-acr-task-timer-add] команды. В следующем примере добавляется имя триггера таймера *timer2* для *mytask* созданную ранее. Этот таймер запускает задачу каждый день в 9:00 UTC.

```azurecli
az acr task timer add \
  --name mytask \
  --registry myregistry \
  --timer-name timer2 \
  --schedule "30 10 * * *"
```

Обновить расписание существующего триггера или изменить его состояние с помощью [задачи таймера az acr update][az-acr-task-timer-update] команды. Например, обновлении триггера с именем *timer2* Чтобы запустить задачу, в 11:30 по UTC:

```azurecli
az acr task timer update \
  --name mytask \
  --registry myregistry \
  --timer-name timer2 \
  --schedule "30 11 * * *"
```

### <a name="list-timer-triggers"></a>Список триггеров с таймерами

[Задачи таймера az acr list][az-acr-task-timer-list] команда показывает триггеры таймера для задачи:

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

### <a name="remove-a-timer-trigger"></a>Удаление триггера таймера 

Используйте [az acr задачи таймера remove][az-acr-task-timer-remove] команду, чтобы удалить триггер таймера из задачи. В следующем примере удаляется *timer2* триггер из *mytask*:

```azurecli
az acr task timer remove \
  --name mytask \
  --registry myregistry \
  --timer-name timer2
```

## <a name="cron-expressions"></a>Выражения cron

Задачи ACR использует [NCronTab](https://github.com/atifaziz/NCrontab) библиотеки для интерпретации выражений cron. Поддерживаемые выражения в задачах ACR имеют пять обязательных полей, разделенных пробелами:

`{minute} {hour} {day} {month} {day-of-week}`

Часовой пояс, используемый с выражениями cron — время в формате UTC. Часы находятся в 24-часовом формате.

> [!NOTE]
> Не поддерживает задачи ACR `{second}` или `{year}` в выражениях cron. При копировании выражение cron, используемое в другую систему, не забудьте удалить эти поля, если они используются.

Каждое поле может принимать значение одного из следующих типов:

|type  |Пример  |Когда активируется  |
|---------|---------|---------|
|Определенное значение |<nobr>"5 * * * *"</nobr>|Каждый час в 5 минут после наступления|
|Все значения (`*`)|<nobr>"* 5 * * *"</nobr>|каждую минуту 5:00 UTC начала часа (60 раз в день)|
|Диапазон (оператор `-`)|<nobr>"0 1-3 * * *"</nobr>|3 раза в день в 1:00 2:00 и 3:00 UTC|  
|Набор значений (оператор `,`)|<nobr>"20,30,40 * * * *"</nobr>|3 раза в час, в 20 минут, 30 минут и 40 минут после наступления|
|Значение интервала (оператор `/`)|<nobr>"*/10 * * * *"</nobr>|6 раз в час на 10 минут, 20 минут и т. д., за последний час

[!INCLUDE [functions-cron-expressions-months-days](../../includes/functions-cron-expressions-months-days.md)]

### <a name="cron-examples"></a>Примеры cron

|Пример|Когда активируется  |
|---------|---------|
|`"*/5 * * * *"`|через каждые пять минут|
|`"0 * * * *"`|через каждый час|
|`"0 */2 * * *"`|через каждые 2 часа|
|`"0 9-17 * * *"`|Каждый час с 9:00 в 17:00 UTC|
|`"30 9 * * *"`|в 9:30 каждый день в формате UTC|
|`"30 9 * * 1-5"`|в 9:30 каждый день в формате UTC|
|`"30 9 * Jan Mon"`|в 9:30 каждый понедельник в январе в формате UTC|


## <a name="next-steps"></a>Дальнейшие действия

Примеры задач, активируемые при фиксации кода источника или основной образ обновлений, советуем [серии руководств задачи ACR](container-registry-tutorial-quick-task.md).



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