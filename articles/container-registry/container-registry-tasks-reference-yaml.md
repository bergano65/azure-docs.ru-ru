---
title: YAML reference - ACR Tasks
description: Справка по определению задач Реестра контейнеров Azure в YAML, включая свойства задач, типы шагов, свойства шагов и встроенные переменные.
ms.topic: article
ms.date: 10/23/2019
ms.openlocfilehash: a27f55d08a7ed5d7bf3360030eabefc4b7720b82
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/24/2019
ms.locfileid: "74454647"
---
# <a name="acr-tasks-reference-yaml"></a>Справка по задачам Реестра контейнеров Azure: YAML

Определение многошаговой задачи в службе "Задачи ACR" предоставляет ориентированный на контейнеры вычислительный примитив, направленный на сборку, тестирование и исправление контейнеров. This article covers the commands, parameters, properties, and syntax for the YAML files that define your multi-step tasks.

Эта статья содержит справочные сведения о создании YAML-файлов многошаговых задач для службы "Задачи ACR". Общие сведения о задачах ACR см. в [обзоре службы "Задачи ACR"](container-registry-tasks-overview.md).

## <a name="acr-taskyaml-file-format"></a>Формат файла acr-task.yaml

Служба "Задачи ACR" поддерживает объявление многошаговой задачи в стандартном синтаксисе YAML. You define a task's steps in a YAML file. You can then run the task manually by passing the file to the [az acr run][az-acr-run] command. Or, use the file to create a task with [az acr task create][az-acr-task-create] that's triggered automatically on a Git commit or base image update. Несмотря на то, что в этой статье `acr-task.yaml` рассматривается как файл, содержащий шаги, служба "Задачи ACR" поддерживает любое допустимое имя файла с [поддерживаемым расширением](#supported-task-filename-extensions).

Примитивами `acr-task.yaml` верхнего уровня являются **свойства задачи**, **типы шагов** и **свойства шага**.

* [Свойства задачи](#task-properties) применяются для всех шагов на протяжении всего выполнения задачи. There are several global task properties, including:
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

В следующих разделах этой статьи приводятся ссылки на несколько примеров файлов задач. The sample tasks are in a public GitHub repository, [Azure-Samples/acr-tasks][acr-tasks]. You can run them with the Azure CLI command [az acr run][az-acr-run]. Примеры команд выглядят приблизительно так:

```azurecli
az acr run -f build-push-hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

Форматирование в примерах команд предполагает, что вы уже настроили реестр по умолчанию в Azure CLI, поэтому параметр `--registry` опускается. To configure a default registry, use the [az configure][az-configure] command with the `--defaults` parameter, which accepts an `acr=REGISTRY_NAME` value.

Например, чтобы настроить Azure CLI с реестром myregistry по умолчанию, выполните следующую команду:

```azurecli
az configure --defaults acr=myregistry
```

## <a name="task-properties"></a>Свойства задачи

Task properties typically appear at the top of an `acr-task.yaml` file, and are global properties that apply throughout the full execution of the task steps. Некоторые из этих глобальных свойств могут быть переопределены в рамках отдельного шага.

| Свойство | Тип | Необязательно | Описание | Поддерживается ли переопределение | Значение по умолчанию |
| -------- | ---- | -------- | ----------- | ------------------ | ------------- |
| `version` | string | ДА | Версия файла `acr-task.yaml`, проанализированного службой "Задачи ACR". В то время как служба "Задачи ACR" стремится поддерживать обратную совместимость, это значение позволяет службе поддерживать совместимость в пределах заданной версии. If unspecified, defaults to the latest version. | Нет | Нет |
| `stepTimeout` | int (секунды) | ДА | Максимальное число секунд выполнения шага. If the property is specified on a task, it sets the default `timeout` property of all the steps. If the `timeout` property is specified on a step, it overrides the property provided by the task. | ДА | 600 (10 минут) |
| `workingDirectory` | string | ДА | The working directory of the container during runtime. If the property is specified on a task, it sets the default `workingDirectory` property of all the steps. If specified on a step, it overrides the property provided by the task. | ДА | `$HOME` |
| `env` | [строка, строка, ...] | ДА |  Array of strings in `key=value` format that define the environment variables for the task. If the property is specified on a task, it sets the default `env` property of all the steps. If specified on a step, it overrides any environment variables inherited from the task. | Нет |
| `secrets` | [secret, secret, ...] | ДА | Array of [secret](#secret) objects. | Нет |
| `networks` | [network, network, ...] | ДА | Array of [network](#network) objects. | Нет |

### <a name="secret"></a>secret

The secret object has the following properties.

| Свойство | Тип | Необязательно | Описание | Значение по умолчанию |
| -------- | ---- | -------- | ----------- | ------- |
| `id` | string | Нет | The identifier of the secret. | Нет |
| `keyvault` | string | ДА | The Azure Key Vault Secret URL. | Нет |
| `clientID` | string | ДА | The client ID of the [user-assigned managed identity](container-registry-tasks-authentication-managed-identity.md) for Azure resources. | Нет |

### <a name="network"></a>Сеть

The network object has the following properties.

| Свойство | Тип | Необязательно | Описание | Значение по умолчанию |
| -------- | ---- | -------- | ----------- | ------- | 
| `name` | string | Нет | Имя сети. | Нет |
| `driver` | string | ДА | The driver to manage the network. | Нет |
| `ipv6` | bool | ДА | Whether IPv6 networking is enabled. | `false` |
| `skipCreation` | bool | ДА | Whether to skip network creation. | `false` |
| `isDefault` | bool | ДА | Whether the network is a default network provided with Azure Container Registry | `false` |

## <a name="task-step-types"></a>Типы шагов задач

Служба "Задачи ACR" поддерживает три типа шагов. Каждый тип шага поддерживает несколько свойств, подробно описанных в соответствующих разделах о каждом из типов.

| Тип шага | Описание |
| --------- | ----------- |
| [`build`](#build) | Создает образ контейнера с использованием знакомого синтаксиса `docker build`. |
| [`push`](#push) | Выполняет команду `docker push` для отправки только что созданных или перемаркированных образов в реестр контейнеров. Поддерживается Реестр контейнеров Azure, другие закрытые реестры, а также общедоступный реестр Docker Hub. |
| [`cmd`](#cmd) | Запускает контейнер в качестве команды с параметрами, передаваемыми в `[ENTRYPOINT]` контейнера. The `cmd` step type supports parameters like `env`, `detach`, and other familiar `docker run` command options, enabling unit and functional testing with concurrent container execution. |

## <a name="build"></a>build;

Сборка образа контейнера. Тип шага `build` представляет мультитенантное защищенное средство запуска `docker build` в облаке в качестве примитива первого класса.

### <a name="syntax-build"></a>Синтаксис: build

```yml
version: v1.1.0
steps:
  - [build]: -t [imageName]:[tag] -f [Dockerfile] [context]
    [property]: [value]
```

Тип шага `build` поддерживает параметры, описанные в следующей таблице. Тип шага `build` также поддерживает все параметры сборки из команды [docker build](https://docs.docker.com/engine/reference/commandline/build/), такие как `--build-arg`, для определения переменных во время сборки.

| Параметр | Описание | Необязательно |
| --------- | ----------- | :-------: |
| `-t` &#124; `--image` | Определяет полное значение `image:tag` созданного образа.<br /><br />Так как образы могут использоваться для внутренних проверок задач, например функциональных тестов, не все образы требуют выполнения `push` для отправки в реестр. Но чтобы создать экземпляр образа в пределах выполнения задачи, необходимо указать имя образа для ссылки.<br /><br />Unlike `az acr build`, running ACR Tasks doesn't provide default push behavior. При использовании службы "Задачи ACR" стандартный сценарий предполагает возможность создания, проверки и последующей отправки образа. Сведения о том, как при необходимости отправлять созданные образы, см. в описании команды [push](#push). | ДА |
| `-f` &#124; `--file` | Позволяет указать файл Dockerfile, передаваемый в `docker build`. Если этот параметр не указан, по умолчанию принимается Dockerfile в корне контекста. To specify a Dockerfile, pass the filename relative to the root of the context. | ДА |
| `context` | Корневой каталог, передаваемый в `docker build`. В качестве корневого каталога каждой задачи задается общий каталог [workingDirectory](#task-step-properties), который включает в себя корень связанного клонированного каталога Git. | Нет |

### <a name="properties-build"></a>Свойства: build

Тип шага `build` поддерживает следующие свойства. Find details of these properties in the [Task step properties](#task-step-properties) section of this article.

| | | |
| -------- | ---- | -------- |
| `detach` | bool | Необязательно |
| `disableWorkingDirectoryOverride` | bool | Необязательно |
| `entryPoint` | string | Необязательно |
| `env` | [строка, строка, ...] | Необязательно |
| `expose` | [строка, строка, ...] | Необязательно |
| `id` | string | Необязательно |
| `ignoreErrors` | bool | Необязательно |
| `isolation` | string | Необязательно |
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
| `workingDirectory` | string | Необязательно |

### <a name="examples-build"></a>Примеры: build

#### <a name="build-image---context-in-root"></a>Сборка образа — контекст в корне

```azurecli
az acr run -f build-hello-world.yaml https://github.com/AzureCR/acr-tasks-sample.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/build-hello-world.yaml -->
[!code-yml[task](~/acr-tasks/build-hello-world.yaml)]

#### <a name="build-image---context-in-subdirectory"></a>Сборка образа — контекст в подкаталоге

```yml
version: v1.1.0
steps:
  - build: -t $Registry/hello-world -f hello-world.dockerfile ./subDirectory
```

## <a name="push"></a>push

Отправляет командой один или несколько из только что созданных или перемаркированных образов в реестр контейнеров. Команда push поддерживается для таких закрытых реестров, как Реестр контейнеров Azure, а также общедоступный реестр Docker Hub.

### <a name="syntax-push"></a>Синтаксис: push

Тип шага `push` поддерживает коллекцию образов. Синтаксис коллекции YAML поддерживает встроенные и вложенные форматы. Отправка одного образа обычно представляется с помощью встроенного синтаксиса:

```yml
version: v1.1.0
steps:
  # Inline YAML collection syntax
  - push: ["$Registry/hello-world:$ID"]
```

Для повышения удобочитаемости используйте вложенный синтаксис при отправке нескольких образов:

```yml
version: v1.1.0
steps:
  # Nested YAML collection syntax
  - push:
    - $Registry/hello-world:$ID
    - $Registry/hello-world:latest
```

### <a name="properties-push"></a>Свойства: push

Тип шага `push` поддерживает следующие свойства. Find details of these properties in the [Task step properties](#task-step-properties) section of this article.

| | | |
| -------- | ---- | -------- |
| `env` | [строка, строка, ...] | Необязательно |
| `id` | string | Необязательно |
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
version: v1.1.0
steps:
  - [cmd]: [containerImage]:[tag (optional)] [cmdParameters to the image]
```

### <a name="properties-cmd"></a>Свойства: cmd

Тип шага `cmd` поддерживает следующие свойства:

| | | |
| -------- | ---- | -------- |
| `detach` | bool | Необязательно |
| `disableWorkingDirectoryOverride` | bool | Необязательно |
| `entryPoint` | string | Необязательно |
| `env` | [строка, строка, ...] | Необязательно |
| `expose` | [строка, строка, ...] | Необязательно |
| `id` | string | Необязательно |
| `ignoreErrors` | bool | Необязательно |
| `isolation` | string | Необязательно |
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
| `workingDirectory` | string | Необязательно |

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
version: v1.1.0
steps:
  - cmd: docker.io/bash:3.0 echo hello world
```

By using the standard `docker run` image reference convention, `cmd` can run images from any private registry or the public Docker Hub. Если вы ссылаетесь на образы в том же реестре, в котором выполняется задача ACR, не нужно указывать учетные данные реестра.

* Run an image that's from an Azure container registry. The following example assumes you have a registry named `myregistry`, and a custom image `myimage:mytag`.

    ```yml
    version: v1.1.0
    steps:
        - cmd: myregistry.azurecr.io/myimage:mytag
    ```

* Generalize the registry reference with a Run variable or alias

    Instead of hard-coding your registry name in an `acr-task.yaml` file, you can make it more portable by using a [Run variable](#run-variables) or [alias](#aliases). The `Run.Registry` variable or `$Registry` alias expands at runtime to the name of the registry in which the task is executing.

    For example, to generalize the preceding task so that it works in any Azure container registry, reference the $Registry variable in the image name:

    ```yml
    version: v1.1.0
    steps:
      - cmd: $Registry/myimage:mytag
    ```

## <a name="task-step-properties"></a>Свойства шага задачи

Каждый тип шага поддерживает несколько свойств, подходящих для его типа. В следующей таблице определены все доступные свойства шагов. Не все типы шагов поддерживают все свойства. Чтобы узнать, какие из этих свойств доступны для каждого типа шага, см. разделы справки по типам шагов [cmd](#cmd), [build](#build) и [push](#push).

| Свойство | Тип | Необязательно | Описание | Значение по умолчанию |
| -------- | ---- | -------- | ----------- | ------- |
| `detach` | bool | ДА | Определяет, следует ли отсоединить контейнер при запуске. | `false` |
| `disableWorkingDirectoryOverride` | bool | ДА | Whether to disable `workingDirectory` override functionality. Use this in combination with `workingDirectory` to have complete control over the container's working directory. | `false` |
| `entryPoint` | string | ДА | Переопределяет `[ENTRYPOINT]` контейнера шага. | Нет |
| `env` | [строка, строка, ...] | ДА | Массив строк в формате `key=value`, определяющий переменные среды для шага. | Нет |
| `expose` | [строка, строка, ...] | ДА | Array of ports that are exposed from the container. |  Нет |
| [`id`](#example-id) | string | ДА | Однозначно определяет шаг в рамках задачи. Другие шаги в задаче могут ссылаться на `id` шага, например для проверки зависимостей с помощью `when`.<br /><br />`id` также является именем запущенного контейнера. Процессы, запущенные в других контейнерах в задаче, могут ссылаться на `id` в качестве имени его узла DNS, или же для доступа к нему с помощью журналов docker [id], например. | `acb_step_%d`, where `%d` is the 0-based index of the step top-down in the YAML file |
| `ignoreErrors` | bool | ДА | Whether to mark the step as successful regardless of whether an error occurred during container execution. | `false` |
| `isolation` | string | ДА | The isolation level of the container. | `default` |
| `keep` | bool | ДА | Следует ли сохранить контейнер этого шага после выполнения. | `false` |
| `network` | object | ДА | Identifies a network in which the container runs. | Нет |
| `ports` | [строка, строка, ...] | ДА | Array of ports that are published from the container to the host. |  Нет |
| `pull` | bool | ДА | Whether to force a pull of the container before executing it to prevent any caching behavior. | `false` |
| `privileged` | bool | ДА | Whether to run the container in privileged mode. | `false` |
| `repeat` | int | ДА | The number of retries to repeat the execution of a container. | 0 |
| `retries` | int | ДА | The number of retries to attempt if a container fails its execution. A retry is only attempted if a container's exit code is non-zero. | 0 |
| `retryDelay` | int (секунды) | ДА | The delay in seconds between retries of a container's execution. | 0 |
| `secret` | object | ДА | Identifies an Azure Key Vault secret or [managed identity for Azure resources](container-registry-tasks-authentication-managed-identity.md). | Нет |
| `startDelay` | int (секунды) | ДА | Number of seconds to delay a container's execution. | 0 |
| `timeout` | int (секунды) | ДА | Максимальное число секунд, в течение которого может выполняться шаг перед завершением. | 600 |
| [`when`](#example-when) | [строка, строка, ...] | ДА | Настраивает зависимость шага от одного или нескольких других шагов в пределах задачи. | Нет |
| `user` | string | ДА | The user name or UID of a container | Нет |
| `workingDirectory` | string | ДА | Задает рабочий каталог для шага. По умолчанию служба "Задачи ACR" создает корневой каталог в качестве рабочего каталога. Но если сборка включает несколько шагов, для предыдущих и последующих шагов можно использовать общие артефакты, указав один и тот же рабочий каталог. | `$HOME` |

### <a name="examples-task-step-properties"></a>Примеры. Свойства шага задачи

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

Parallel images build:

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
* `Run.SharedVolume`
* `Run.Registry`
* `Run.RegistryName`
* `Run.Date`
* `Run.OS`
* `Run.Architecture`
* `Run.Commit`
* `Run.Branch`
* `Run.TaskName`

The variable names are generally self-explanatory. Details follows for commonly used variables. As of YAML version `v1.1.0`, you can use an abbreviated, predefined [task alias](#aliases) in place of most run variables. For example, in place of `{{.Run.Registry}}`, use the `$Registry` alias.

### <a name="runid"></a>Run.ID

Each Run, through `az acr run`, or trigger based execution of tasks created through `az acr task create`, has a unique ID. Идентификатор представляет выполняющуюся в текущий момент команду Run.

Обычно используется для уникальной маркировки образа:

```yml
version: v1.1.0
steps:
    - build: -t $Registry/hello-world:$ID .
```

### <a name="runregistry"></a>Run.Registry

Полное имя сервера реестра. Обычно используется для универсальной ссылки на реестр, в котором выполняется задача.

```yml
version: v1.1.0
steps:
  - build: -t $Registry/hello-world:$ID .
```

### <a name="runregistryname"></a>Run.RegistryName

The name of the container registry. Typically used in task steps that don't require a fully qualified server name, for example, `cmd` steps that run Azure CLI commands on registries.

```yml
version 1.1.0
steps:
# List repositories in registry
- cmd: az login --identity
- cmd: az acr repository list --name $RegistryName
```

### <a name="rundate"></a>Run.Date

Текущее время в формате UTC, когда начался запуск.

### <a name="runcommit"></a>Run.Commit

For a task triggered by a commit to a GitHub repository, the commit identifier.

### <a name="runbranch"></a>Run.Branch

For a task triggered by a commit to a GitHub repository, the branch name.

## <a name="aliases"></a>Псевдонимы

As of `v1.1.0`, ACR Tasks supports aliases that are available to task steps when they execute. Aliases are similar in concept to aliases (command shortcuts) supported in bash and some other command shells. 

With an alias, you can launch any command or group of commands (including options and filenames) by entering a single word.

ACR Tasks supports several predefined aliases and also custom aliases you create.

### <a name="predefined-aliases"></a>Predefined aliases

The following task aliases are available to use in place of [run variables](#run-variables):

| Alias | Run variable |
| ----- | ------------ |
| `ID` | `Run.ID` |
| `SharedVolume` | `Run.SharedVolume` |
| `Registry` | `Run.Registry` |
| `RegistryName` | `Run.RegistryName` |
| `Date` | `Run.Date` |
| `OS` | `Run.OS` |
| `Architecture` | `Run.Architecture` |
| `Commit` | `Run.Commit` |
| `Branch` | `Run.Branch` |

In task steps, precede an alias with the `$` directive, as in this example:

```yaml
version: v1.1.0
steps:
  - build: -t $Registry/hello-world:$ID -f hello-world.dockerfile .
```

### <a name="image-aliases"></a>Image aliases

Each of the following aliases points to a stable image in Microsoft Container Registry (MCR). You can refer to each of them in the `cmd` section of a Task file without using a directive.

| Alias | Изображение |
| ----- | ----- |
| `acr` | `mcr.microsoft.com/acr/acr-cli:0.1` |
| `az` | `mcr.microsoft.com/acr/azure-cli:d0725bc` |
| `bash` | `mcr.microsoft.com/acr/bash:d0725bc` |
| `curl` | `mcr.microsoft.com/acr/curl:d0725bc` |

The following example task uses several aliases to [purge](container-registry-auto-purge.md) image tags older than 7 days in the repo `samples/hello-world` in the run registry:

```yaml
version: v1.1.0
steps:
  - cmd: acr tag list --registry $RegistryName --repository samples/hello-world
  - cmd: acr purge --registry $RegistryName --filter samples/hello-world:.* --ago 7d
```

### <a name="custom-alias"></a>Custom alias

Define a custom alias in your YAML file and use it as shown in the following example. An alias can contain only alphanumeric characters. The default directive to expand an alias is the `$` character.

```yml
version: v1.1.0
alias:
  values:
    repo: myrepo
steps:
  - build: -t $Registry/$repo/hello-world:$ID -f Dockerfile .
```

You can link to a remote or local YAML file for custom alias definitions. The following example links to a YAML file in Azure blob storage:

```yml
version: v1.1.0
alias:
  src:  # link to local or remote custom alias files
    - 'https://link/to/blob/remoteAliases.yml?readSasToken'
[...]
```

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
