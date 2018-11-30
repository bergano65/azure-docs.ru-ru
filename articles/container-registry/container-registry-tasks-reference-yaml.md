---
title: Справка по задачам Реестра контейнеров Azure — YAML
description: Справка по определению задач Реестра контейнеров Azure в YAML, включая свойства задач, типы шагов, свойства шагов и встроенные переменные.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 11/13/2018
ms.author: danlep
ms.openlocfilehash: e91b4e881c0f39304e3042d556f111db2089f7de
ms.sourcegitcommit: 922f7a8b75e9e15a17e904cc941bdfb0f32dc153
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/27/2018
ms.locfileid: "52334488"
---
# <a name="acr-tasks-reference-yaml"></a>Справка по задачам Реестра контейнеров Azure: YAML

Определение многошаговой задачи в службе "Задачи ACR" предоставляет ориентированный на контейнеры вычислительный примитив, направленный на сборку, тестирование и исправление контейнеров. В этой статье рассматриваются команды, параметры, свойства и синтаксис для файлов YAML, определяющих многошаговые задачи.

Эта статья содержит справочные сведения о создании YAML-файлов многошаговых задач для службы "Задачи ACR". Общие сведения о задачах ACR см. в [обзоре службы "Задачи ACR"](container-registry-tasks-overview.md).

> [!IMPORTANT]
> Сейчас функция многошаговых задач службы "Задачи ACR" доступна в предварительной версии. Предварительные версии предоставляются только в том случае, если вы принимаете [дополнительные условия использования][terms-of-use]. Некоторые аспекты этой функции могут быть изменены до выхода общедоступной версии.

## <a name="acr-taskyaml-file-format"></a>Формат файла acr-task.yaml

Служба "Задачи ACR" поддерживает объявление многошаговой задачи в стандартном синтаксисе YAML. Необходимо определить шаги задачи в YAML-файле, который затем можно запустить вручную или автоматически при фиксации в Git или обновлении базового образа. Несмотря на то, что в этой статье `acr-task.yaml` рассматривается как файл, содержащий шаги, служба "Задачи ACR" поддерживает любое допустимое имя файла с [поддерживаемым расширением](#supported-task-filename-extensions).

Примитивами `acr-task.yaml` верхнего уровня являются **свойства задачи**, **типы шагов** и **свойства шага**.

* [Свойства задачи](#task-properties) применяются для всех шагов на протяжении всего выполнения задачи. Существуют три глобальных свойства задачи:
  * версия
  * stepTimeout;
  * totalTimeout.
* [Типы шагов задачи](#task-step-types) представляют собой типы действий, которые могут быть выполнены в задаче. Имеются три типа шагов:
  * build;
  * push;
  * cmd.
* [Свойства шага задачи](#task-step-properties) — параметры, которые применяются к отдельному шагу. Существует несколько свойств шага, в том числе:
  * startDelay;
  * timeout
  * when
  * ...и многие другие.

Ниже приведен пример базового формата файла `acr-task.yaml`, включая некоторые общие свойства шага. Хотя он не является исчерпывающим представлением всех доступных свойств шага или использования типа шага, он предоставляет краткий обзор базового формата файла.

```yaml
version: # acr-task.yaml format version.
stepTimeout: # Seconds each step may take.
totalTimeout: # Total seconds allowed for all steps to complete.
steps: # A collection of image or container actions.
    build: # Equivalent to "docker build," but in a multi-tenant environment
    push: # Push a newly built or retagged image to a registry.
      when: # Step property that defines either parallel or dependent step execution.
    cmd: # Executes a container, supports specifying an [ENTRYPOINT] and parameters.
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

Свойства задачи обычно отображаются в верхней части файла `acr-task.yaml` и являются глобальными свойствами, которые применяются на протяжении всего выполнения задачи. Некоторые из этих глобальных свойств могут быть переопределены в рамках отдельного шага.

| Свойство | type | Необязательно | ОПИСАНИЕ | Поддерживается ли переопределение | Значение по умолчанию |
| -------- | ---- | -------- | ----------- | ------------------ | ------------- |
| `version` | строка | Нет  | Версия файла `acr-task.yaml`, проанализированного службой "Задачи ACR". В то время как служба "Задачи ACR" стремится поддерживать обратную совместимость, это значение позволяет службе поддерживать совместимость в пределах заданной версии. | Нет  | Нет |
| `stepTimeout` | int (секунды) | Yes | Максимальное число секунд выполнения шага. Это свойство можно переопределить в том или ином шаге, задав для него свойство [timeout](#timeout) (время ожидания). | Yes | 600 (10 минут) |
| `totalTimeout` | int (секунды) | Yes | Максимальное число секунд выполнения задачи. В этом случае под выполнением подразумевается выполнение и завершение всех шагов задачи, будь то успешное или неудачное. Также включается задача вывода выходных данных задачи, например обнаруженных зависимостей образа и состояние выполнения задачи. | Нет  | 3600 (1 час) |

## <a name="task-step-types"></a>Типы шагов задач

Служба "Задачи ACR" поддерживает три типа шагов. Каждый тип шага поддерживает несколько свойств, подробно описанных в соответствующих разделах о каждом из типов.

| Тип шага | ОПИСАНИЕ |
| --------- | ----------- |
| [`build`](#build) | Создает образ контейнера с использованием знакомого синтаксиса `docker build`. |
| [`push`](#push) | Выполняет команду `docker push` для отправки только что созданных или перемаркированных образов в реестр контейнеров. Поддерживается Реестр контейнеров Azure, другие закрытые реестры, а также общедоступный реестр Docker Hub.
| [`cmd`](#cmd) | Запускает контейнер в качестве команды с параметрами, передаваемыми в `[ENTRYPOINT]` контейнера. Тип шага `cmd` поддерживает такие параметры, как env, detach и другие знакомые параметры команды `docker run`, что обеспечивает возможность модульного и функционального тестирования с параллельным выполнением контейнеров. |

## <a name="build"></a>build;

Сборка образа контейнера. Тип шага `build` представляет мультитенантное защищенное средство запуска `docker build` в облаке в качестве примитива первого класса.

### <a name="syntax-build"></a>Синтаксис: build

```yaml
version: 1.0-preview-1
steps:
    - [build]: -t [imageName]:[tag] -f [Dockerfile] [context]
      [property]: [value]
```

Тип шага `build` поддерживает параметры, описанные в следующей таблице. Тип шага `build` также поддерживает все параметры сборки из команды [docker build](https://docs.docker.com/engine/reference/commandline/build/), такие как `--build-arg`, для определения переменных во время сборки.

| Параметр | ОПИСАНИЕ | Необязательно |
| --------- | ----------- | :-------: |
| `-t` &#124; `--image` | Определяет полное значение `image:tag` созданного образа.<br /><br />Так как образы могут использоваться для внутренних проверок задач, например функциональных тестов, не все образы требуют выполнения `push` для отправки в реестр. Но чтобы создать экземпляр образа в пределах выполнения задачи, необходимо указать имя образа для ссылки.<br /><br />В отличие от `az acr build`, выполнение задач ACR не предоставляет поведения по умолчанию команды push. При использовании службы "Задачи ACR" стандартный сценарий предполагает возможность создания, проверки и последующей отправки образа. Сведения о том, как при необходимости отправлять созданные образы, см. в описании команды [push](#push). | Yes |
| `-f` &#124; `--file` | Позволяет указать файл Dockerfile, передаваемый в `docker build`. Если этот параметр не указан, по умолчанию принимается Dockerfile в корне контекста. Чтобы указать другой Dockerfile, передайте имя файла относительно корня контекста. | Yes |
| `context` | Корневой каталог, передаваемый в `docker build`. В качестве корневого каталога каждой задачи задается общий каталог [workingDirectory](#task-step-properties), который включает в себя корень связанного клонированного каталога Git. | Нет  |

### <a name="properties-build"></a>Свойства: build

Тип шага `build` поддерживает следующие свойства. Сведения об этих свойствах см. в разделе [Свойства шага задачи](#task-step-properties) этой статьи.

| | | |
| -------- | ---- | -------- |
| `detach` | bool | Необязательно |
| `entryPoint` | строка | Необязательно |
| `env` | [строка, строка, ...] | Необязательно |
| `id` | строка | Необязательно |
| `ignoreErrors` | bool | Необязательно |
| `keep` | bool | Необязательно |
| `startDelay` | int (секунды) | Необязательно |
| `timeout` | int (секунды) | Необязательно |
| `when` | [строка, строка, ...] | Необязательно |
| `workingDirectory` | строка | Необязательно |

### <a name="examples-build"></a>Примеры: build

#### <a name="build-image---context-in-root"></a>Сборка образа — контекст в корне

```azurecli
az acr run -f build-hello-world.yaml https://github.com/AzureCR/acr-tasks-sample.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/build-hello-world.yaml --> [!code-yml[task](~/acr-tasks/build-hello-world.yaml)]

#### <a name="build-image---context-in-subdirectory"></a>Сборка образа — контекст в подкаталоге

```yaml
version: 1.0-preview-1
steps:
- build: -t {{.Run.Registry}}/hello-world -f hello-world.dockerfile ./subDirectory
```

## <a name="push"></a>push

Отправляет командой один или несколько из только что созданных или перемаркированных образов в реестр контейнеров. Команда push поддерживается для таких закрытых реестров, как Реестр контейнеров Azure, а также общедоступный реестр Docker Hub.

### <a name="syntax-push"></a>Синтаксис: push

Тип шага `push` поддерживает коллекцию образов. Синтаксис коллекции YAML поддерживает встроенные и вложенные форматы. Отправка одного образа обычно представляется с помощью встроенного синтаксиса:

```yaml
version: 1.0-preview-1
steps:
  # Inline YAML collection syntax
  - push: ["{{.Run.Registry}}/hello-world:{{.Run.ID}}"]
```

Для повышения удобочитаемости используйте вложенный синтаксис при отправке нескольких образов:

```yaml
version: 1.0-preview-1
steps:
  # Nested YAML collection syntax
  - push:
    - {{.Run.Registry}}/hello-world:{{.Run.ID}}
    - {{.Run.Registry}}/hello-world:latest
```

### <a name="properties-push"></a>Свойства: push

Тип шага `push` поддерживает следующие свойства. Сведения об этих свойствах см. в разделе [Свойства шага задачи](#task-step-properties) этой статьи.

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

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/build-push-hello-world.yaml --> [!code-yml[task](~/acr-tasks/build-push-hello-world.yaml)]

#### <a name="build-push-and-run"></a>Сборка, отправка и запуск

```azurecli
az acr run -f build-run-hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/build-run-hello-world.yaml --> [!code-yml[task](~/acr-tasks/build-run-hello-world.yaml)]

## <a name="cmd"></a>cmd.

Тип шага `cmd` запускает контейнер.

### <a name="syntax-cmd"></a>Синтаксис: cmd

```yaml
version: 1.0-preview-1
steps:
    - [cmd]: [containerImage]:[tag (optional)] [cmdParameters to the image]
```

### <a name="properties-cmd"></a>Свойства: cmd

Тип шага `cmd` поддерживает следующие свойства:

| | | |
| -------- | ---- | -------- |
| `detach` | bool | Необязательно |
| `entryPoint` | строка | Необязательно |
| `env` | [строка, строка, ...] | Необязательно |
| `id` | строка | Необязательно |
| `ignoreErrors` | bool | Необязательно |
| `keep` | bool | Необязательно |
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

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.yaml --> [!code-yml[task](~/acr-tasks/hello-world.yaml)]

#### <a name="run-bash-image-and-echo-hello-world"></a>Запуск образа bash и вывод командой echo hello world

Эта команда выполняет файл задачи `bash-echo.yaml`, который ссылается на образ [bash](https://hub.docker.com/_/bash/) в Docker Hub.

```azurecli
az acr run -f bash-echo.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/bash-echo.yaml --> [!code-yml[task](~/acr-tasks/bash-echo.yaml)]

#### <a name="run-specific-bash-image-tag"></a>Тег для запуска определенного образа bash

Чтобы запустить конкретную версию образа, укажите тег в команде `cmd`.

Эта команда выполняет файл задачи `bash-echo-3.yaml`, который ссылается на образ [bash:3.0](https://hub.docker.com/_/bash/) в Docker Hub.

```azurecli
az acr run -f bash-echo-3.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/bash-echo-3.yaml --> [!code-yml[task](~/acr-tasks/bash-echo-3.yaml)]

#### <a name="run-custom-images"></a>Запуск пользовательских образов

Тип шага `cmd` ссылается на образы с использованием стандартного формата `docker run`. Предполагается, что образы, которые не начинаются с указания реестра, поступают из docker.io. Предыдущий пример может быть также представлен следующим образом:

```yaml
version: 1.0-preview-1
steps:
    - cmd: docker.io/bash:3.0 echo hello world
```

Применяя стандартное соглашение о ссылках на образы `docker run`, `cmd` может запускать образы, находящиеся в любом частном реестре или общедоступном реестре Docker Hub. Если вы ссылаетесь на образы в том же реестре, в котором выполняется задача ACR, не нужно указывать учетные данные реестра.

* Запуск образа, содержащегося в Реестре контейнеров Azure

    Замените `[myregistry]` именем реестра:

    ```yaml
    version: 1.0-preview-1
    steps:
        - cmd: [myregistry].azurecr.io/bash:3.0 echo hello world
    ```

* Обобщение ссылки на реестр с помощью переменной Run

    Вместо непосредственного указания имени реестра в файле `acr-task.yaml` можно улучшить его переносимость с помощью [переменной Run](#run-variables). Переменная `Run.Registry` развертывается во время выполнения как имя реестра, в котором выполняется задача.

    Чтобы обобщить предыдущую задачу для работы в любом реестре контейнеров Azure, примените ссылку на переменную [Run.Registry](#runregistry) в имени образа:

    ```yaml
    version: 1.0-preview-1
    steps:
        - cmd: {{.Run.Registry}}/bash:3.0 echo hello world
    ```

## <a name="task-step-properties"></a>Свойства шага задачи

Каждый тип шага поддерживает несколько свойств, подходящих для его типа. В следующей таблице определены все доступные свойства шагов. Не все типы шагов поддерживают все свойства. Чтобы узнать, какие из этих свойств доступны для каждого типа шага, см. разделы справки по типам шагов [cmd](#cmd), [build](#build) и [push](#push).

| Свойство | type | Необязательно | ОПИСАНИЕ |
| -------- | ---- | -------- | ----------- |
| `detach` | bool | Yes | Определяет, следует ли отсоединить контейнер при запуске. |
| `entryPoint` | строка | Yes | Переопределяет `[ENTRYPOINT]` контейнера шага. |
| `env` | [строка, строка, ...] | Yes | Массив строк в формате `key=value`, определяющий переменные среды для шага. |
| [`id`](#example-id) | строка | Yes | Однозначно определяет шаг в рамках задачи. Другие шаги в задаче могут ссылаться на `id` шага, например для проверки зависимостей с помощью `when`.<br /><br />`id` также является именем запущенного контейнера. Процессы, запущенные в других контейнерах в задаче, могут ссылаться на `id` в качестве имени его узла DNS, или же для доступа к нему с помощью журналов docker [id], например. |
| `ignoreErrors` | bool | Yes | Если задано значение `true`, шаг помечается как выполненный независимо от того, произошла ли ошибка во время его выполнения. По умолчанию: `false`. |
| `keep` | bool | Yes | Следует ли сохранить контейнер этого шага после выполнения. |
| `startDelay` | int (секунды) | Yes | Число секунд задержки выполнения этапа. |
| `timeout` | int (секунды) | Yes | Максимальное число секунд, в течение которого может выполняться шаг перед завершением. |
| [`when`](#example-when) | [строка, строка, ...] | Yes | Настраивает зависимость шага от одного или нескольких других шагов в пределах задачи. |
| `workingDirectory` | строка | Yes | Задает рабочий каталог для шага. По умолчанию служба "Задачи ACR" создает корневой каталог в качестве рабочего каталога. Но если сборка включает несколько шагов, для предыдущих и последующих шагов можно использовать общие артефакты, указав один и тот же рабочий каталог. |

### <a name="examples-task-step-properties"></a>Примеры. Свойства шага задачи

#### <a name="example-id"></a>Пример: id

Создайте два образа, используя экземпляры образа функционального теста. Каждый шаг идентифицируется уникальным `id`, на который ссылаются другие шаги задачи в свойстве `when`.

```azurecli
az acr run -f when-parallel-dependent.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-parallel-dependent.yaml --> [!code-yml[task](~/acr-tasks/when-parallel-dependent.yaml)]

#### <a name="example-when"></a>Пример: when

Свойство `when` указывает зависимость шага от других шагов в пределах задачи. Оно поддерживает два значения параметров:

* `when: ["-"]` — указывает отсутствие зависимости от других шагов. Шаг с указанием `when: ["-"]` начнет выполняться немедленно и обеспечивает параллельное выполнение шагов.
* `when: ["id1", "id2"]` — указывает, что шаг зависит от шагов с `id` id1 и `id` id2. Этот шаг не будет выполнен до тех пор, пока не будут завершены оба шага id1 и id2.

Если свойство `when` не указано в шаге, этот шаг зависит от завершения предыдущего шага в файле `acr-task.yaml`.

Последовательное выполнение шагов без свойства `when`:

```azurecli
az acr run -f when-sequential-default.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-sequential-default.yaml --> [!code-yml[task](~/acr-tasks/when-sequential-default.yaml)]

Последовательное выполнение шагов со свойством `when`:

```azurecli
az acr run -f when-sequential-id.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-sequential-id.yaml --> [!code-yml[task](~/acr-tasks/when-sequential-id.yaml)]

Параллельное создание образов:

```azurecli
az acr run -f when-parallel.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-parallel.yaml --> [!code-yml[task](~/acr-tasks/when-parallel.yaml)]

Параллельное создание образов и тестирование зависимостей:

```azurecli
az acr run -f when-parallel-dependent.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-parallel-dependent.yaml --> [!code-yml[task](~/acr-tasks/when-parallel-dependent.yaml)]

## <a name="run-variables"></a>Переменные run

Служба "Задачи ACR" содержит набор переменных по умолчанию, доступных для шагов задачи при их выполнении. К этим переменным можно получить доступ с использованием формата `{{.Run.VariableName}}`, где `VariableName` — одна из следующих переменных:

* `Run.ID`
* `Run.Registry`
* `Run.Date`

### <a name="run46id"></a>Run&#46;ID

Каждый запуск с помощью `az acr run` или выполнение по триггеру задач, созданных с помощью `az acr task create`, обладает уникальным идентификатором. Идентификатор представляет выполняющуюся в текущий момент команду Run.

Обычно используется для уникальной маркировки образа:

```yaml
version: 1.0-preview-1
steps:
    - build: -t {{.Run.Registry}}/hello-world:{{.Run.ID}} .
```

### <a name="runregistry"></a>Run.Registry

Полное имя сервера реестра. Обычно используется для универсальной ссылки на реестр, в котором выполняется задача.

```yaml
version: 1.0-preview-1
steps:
    - build: -t {{.Run.Registry}}/hello-world:{{.Run.ID}} .
```

### <a name="rundate"></a>Run.Date

Текущее время в формате UTC, когда начался запуск.

## <a name="next-steps"></a>Дополнительная информация

Обзор многошаговых задач см. в статье [Run multi-step build, test, and patch tasks in ACR Tasks](container-registry-tasks-multi-step.md) (Выполнение многошаговых задач сборки, тестирования и исправления в решении "Задачи ACR").

Сведения об одношаговых сборках см. в статье [Автоматизация установки исправлений ОС и платформы с помощью службы "Задачи ACR"](container-registry-tasks-overview.md).

<!-- IMAGES -->

<!-- LINKS - External -->
[acr-tasks]: https://github.com/Azure-Samples/acr-tasks
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[az-acr-run]: /cli/azure/acr#az-acr-run
[az-configure]: /cli/azure/reference-index#az-configure