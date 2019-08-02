---
title: Справка по задачам Реестра контейнеров Azure — YAML
description: Справка по определению задач Реестра контейнеров Azure в YAML, включая свойства задач, типы шагов, свойства шагов и встроенные переменные.
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 07/12/2019
ms.author: danlep
ms.openlocfilehash: 27c38f51104dfb170c59860c96a8e3a86973bb1e
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/30/2019
ms.locfileid: "68638922"
---
# <a name="acr-tasks-reference-yaml"></a>Справка по Задачам Реестра контейнеров Azure: YAML

Определение многошаговой задачи в службе "Задачи ACR" предоставляет ориентированный на контейнеры вычислительный примитив, направленный на сборку, тестирование и исправление контейнеров. В этой статье рассматриваются команды, параметры, свойства и синтаксис для файлов YAML, определяющих многошаговые задачи.

Эта статья содержит справочные сведения о создании YAML-файлов многошаговых задач для службы "Задачи ACR". Общие сведения о задачах ACR см. в [обзоре службы "Задачи ACR"](container-registry-tasks-overview.md).

## <a name="acr-taskyaml-file-format"></a>Формат файла acr-task.yaml

Служба "Задачи ACR" поддерживает объявление многошаговой задачи в стандартном синтаксисе YAML. Шаги задачи определяются в файле YAML. Затем эту задачу можно запустить вручную, передав файл в команду AZ запись [контроля][az-acr-run] доступа. Или используйте файл для создания задачи с помощью команды [AZ контроля][az-acr-task-create] учетных записей, созданной автоматически при фиксации Git или обновлении базового образа. Несмотря на то, что в этой статье `acr-task.yaml` рассматривается как файл, содержащий шаги, служба "Задачи ACR" поддерживает любое допустимое имя файла с [поддерживаемым расширением](#supported-task-filename-extensions).

Примитивами `acr-task.yaml` верхнего уровня являются **свойства задачи**, **типы шагов** и **свойства шага**.

* [Свойства задачи](#task-properties) применяются для всех шагов на протяжении всего выполнения задачи. Существует несколько глобальных свойств задачи, включая следующие.
  * `version`
  * `stepTimeout`
  * `workingDirectory`
* [Типы шагов задачи](#task-step-types) представляют собой типы действий, которые могут быть выполнены в задаче. Имеются три типа шагов:
  * `build`
  * `push`
  * `cmd`
* [Свойства шага задачи](#task-step-properties) — параметры, которые применяются к отдельному шагу. Существует несколько свойств шага, в том числе:
  * `startDelay`
  * `timeout`
  * `when`
  * ...и многие другие.

Ниже приведен пример базового формата файла `acr-task.yaml`, включая некоторые общие свойства шага. Хотя он не является исчерпывающим представлением всех доступных свойств шага или использования типа шага, он предоставляет краткий обзор базового формата файла.

```yml
version: # acr-task.yaml format version.
stepTimeout: # Seconds each step may take.
steps: # A collection of image or container actions.
  - build: # Equivalent to "docker build," but in a multi-tenant environment
  - push: # Push a newly built or retagged image to a registry.
    when: # Step property that defines either parallel or dependent step execution.
  - cmd: # Executes a container, supports specifying an [ENTRYPOINT] and parameters.
    startDelay: # Step property that specifies the number of seconds to wait before starting execution.
```

### <a name="supported-task-filename-extensions"></a>Поддерживаемые расширения имен файлов задач

Для службы "Задачи ACR" зарезервированы несколько расширений имен файлов, включая `.yaml`, которые будут обрабатываться как файл задачи. Любое расширение, *отсутствующее* в следующем списке, служба "Задачи ACR" относит к Dockerfile: .yaml, .yml, .toml, .json, .sh, .bash, .zsh, .ps1, .ps, .cmd, .bat, .ts, .js, .php, .py, .rb, .lua

YAML — единственный формат файлов, который сейчас поддерживает служба "Задачи ACR". Другие расширения имен файлов зарезервированы для возможной будущей поддержки.

## <a name="run-the-sample-tasks"></a>Запуск примеров задач

В следующих разделах этой статьи приводятся ссылки на несколько примеров файлов задач. Примеры задач находятся в общедоступном репозитории GitHub, [Azure-Samples/контроля доступа — задачах][acr-tasks]. Их можно запустить с помощью команды Azure CLI [AZ запись контроля][az-acr-run]доступа. Примеры команд выглядят приблизительно так:

```azurecli
az acr run -f build-push-hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

Форматирование в примерах команд предполагает, что вы уже настроили реестр по умолчанию в Azure CLI, поэтому параметр `--registry` опускается. Чтобы настроить реестр по умолчанию, используйте команду [AZ configure][az-configure] с `--defaults` параметром `acr=REGISTRY_NAME` , который принимает значение.

Например, чтобы настроить Azure CLI с реестром myregistry по умолчанию, выполните следующую команду:

```azurecli
az configure --defaults acr=myregistry
```

## <a name="task-properties"></a>Свойства задачи

Свойства задачи обычно отображаются в верхней части `acr-task.yaml` файла и являются глобальными свойствами, которые применяются во время полного выполнения шагов задачи. Некоторые из этих глобальных свойств могут быть переопределены в рамках отдельного шага.

| Свойство | Тип | Необязательный | Описание | Поддерживается ли переопределение | Значение по умолчанию |
| -------- | ---- | -------- | ----------- | ------------------ | ------------- |
| `version` | строка | Да | Версия файла `acr-task.yaml`, проанализированного службой "Задачи ACR". В то время как служба "Задачи ACR" стремится поддерживать обратную совместимость, это значение позволяет службе поддерживать совместимость в пределах заданной версии. Если значение не указано, по умолчанию используется последняя версия. | Нет | Отсутствуют |
| `stepTimeout` | int (секунды) | Да | Максимальное число секунд выполнения шага. Если свойство задано в задаче, оно устанавливает свойство по умолчанию `timeout` для всех шагов. `timeout` Если свойство задано на шаге, оно переопределяет свойство, предоставленное задачей. | Да | 600 (10 минут) |
| `workingDirectory` | строка | Да | Рабочий каталог контейнера во время выполнения. Если свойство задано в задаче, оно устанавливает свойство по умолчанию `workingDirectory` для всех шагов. Если этот параметр указан на шаге, он переопределяет свойство, предоставленное задачей. | Да | `$HOME` |
| `env` | [строка, строка, ...] | Да |  Массив строк в `key=value` формате, который определяет переменные среды для задачи. Если свойство задано в задаче, оно устанавливает свойство по умолчанию `env` для всех шагов. Если этот параметр указан на шаге, он переопределяет все переменные среды, унаследованные от задачи. | Отсутствуют |
| `secrets` | [секретный, секретный,...] | Да | Массив [секретных](#secret) объектов. | Отсутствуют |
| `networks` | [сеть, сеть,...] | Да | Массив [сетевых](#network) объектов. | Отсутствуют |

### <a name="secret"></a>secret

Секретный объект имеет следующие свойства.

| Свойство | Type | Необязательный | Описание | Значение по умолчанию |
| -------- | ---- | -------- | ----------- | ------- |
| `id` | строка | Нет | Идентификатор секрета. | Отсутствуют |
| `keyvault` | строка | Да | URL-адрес Azure Key Vault секрета. | Отсутствуют |
| `clientID` | строка | Да | Идентификатор клиента [управляемого удостоверения, назначаемого пользователем](container-registry-tasks-authentication-managed-identity.md) для ресурсов Azure. | Отсутствуют |

### <a name="network"></a>Сеть

Объект Network имеет следующие свойства.

| Свойство | Тип | Необязательный | Описание | Значение по умолчанию |
| -------- | ---- | -------- | ----------- | ------- | 
| `name` | строка | Нет | Имя сети. | Отсутствуют |
| `driver` | строка | Да | Драйвер для управления сетью. | Отсутствуют |
| `ipv6` | bool | Да | Включена ли сеть IPv6. | `false` |
| `skipCreation` | bool | Да | Указывает, следует ли пропустить создание сети. | `false` |
| `isDefault` | bool | Да | Является ли сеть сетью по умолчанию, предоставляемой реестром контейнеров Azure | `false` |

## <a name="task-step-types"></a>Типы шагов задач

Служба "Задачи ACR" поддерживает три типа шагов. Каждый тип шага поддерживает несколько свойств, подробно описанных в соответствующих разделах о каждом из типов.

| Тип шага | Описание |
| --------- | ----------- |
| [`build`](#build) | Создает образ контейнера с использованием знакомого синтаксиса `docker build`. |
| [`push`](#push) | Выполняет команду `docker push` для отправки только что созданных или перемаркированных образов в реестр контейнеров. Поддерживается Реестр контейнеров Azure, другие закрытые реестры, а также общедоступный реестр Docker Hub. |
| [`cmd`](#cmd) | Запускает контейнер в качестве команды с параметрами, передаваемыми в `[ENTRYPOINT]` контейнера. Тип шага поддерживает такие параметры, `env`как `detach`, и другие привычные `docker run` параметры команды, позволяющие выполнять модульное и функциональное тестирование с параллельным выполнением контейнера. `cmd` |

## <a name="build"></a>build;

Сборка образа контейнера. Тип шага `build` представляет мультитенантное защищенное средство запуска `docker build` в облаке в качестве примитива первого класса.

### <a name="syntax-build"></a>Синтаксис: build

```yml
version: v1.0.0
steps:
  - [build]: -t [imageName]:[tag] -f [Dockerfile] [context]
    [property]: [value]
```

Тип шага `build` поддерживает параметры, описанные в следующей таблице. Тип шага `build` также поддерживает все параметры сборки из команды [docker build](https://docs.docker.com/engine/reference/commandline/build/), такие как `--build-arg`, для определения переменных во время сборки.

| Параметр | Описание | Необязательный |
| --------- | ----------- | :-------: |
| `-t` &#124; `--image` | Определяет полное значение `image:tag` созданного образа.<br /><br />Так как образы могут использоваться для внутренних проверок задач, например функциональных тестов, не все образы требуют выполнения `push` для отправки в реестр. Но чтобы создать экземпляр образа в пределах выполнения задачи, необходимо указать имя образа для ссылки.<br /><br />В отличие `az acr build`от, выполнение задач записи контроля доступа не обеспечивает поведения принудительной отправки по умолчанию. При использовании службы "Задачи ACR" стандартный сценарий предполагает возможность создания, проверки и последующей отправки образа. Сведения о том, как при необходимости отправлять созданные образы, см. в описании команды [push](#push). | Да |
| `-f` &#124; `--file` | Позволяет указать файл Dockerfile, передаваемый в `docker build`. Если этот параметр не указан, по умолчанию принимается Dockerfile в корне контекста. Чтобы указать Dockerfile, передайте имя файла относительно корня контекста. | Да |
| `context` | Корневой каталог, передаваемый в `docker build`. В качестве корневого каталога каждой задачи задается общий каталог [workingDirectory](#task-step-properties), который включает в себя корень связанного клонированного каталога Git. | Нет |

### <a name="properties-build"></a>Свойства: build

Тип шага `build` поддерживает следующие свойства. Дополнительные сведения об этих свойствах см. в разделе [Свойства шага задачи](#task-step-properties) этой статьи.

| | | |
| -------- | ---- | -------- |
| `detach` | bool | Необязательный |
| `disableWorkingDirectoryOverride` | bool | Необязательный |
| `entryPoint` | строка | Необязательный |
| `env` | [строка, строка, ...] | Необязательный |
| `expose` | [строка, строка, ...] | Необязательный |
| `id` | строка | Необязательный |
| `ignoreErrors` | bool | Необязательный |
| `isolation` | строка | Необязательный |
| `keep` | bool | Необязательный |
| `network` | объект | Необязательный |
| `ports` | [строка, строка, ...] | Необязательный |
| `pull` | bool | Необязательный |
| `repeat` | ssNoversion | Необязательный |
| `retries` | ssNoversion | Необязательный |
| `retryDelay` | int (секунды) | Необязательный |
| `secret` | объект | Необязательный |
| `startDelay` | int (секунды) | Необязательный |
| `timeout` | int (секунды) | Необязательный |
| `when` | [строка, строка, ...] | Необязательный |
| `workingDirectory` | строка | Необязательный |

### <a name="examples-build"></a>Примеры: build

#### <a name="build-image---context-in-root"></a>Сборка образа — контекст в корне

```azurecli
az acr run -f build-hello-world.yaml https://github.com/AzureCR/acr-tasks-sample.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/build-hello-world.yaml -->
[!code-yml[task](~/acr-tasks/build-hello-world.yaml)]

#### <a name="build-image---context-in-subdirectory"></a>Сборка образа — контекст в подкаталоге

```yml
version: v1.0.0
steps:
  - build: -t {{.Run.Registry}}/hello-world -f hello-world.dockerfile ./subDirectory
```

## <a name="push"></a>push

Отправляет командой один или несколько из только что созданных или перемаркированных образов в реестр контейнеров. Команда push поддерживается для таких закрытых реестров, как Реестр контейнеров Azure, а также общедоступный реестр Docker Hub.

### <a name="syntax-push"></a>Синтаксис: push

Тип шага `push` поддерживает коллекцию образов. Синтаксис коллекции YAML поддерживает встроенные и вложенные форматы. Отправка одного образа обычно представляется с помощью встроенного синтаксиса:

```yml
version: v1.0.0
steps:
  # Inline YAML collection syntax
  - push: ["{{.Run.Registry}}/hello-world:{{.Run.ID}}"]
```

Для повышения удобочитаемости используйте вложенный синтаксис при отправке нескольких образов:

```yml
version: v1.0.0
steps:
  # Nested YAML collection syntax
  - push:
    - {{.Run.Registry}}/hello-world:{{.Run.ID}}
    - {{.Run.Registry}}/hello-world:latest
```

### <a name="properties-push"></a>Свойства: push

Тип шага `push` поддерживает следующие свойства. Дополнительные сведения об этих свойствах см. в разделе [Свойства шага задачи](#task-step-properties) этой статьи.

| | | |
| -------- | ---- | -------- |
| `env` | [строка, строка, ...] | Необязательный |
| `id` | строка | Необязательный |
| `ignoreErrors` | bool | Необязательный |
| `startDelay` | int (секунды) | Необязательный |
| `timeout` | int (секунды) | Необязательный |
| `when` | [строка, строка, ...] | Необязательный |

### <a name="examples-push"></a>Примеры: push

#### <a name="push-multiple-images"></a>Отправка нескольких образов командой push

```azurecli
az acr run -f build-push-hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/build-push-hello-world.yaml -->
[!code-yml[task](~/acr-tasks/build-push-hello-world.yaml)]

#### <a name="build-push-and-run"></a>Сборка, отправка и запуск

```azurecli
az acr run -f build-run-hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/build-run-hello-world.yaml -->
[!code-yml[task](~/acr-tasks/build-run-hello-world.yaml)]

## <a name="cmd"></a>cmd.

Тип шага `cmd` запускает контейнер.

### <a name="syntax-cmd"></a>Синтаксис: cmd

```yml
version: v1.0.0
steps:
  - [cmd]: [containerImage]:[tag (optional)] [cmdParameters to the image]
```

### <a name="properties-cmd"></a>Свойства: cmd

Тип шага `cmd` поддерживает следующие свойства:

| | | |
| -------- | ---- | -------- |
| `detach` | bool | Необязательный |
| `disableWorkingDirectoryOverride` | bool | Необязательный |
| `entryPoint` | строка | Необязательный |
| `env` | [строка, строка, ...] | Необязательный |
| `expose` | [строка, строка, ...] | Необязательный |
| `id` | строка | Необязательный |
| `ignoreErrors` | bool | Необязательный |
| `isolation` | строка | Необязательный |
| `keep` | bool | Необязательный |
| `network` | объект | Необязательный |
| `ports` | [строка, строка, ...] | Необязательный |
| `pull` | bool | Необязательный |
| `repeat` | ssNoversion | Необязательный |
| `retries` | ssNoversion | Необязательный |
| `retryDelay` | int (секунды) | Необязательный |
| `secret` | объект | Необязательный |
| `startDelay` | int (секунды) | Необязательный |
| `timeout` | int (секунды) | Необязательный |
| `when` | [строка, строка, ...] | Необязательный |
| `workingDirectory` | строка | Необязательный |

Сведения об этих свойствах см. в разделе [Свойства шага задачи](#task-step-properties) этой статьи.

### <a name="examples-cmd"></a>Примеры: cmd

#### <a name="run-hello-world-image"></a>Запуск образа hello-world

Эта команда выполняет файл задачи `hello-world.yaml`, который ссылается на образ [hello-world](https://hub.docker.com/_/hello-world/) в Docker Hub.

```azurecli
az acr run -f hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.yaml -->
[!code-yml[task](~/acr-tasks/hello-world.yaml)]

#### <a name="run-bash-image-and-echo-hello-world"></a>Запуск образа bash и вывод командой echo hello world

Эта команда выполняет файл задачи `bash-echo.yaml`, который ссылается на образ [bash](https://hub.docker.com/_/bash/) в Docker Hub.

```azurecli
az acr run -f bash-echo.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/bash-echo.yaml -->
[!code-yml[task](~/acr-tasks/bash-echo.yaml)]

#### <a name="run-specific-bash-image-tag"></a>Тег для запуска определенного образа bash

Чтобы запустить конкретную версию образа, укажите тег в команде `cmd`.

Эта команда выполняет файл задачи `bash-echo-3.yaml`, который ссылается на образ [bash:3.0](https://hub.docker.com/_/bash/) в Docker Hub.

```azurecli
az acr run -f bash-echo-3.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/bash-echo-3.yaml -->
[!code-yml[task](~/acr-tasks/bash-echo-3.yaml)]

#### <a name="run-custom-images"></a>Запуск пользовательских образов

Тип шага `cmd` ссылается на образы с использованием стандартного формата `docker run`. Предполагается, что образы, которые не начинаются с указания реестра, поступают из docker.io. Предыдущий пример может быть также представлен следующим образом:

```yml
version: v1.0.0
steps:
  - cmd: docker.io/bash:3.0 echo hello world
```

Использование стандартного `docker run` `cmd` соглашения о ссылках на образы позволяет запускать образы из любого частного реестра или общедоступного центра DOCKER. Если вы ссылаетесь на образы в том же реестре, в котором выполняется задача ACR, не нужно указывать учетные данные реестра.

* Запуск образа из реестра контейнеров Azure

    Замените `[myregistry]` именем реестра:

    ```yml
    version: v1.0.0
    steps:
        - cmd: [myregistry].azurecr.io/bash:3.0 echo hello world
    ```

* Обобщение ссылки на реестр с помощью переменной Run

    Вместо непосредственного указания имени реестра в файле `acr-task.yaml` можно улучшить его переносимость с помощью [переменной Run](#run-variables). Переменная `Run.Registry` развертывается во время выполнения как имя реестра, в котором выполняется задача.

    Чтобы обобщить предыдущую задачу для работы в любом реестре контейнеров Azure, примените ссылку на переменную [Run.Registry](#runregistry) в имени образа:

    ```yml
    version: v1.0.0
    steps:
      - cmd: {{.Run.Registry}}/bash:3.0 echo hello world
    ```

## <a name="task-step-properties"></a>Свойства шага задачи

Каждый тип шага поддерживает несколько свойств, подходящих для его типа. В следующей таблице определены все доступные свойства шагов. Не все типы шагов поддерживают все свойства. Чтобы узнать, какие из этих свойств доступны для каждого типа шага, см. разделы справки по типам шагов [cmd](#cmd), [build](#build) и [push](#push).

| Свойство | Тип | Необязательный | Описание | Значение по умолчанию |
| -------- | ---- | -------- | ----------- | ------- |
| `detach` | bool | Да | Определяет, следует ли отсоединить контейнер при запуске. | `false` |
| `disableWorkingDirectoryOverride` | bool | Да | Следует ли отключить `workingDirectory` функцию переопределения. Используйте его в сочетании с `workingDirectory` , чтобы получить полный контроль над рабочим каталогом контейнера. | `false` |
| `entryPoint` | строка | Да | Переопределяет `[ENTRYPOINT]` контейнера шага. | Отсутствуют |
| `env` | [строка, строка, ...] | Да | Массив строк в формате `key=value`, определяющий переменные среды для шага. | Отсутствуют |
| `expose` | [строка, строка, ...] | Да | Массив портов, доступных из контейнера. |  Отсутствуют |
| [`id`](#example-id) | строка | Да | Однозначно определяет шаг в рамках задачи. Другие шаги в задаче могут ссылаться на `id` шага, например для проверки зависимостей с помощью `when`.<br /><br />`id` также является именем запущенного контейнера. Процессы, запущенные в других контейнерах в задаче, могут ссылаться на `id` в качестве имени его узла DNS, или же для доступа к нему с помощью журналов docker [id], например. | `acb_step_%d`, где `%d` — это Отсчитываемый от нуля индекс шага сверху вниз в файле YAML |
| `ignoreErrors` | bool | Да | Помечать ли шаг как успешный независимо от того, произошла ли ошибка во время выполнения контейнера. | `false` |
| `isolation` | строка | Да | Уровень изоляции контейнера. | `default` |
| `keep` | bool | Да | Следует ли сохранить контейнер этого шага после выполнения. | `false` |
| `network` | объект | Да | Определяет сеть, в которой выполняется контейнер. | Отсутствуют |
| `ports` | [строка, строка, ...] | Да | Массив портов, опубликованных из контейнера на узле. |  Отсутствуют |
| `pull` | bool | Да | Указывает, следует ли принудительно запрашивать контейнер перед его выполнением, чтобы предотвратить любое поведение кэширования. | `false` |
| `privileged` | bool | Да | Следует ли запускать контейнер в привилегированном режиме. | `false` |
| `repeat` | ssNoversion | Да | Число повторных попыток для повторного выполнения контейнера. | 0 |
| `retries` | ssNoversion | Да | Число повторных попыток при сбое выполнения контейнера. Повторная попытка выполняется только в том случае, если код выхода контейнера не равен нулю. | 0 |
| `retryDelay` | int (секунды) | Да | Задержка в секундах между повторными попытками выполнения контейнера. | 0 |
| `secret` | объект | Да | Определяет Azure Key Vault секретного или [управляемого удостоверения для ресурсов Azure](container-registry-tasks-authentication-managed-identity.md). | Отсутствуют |
| `startDelay` | int (секунды) | Да | Количество секунд, в течение которых откладывается выполнение контейнера. | 0 |
| `timeout` | int (секунды) | Да | Максимальное число секунд, в течение которого может выполняться шаг перед завершением. | 600 |
| [`when`](#example-when) | [строка, строка, ...] | Да | Настраивает зависимость шага от одного или нескольких других шагов в пределах задачи. | Отсутствуют |
| `user` | строка | Да | Имя пользователя или UID контейнера | Отсутствуют |
| `workingDirectory` | строка | Да | Задает рабочий каталог для шага. По умолчанию служба "Задачи ACR" создает корневой каталог в качестве рабочего каталога. Но если сборка включает несколько шагов, для предыдущих и последующих шагов можно использовать общие артефакты, указав один и тот же рабочий каталог. | `$HOME` |

### <a name="examples-task-step-properties"></a>Примеры: Свойства шага задачи

#### <a name="example-id"></a>Пример: id

Создайте два образа, используя экземпляры образа функционального теста. Каждый шаг идентифицируется уникальным `id`, на который ссылаются другие шаги задачи в свойстве `when`.

```azurecli
az acr run -f when-parallel-dependent.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-parallel-dependent.yaml -->
[!code-yml[task](~/acr-tasks/when-parallel-dependent.yaml)]

#### <a name="example-when"></a>Пример: when

Свойство `when` указывает зависимость шага от других шагов в пределах задачи. Оно поддерживает два значения параметров:

* `when: ["-"]` — указывает отсутствие зависимости от других шагов. Шаг с указанием `when: ["-"]` начнет выполняться немедленно и обеспечивает параллельное выполнение шагов.
* `when: ["id1", "id2"]` — указывает, что шаг зависит от шагов с `id` id1 и `id` id2. Этот шаг не будет выполнен до тех пор, пока не будут завершены оба шага id1 и id2.

Если свойство `when` не указано в шаге, этот шаг зависит от завершения предыдущего шага в файле `acr-task.yaml`.

Последовательное выполнение шагов без свойства `when`:

```azurecli
az acr run -f when-sequential-default.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-sequential-default.yaml -->
[!code-yml[task](~/acr-tasks/when-sequential-default.yaml)]

Последовательное выполнение шагов со свойством `when`:

```azurecli
az acr run -f when-sequential-id.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-sequential-id.yaml -->
[!code-yml[task](~/acr-tasks/when-sequential-id.yaml)]

Сборка параллельных образов:

```azurecli
az acr run -f when-parallel.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-parallel.yaml -->
[!code-yml[task](~/acr-tasks/when-parallel.yaml)]

Параллельное создание образов и тестирование зависимостей:

```azurecli
az acr run -f when-parallel-dependent.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-parallel-dependent.yaml -->
[!code-yml[task](~/acr-tasks/when-parallel-dependent.yaml)]

## <a name="run-variables"></a>Переменные run

Служба "Задачи ACR" содержит набор переменных по умолчанию, доступных для шагов задачи при их выполнении. К этим переменным можно получить доступ с использованием формата `{{.Run.VariableName}}`, где `VariableName` — одна из следующих переменных:

* `Run.ID`
* `Run.Registry`
* `Run.Date`
* `Run.Commit`
* `Run.Branch`

### <a name="runid"></a>Run.ID

Каждый запуск с помощью `az acr run` или выполнение по триггеру задач, созданных с помощью `az acr task create`, обладает уникальным идентификатором. Идентификатор представляет выполняющуюся в текущий момент команду Run.

Обычно используется для уникальной маркировки образа:

```yml
version: v1.0.0
steps:
    - build: -t {{.Run.Registry}}/hello-world:{{.Run.ID}} .
```

### <a name="runregistry"></a>Run.Registry

Полное имя сервера реестра. Обычно используется для универсальной ссылки на реестр, в котором выполняется задача.

```yml
version: v1.0.0
steps:
  - build: -t {{.Run.Registry}}/hello-world:{{.Run.ID}} .
```

### <a name="rundate"></a>Run.Date

Текущее время в формате UTC, когда начался запуск.

### <a name="runcommit"></a>Выполнить. Commit

Для задачи, активируемой фиксацией в репозитории GitHub, идентификатор фиксации.

### <a name="runbranch"></a>Запустить. Branch

Для задачи, активируемой фиксацией в репозитории GitHub, это имя ветви.

## <a name="next-steps"></a>Следующие шаги

Обзор многошаговых задач см. в статье [Run multi-step build, test, and patch tasks in ACR Tasks](container-registry-tasks-multi-step.md) (Выполнение многошаговых задач сборки, тестирования и исправления в решении "Задачи ACR").

Сведения об одношаговых сборках см. в статье [Автоматизация установки исправлений ОС и платформы с помощью службы "Задачи ACR"](container-registry-tasks-overview.md).

<!-- IMAGES -->

<!-- LINKS - External -->
[acr-tasks]: https://github.com/Azure-Samples/acr-tasks

<!-- LINKS - Internal -->
[az-acr-run]: /cli/azure/acr#az-acr-run
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-configure]: /cli/azure/reference-index#az-configure
