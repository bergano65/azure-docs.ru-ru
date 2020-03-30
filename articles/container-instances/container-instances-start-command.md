---
title: Переопределение точки входа в экземпляре контейнера
description: Установите строку команды для переопределения точки входа в изображении контейнера при развертывании экземпляра контейнера Azure
ms.topic: article
ms.date: 04/15/2019
ms.openlocfilehash: d9554603f78a07fa44af51d8f39a91e1b3c39f70
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79247128"
---
# <a name="set-the-command-line-in-a-container-instance-to-override-the-default-command-line-operation"></a>Установите строку команды в экземпляре контейнера для переопределения операции командной строки по умолчанию

При создании экземпляра контейнера дополнительно укажите команду для переопределения инструкции по строке команды по умолчанию, запеченной в изображении контейнера. Такое поведение похоже `--entrypoint` на аргумент `docker run`командной строки для .

[Например, параметры среды](container-instances-environment-variables.md) для экземпляров контейнеров, указание стартовой строки команд полезно для пакетных заданий, где необходимо динамически подготовить каждый контейнер с конфигурацией, конкретной задачой.

## <a name="command-line-guidelines"></a>Руководящие принципы командной строки

* По умолчанию командная строка определяет *один процесс, который начинается без оболочки* в контейнере. Например, командная строка может выполнить сценарий Python или исполняемый файл. Процесс может указать дополнительные параметры или аргументы.

* Чтобы выполнить несколько команд, начните командную строку, установив среду оболочки, которая поддерживается в операционной системе контейнера. Примеры:

  |Операционная система  |Оболочка по умолчанию  |
  |---------|---------|
  |Ubuntu     |   `/bin/bash`      |
  |Альпийский     |   `/bin/sh`      |
  |Windows     |    `cmd`     |

  Следуйте конвенциям оболочки, чтобы объединить несколько команд для выполнения в последовательности.

* В зависимости от конфигурации контейнера может потребоваться установить полный путь к исполняемой строке команды или аргументам.

* Установите соответствующую [политику перезагрузки](container-instances-restart-policy.md) для экземпляра контейнера в зависимости от того, определяет ли командная строка длительную задачу или задачу запуска. Например, политика перезапуска `Never` `OnFailure` или рекомендуется для выполнения задачи. 

* Если вам нужна информация о точке входа по умолчанию, установленной в изображении контейнера, используйте команду [проверки изображения докера.](https://docs.docker.com/engine/reference/commandline/image_inspect/)

## <a name="command-line-syntax"></a>Синтаксис командной строки

Синтаксис командной строки варьируется в зависимости от API Или инструмента Azure, используемого для создания экземпляров. Если вы указываете среду оболочки, также соблюдайте коммитации командсинтаксия оболочки.

* [az контейнер создать][az-container-create] команду: Передайте строку с параметром. `--command-line` Пример: `--command-line "python myscript.py arg1 arg2"`).

* [Новая AzureRmContainerGroup][new-azurermcontainergroup] Смдлет Azure PowerShell: Передайте строку с параметром. `-Command` Например, `-Command "echo hello"`.

* Портал Azure: В **командовании переопределить** свойство конфигурации контейнера, предоставьте запятую разделенный список строк, без котировок. Пример: `python, myscript.py, arg1, arg2`). 

* Шаблон управления ресурсами или файл YAML или один из SDK Azure: Укажите свойство командной строки как массив строк. Пример: массив `["python", "myscript.py", "arg1", "arg2"]` JSON в шаблоне менеджера ресурсов. 

  Если вы знакомы с синтаксисом [Dockerfile,](https://docs.docker.com/engine/reference/builder/) этот формат аналогичен форме *exec* инструкции CMD.

### <a name="examples"></a>Примеры

|    |  Azure CLI   | Портал | Шаблон | 
| ---- | ---- | --- | --- |
| Единая команда | `--command-line "python myscript.py arg1 arg2"` | **Командование переопределить**:`python, myscript.py, arg1, arg2` | `"command": ["python", "myscript.py", "arg1", "arg2"]` |
| Несколько команд | `--command-line "/bin/bash -c 'mkdir test; touch test/myfile; tail -f /dev/null'"` |**Командование переопределить**:`/bin/bash, -c, mkdir test; touch test/myfile; tail -f /dev/null` | `"command": ["/bin/bash", "-c", "mkdir test; touch test/myfile; tail -f /dev/null"]` |

## <a name="azure-cli-example"></a>Пример для Azure CLI

В качестве примера, изменить поведение [Microsoft / Aci-слово][aci-wordcount] контейнер изображения, который анализирует текст в *Гамлет* Шекспира, чтобы найти наиболее часто встречающиеся слова. Вместо того, чтобы анализировать *Гамлета,* можно установить командную строку, которая указывает на другой источник текста.

Чтобы увидеть вывод контейнера [Microsoft/aci-wordcount,][aci-wordcount] когда он анализирует текст по умолчанию, запустите его со следующим [исконтейнером создать][az-container-create] команду. Стартовая командная строка не указана, поэтому команда контейнера по умолчанию выполняется. Для целей иллюстрации этот пример устанавливает [переменные среды,](container-instances-environment-variables.md) чтобы найти 3 верхних слова длиной не менее пяти букв:

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer1 \
    --image mcr.microsoft.com/azuredocs/aci-wordcount:latest \
    --environment-variables NumWords=3 MinLength=5 \
    --restart-policy OnFailure
```

Как только состояние контейнера отображается как *Завершено* (использовать [показ контейнера az][az-container-show] для проверки состояния) отобразите журнал с [журналами контейнеров az,][az-container-logs] чтобы увидеть выход.

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer1
```

Выходные данные:

```console
[('HAMLET', 386), ('HORATIO', 127), ('CLAUDIUS', 120)]
```

Теперь нависните второй пример контейнера для анализа другого текста, указав другую строку команды. Скрипт Python, выполняемый контейнером, *wordcount.py,* принимает URL в качестве аргумента и обрабатывает содержимое этой страницы вместо значения по умолчанию.

Например, чтобы определить 3 верхних слова, которые, по крайней мере пять букв длиной в *Ромео и Джульетта*:

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer2 \
    --image mcr.microsoft.com/azuredocs/aci-wordcount:latest \
    --restart-policy OnFailure \
    --environment-variables NumWords=3 MinLength=5 \
    --command-line "python wordcount.py http://shakespeare.mit.edu/romeo_juliet/full.html"
```

Как и прежде, вы сможете просмотреть выходные данные с помощью журналов контейнера, когда его состояние изменится на *Terminated* (Завершено), как показано ниже.

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer2
```

Выходные данные:

```console
[('ROMEO', 177), ('JULIET', 134), ('CAPULET', 119)]
```

## <a name="next-steps"></a>Дальнейшие действия

Сценарии, основанные на задачах, такие как пакетная обработка большого набора данных с несколькими контейнерами, могут воспользоваться пользовательскими командными линиями во время выполнения. Для получения дополнительной информации о емкости на основе задач [см.](container-instances-restart-policy.md)

<!-- LINKS - External -->
[aci-wordcount]: https://hub.docker.com/_/microsoft-azuredocs-aci-wordcount

<!-- LINKS Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show
[new-azurermcontainergroup]: /powershell/module/azurerm.containerinstance/new-azurermcontainergroup
[portal]: https://portal.azure.com
