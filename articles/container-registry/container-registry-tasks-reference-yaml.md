---
title: Справка по задачам Реестра контейнеров Azure — YAML
description: Справка по определению задач Реестра контейнеров Azure в YAML, включая свойства задач, типы шагов, свойства шагов и встроенные переменные.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 03/28/2019
ms.author: danlep
ms.openlocfilehash: b2398e7db7ed91dee8d85c0c50058bb15b9f4c7e
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/18/2019
ms.locfileid: "58894138"
---
# <a name="acr-tasks-reference-yaml"></a>Справка по Задачам Реестра контейнеров Azure: YAML

Определение многошаговой задачи в службе "Задачи ACR" предоставляет ориентированный на контейнеры вычислительный примитив, направленный на сборку, тестирование и исправление контейнеров. В этой статье рассматриваются команды, параметры, свойства и синтаксис для файлов YAML, определяющие многошаговых задач.

Эта статья содержит справочные сведения о создании YAML-файлов многошаговых задач для службы "Задачи ACR". Общие сведения о задачах ACR см. в [обзоре службы "Задачи ACR"](container-registry-tasks-overview.md).

## <a name="acr-taskyaml-file-format"></a>Формат файла acr-task.yaml

Служба "Задачи ACR" поддерживает объявление многошаговой задачи в стандартном синтаксисе YAML. Шаги задания определяются в yaml-файл. Затем задачу можно запустить вручную, передав файл [az acr запуска] [ az-acr-run] команды. Или использовать файл, чтобы создать задачу с [az acr задача создание] [ az-acr-task-create] , запускается автоматически при обновлении Git commit или базового образа. Несмотря на то, что в этой статье `acr-task.yaml` рассматривается как файл, содержащий шаги, служба "Задачи ACR" поддерживает любое допустимое имя файла с [поддерживаемым расширением](#supported-task-filename-extensions).

Примитивами `acr-task.yaml` верхнего уровня являются **свойства задачи**, **типы шагов** и **свойства шага**.

* [Свойства задачи](#task-properties) применяются для всех шагов на протяжении всего выполнения задачи. Существует несколько свойств глобальной задачи, в том числе:
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

В следующих разделах этой статьи приводятся ссылки на несколько примеров файлов задач. Примеры задач расположены в общедоступном репозитории GitHub [Azure-Samples/acr-tasks][acr-tasks]. Их можно запустить с помощью команды [az acr run][az-acr-run] в Azure CLI. Примеры команд выглядят приблизительно так:

```azurecli
az acr run -f build-push-hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

Форматирование в примерах команд предполагает, что вы уже настроили реестр по умолчанию в Azure CLI, поэтому параметр `--registry` опускается. Чтобы настроить реестр по умолчанию, воспользуйтесь командой [az configure][az-configure] с параметром `--defaults`, который принимает значение `acr=REGISTRY_NAME`.

Например, чтобы настроить Azure CLI с реестром myregistry по умолчанию, выполните следующую команду:

```azurecli
az configure --defaults acr=myregistry
```

## <a name="task-properties"></a>Свойства задачи

Свойства задачи обычно отображаются в верхней части `acr-task.yaml` , являются file и глобальных свойств, которые применяются во время полного выполнения шагов задач. Некоторые из этих глобальных свойств могут быть переопределены в рамках отдельного шага.

| Свойство | type | Необязательно | ОПИСАНИЕ | Поддерживается ли переопределение | Значение по умолчанию |
| -------- | ---- | -------- | ----------- | ------------------ | ------------- |
| `version` | строка | Yes | Версия файла `acr-task.yaml`, проанализированного службой "Задачи ACR". В то время как служба "Задачи ACR" стремится поддерживать обратную совместимость, это значение позволяет службе поддерживать совместимость в пределах заданной версии. Если этот атрибут не задан, по умолчанию до последней версии. | Нет  | Нет |
| `stepTimeout` | int (секунды) | Yes | Максимальное число секунд выполнения шага. Если свойство задается в задаче, он задает значение по умолчанию `timeout` свойства всех шагов. Если `timeout` свойство указано на шаге, оно переопределяет свойство, предоставленные задачей. | Yes | 600 (10 минут) |
| `workingDirectory` | строка | Yes | Рабочий каталог контейнера во время выполнения. Если свойство задается в задаче, он задает значение по умолчанию `workingDirectory` свойства всех шагов. Если указано в шаге, оно переопределяет свойство, предоставленные задачей. | Yes | `$HOME` |
| `env` | [строка, строка, ...] | Yes |  Массив строк в `key=value` формат, определение переменных среды для задачи. Если свойство задается в задаче, он задает значение по умолчанию `env` свойства всех шагов. Если указано в шаге, он переопределяет все переменные среды, унаследованные от задачи. | Нет |
| `secrets` | [секрета, секрет,...] | Yes | Массив [секрет](#secret) объектов. | Нет |
| `networks` | [, сети,...] | Yes | Массив [сети](#network) объектов. | Нет |

### <a name="secret"></a>secret

Объект секрета имеет следующие свойства.

| Свойство | type | Необязательно | ОПИСАНИЕ | Значение по умолчанию |
| -------- | ---- | -------- | ----------- | ------- |
| `id` | строка | Нет  | Идентификатор секрета. | Нет |
| `akv` | строка | Yes | Azure Key Vault (AKV) URL-адрес секрета. | Нет |
| `clientID` | строка | Yes | Идентификатор клиента для назначаемого пользователем управляемого удостоверения для ресурсов Azure. | Нет |

### <a name="network"></a>Сеть

Объект имеет следующие свойства.

| Свойство | type | Необязательно | ОПИСАНИЕ | Значение по умолчанию |
| -------- | ---- | -------- | ----------- | ------- | 
| `name` | строка | Нет  | Имя сети. | Нет |
| `driver` | строка | Yes | Драйвер для управления сетью. | Нет |
| `ipv6` | bool | Yes | Указывает, включена ли сетей IPv6. | `false` |
| `skipCreation` | bool | Yes | Нужно ли пропустить создание сети. | `false` |
| `isDefault` | bool | Yes | Является ли сеть в сеть по умолчанию, предоставляемые с помощью реестра контейнеров Azure | `false` |

## <a name="task-step-types"></a>Типы шагов задач

Служба "Задачи ACR" поддерживает три типа шагов. Каждый тип шага поддерживает несколько свойств, подробно описанных в соответствующих разделах о каждом из типов.

| Тип шага | ОПИСАНИЕ |
| --------- | ----------- |
| [`build`](#build) | Создает образ контейнера с использованием знакомого синтаксиса `docker build`. |
| [`push`](#push) | Выполняет команду `docker push` для отправки только что созданных или перемаркированных образов в реестр контейнеров. Поддерживается Реестр контейнеров Azure, другие закрытые реестры, а также общедоступный реестр Docker Hub. |
| [`cmd`](#cmd) | Запускает контейнер в качестве команды с параметрами, передаваемыми в `[ENTRYPOINT]` контейнера. `cmd` Тип шага поддерживает параметры, например `env`, `detach`и другие знакомые `docker run` параметры команд, включение модульного и функционального тестирования с выполнением параллельных контейнеров. |

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

| Параметр | ОПИСАНИЕ | Необязательно |
| --------- | ----------- | :-------: |
| `-t` &#124; `--image` | Определяет полное значение `image:tag` созданного образа.<br /><br />Так как образы могут использоваться для внутренних проверок задач, например функциональных тестов, не все образы требуют выполнения `push` для отправки в реестр. Но чтобы создать экземпляр образа в пределах выполнения задачи, необходимо указать имя образа для ссылки.<br /><br />В отличие от `az acr build`, выполнение задач для записи контроля доступа не предоставляет поведение по умолчанию push. При использовании службы "Задачи ACR" стандартный сценарий предполагает возможность создания, проверки и последующей отправки образа. Сведения о том, как при необходимости отправлять созданные образы, см. в описании команды [push](#push). | Yes |
| `-f` &#124; `--file` | Позволяет указать файл Dockerfile, передаваемый в `docker build`. Если этот параметр не указан, по умолчанию принимается Dockerfile в корне контекста. Чтобы указать файл Dockerfile, передайте имя файла относительно корня контекста. | Yes |
| `context` | Корневой каталог, передаваемый в `docker build`. В качестве корневого каталога каждой задачи задается общий каталог [workingDirectory](#task-step-properties), который включает в себя корень связанного клонированного каталога Git. | Нет  |

### <a name="properties-build"></a>Свойства: build

Тип шага `build` поддерживает следующие свойства. Сведения об этих свойств в [Свойства шага задачи](#task-step-properties) этой статьи.

| | | |
| -------- | ---- | -------- |
| `detach` | bool | Необязательно |
| `disableWorkingDirectoryOverride` | bool | Необязательно |
| `entryPoint` | строка | Необязательно |
| `env` | [строка, строка, ...] | Необязательно |
| `expose` | [строка, строка, ...] | Необязательно |
| `id` | строка | Необязательно |
| `ignoreErrors` | bool | Необязательно |
| `isolation` | строка | Необязательно |
| `keep` | bool | Необязательно |
| `network` | object | Необязательно |
| `ports` | [строка, строка, ...] | Необязательно |
| `pull` | bool | Необязательно |
| `repeat` | int | Необязательно |
| `retries` | int | Необязательно |
| `retryDelay` | int (секунды) | Необязательно |
| `secret` | object | Необязательно |
| `startDelay` | int (секунды) | Необязательно |
| `timeout` | int (секунды) | Необязательно |
| `when` | [строка, строка, ...] | Необязательно |
| `workingDirectory` | строка | Необязательно |

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

Тип шага `push` поддерживает следующие свойства. Сведения об этих свойств в [Свойства шага задачи](#task-step-properties) этой статьи.

| | | |
| -------- | ---- | -------- |
| `env` | [строка, строка, ...] | Необязательно |
| `id` | строка | Необязательно |
| `ignoreErrors` | bool | Необязательно |
| `startDelay` | int (секунды) | Необязательно |
| `timeout` | int (секунды) | Необязательно |
| `when` | [строка, строка, ...] | Необязательно |

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
| `detach` | bool | Необязательно |
| `disableWorkingDirectoryOverride` | bool | Необязательно |
| `entryPoint` | строка | Необязательно |
| `env` | [строка, строка, ...] | Необязательно |
| `expose` | [строка, строка, ...] | Необязательно |
| `id` | строка | Необязательно |
| `ignoreErrors` | bool | Необязательно |
| `isolation` | строка | Необязательно |
| `keep` | bool | Необязательно |
| `network` | object | Необязательно |
| `ports` | [строка, строка, ...] | Необязательно |
| `pull` | bool | Необязательно |
| `repeat` | int | Необязательно |
| `retries` | int | Необязательно |
| `retryDelay` | int (секунды) | Необязательно |
| `secret` | object | Необязательно |
| `startDelay` | int (секунды) | Необязательно |
| `timeout` | int (секунды) | Необязательно |
| `when` | [строка, строка, ...] | Необязательно |
| `workingDirectory` | строка | Необязательно |

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

С помощью стандарта `docker run` изображения Справочник по соглашению `cmd` можно запускать образы из любого частного реестра или общедоступный центр Docker Hub. Если вы ссылаетесь на образы в том же реестре, в котором выполняется задача ACR, не нужно указывать учетные данные реестра.

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

| Свойство | type | Необязательно | ОПИСАНИЕ | Значение по умолчанию |
| -------- | ---- | -------- | ----------- | ------- |
| `detach` | bool | Yes | Определяет, следует ли отсоединить контейнер при запуске. | `false` |
| `disableWorkingDirectoryOverride` | bool | Yes | Следует ли отключить `workingDirectory` переопределение функциональности. Использовать в сочетании с `workingDirectory` иметь полный контроль над контейнера рабочий каталог. | `false` |
| `entryPoint` | строка | Yes | Переопределяет `[ENTRYPOINT]` контейнера шага. | Нет |
| `env` | [строка, строка, ...] | Yes | Массив строк в формате `key=value`, определяющий переменные среды для шага. | Нет |
| `expose` | [строка, строка, ...] | Yes | Массив порты, которые доступны из контейнера. |  Нет |
| [`id`](#example-id) | строка | Yes | Однозначно определяет шаг в рамках задачи. Другие шаги в задаче могут ссылаться на `id` шага, например для проверки зависимостей с помощью `when`.<br /><br />`id` также является именем запущенного контейнера. Процессы, запущенные в других контейнерах в задаче, могут ссылаться на `id` в качестве имени его узла DNS, или же для доступа к нему с помощью журналов docker [id], например. | `acb_step_%d`, где `%d` — от нуля индекс шага сверху вниз в yaml-файл |
| `ignoreErrors` | bool | Yes | Нужно ли пометьте этап как успешной независимо от того, произошла ли ошибка во время выполнения контейнера. | `false` |
| `isolation` | строка | Yes | Уровень изоляции контейнера. | `default` |
| `keep` | bool | Yes | Следует ли сохранить контейнер этого шага после выполнения. | `false` |
| `network` | object | Yes | Идентифицирует сети, в котором выполняется контейнер. | Нет |
| `ports` | [строка, строка, ...] | Yes | Массив порты, которые публикуются из контейнера на узле. |  Нет |
| `pull` | bool | Yes | Необходимость принудительного извлечения контейнера перед выполнением его, чтобы предотвратить любое поведение кэширования. | `false` |
| `privileged` | bool | Yes | Необходимость запуска контейнера в привилегированном режиме. | `false` |
| `repeat` | int | Yes | Число повторных попыток для повторения выполнения контейнера. | 0 |
| `retries` | int | Yes | Число повторных попыток при сбое выполнения контейнера. Повторная попытка предпринимается только в том случае, если код выхода контейнера имеет ненулевое значение. | 0 |
| `retryDelay` | int (секунды) | Yes | Задержка в секундах между повторными попытками выполнения контейнера. | 0 |
| `secret` | object | Yes | Определяет, секрет хранилища ключей Azure или управляемого удостоверения для ресурсов Azure. | Нет |
| `startDelay` | int (секунды) | Yes | Число секунд задержки выполнения контейнера. | 0 |
| `timeout` | int (секунды) | Yes | Максимальное число секунд, в течение которого может выполняться шаг перед завершением. | 600 |
| [`when`](#example-when) | [строка, строка, ...] | Yes | Настраивает зависимость шага от одного или нескольких других шагов в пределах задачи. | Нет |
| `user` | строка | Yes | Имя пользователя или идентификатор UID данного контейнера | Нет |
| `workingDirectory` | строка | Yes | Задает рабочий каталог для шага. По умолчанию служба "Задачи ACR" создает корневой каталог в качестве рабочего каталога. Но если сборка включает несколько шагов, для предыдущих и последующих шагов можно использовать общие артефакты, указав один и тот же рабочий каталог. | `$HOME` |

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

Построение параллельных образов:

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

### <a name="run46id"></a>Run&#46;ID

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

## <a name="next-steps"></a>Дальнейшие действия

Обзор многошаговых задач см. в статье [Run multi-step build, test, and patch tasks in ACR Tasks](container-registry-tasks-multi-step.md) (Выполнение многошаговых задач сборки, тестирования и исправления в решении "Задачи ACR").

Сведения об одношаговых сборках см. в статье [Автоматизация установки исправлений ОС и платформы с помощью службы "Задачи ACR"](container-registry-tasks-overview.md).

<!-- IMAGES -->

<!-- LINKS - External -->
[acr-tasks]: https://github.com/Azure-Samples/acr-tasks

<!-- LINKS - Internal -->
[az-acr-run]: /cli/azure/acr#az-acr-run
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-configure]: /cli/azure/reference-index#az-configure
