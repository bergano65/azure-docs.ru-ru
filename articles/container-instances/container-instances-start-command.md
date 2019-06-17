---
title: Использовать начальная Командная строка в экземплярах контейнеров Azure
description: Переопределение точки входа, настроенные в образ контейнера, при развертывании экземпляра контейнера Azure
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: article
ms.date: 04/15/2019
ms.author: danlep
ms.openlocfilehash: da94a4c79694f511d41e5c8dda8c786fc7049726
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "64569638"
---
# <a name="set-the-command-line-in-a-container-instance-to-override-the-default-command-line-operation"></a>Задайте командной строки в экземпляре контейнера для переопределения установленной по умолчанию операции командной строки

Когда вы создаете экземпляр контейнера, при необходимости укажите команду для переопределения инструкция командной строки по умолчанию, помещенного в образ контейнера. Это поведение похоже на `--entrypoint` аргумент командной строки для `docker run`.

Параметр, такие как [переменные среды](container-instances-environment-variables.md) для экземпляров контейнеров, указание начальная Командная строка полезно для пакетных заданий где необходимо подготовить каждый контейнер динамически с конфигурацией конкретных задач.

## <a name="command-line-guidelines"></a>Инструкции командной строки

* По умолчанию, в командной строке указывается *единый процесс, который запускает без оболочки* в контейнере. Например командной строке выполнить скрипт Python или исполняемый файл. 

* Чтобы выполнить несколько команд, начните в командной строке, задав среда оболочки, который поддерживается в операционной системе контейнера. Примеры:

  |Операционная система  |Оболочки по умолчанию  |
  |---------|---------|
  |Ubuntu     |   `/bin/bash`      |
  |Alpine     |   `/bin/sh`      |
  |Windows     |    `cmd`     |

  Соглашениям оболочки для объединения нескольких команд, выполняемых в последовательности.

* В зависимости от конфигурации контейнера необходимо задать полный путь к исполняемый файл командной строки или аргументы.

* Задайте необходимые [политика перезапуска](container-instances-restart-policy.md) для экземпляра контейнера, в зависимости от того, является ли командной строки указывает длительную задачу или задачу однократного запуска. Например, политику перезагрузки `Never` или `OnFailure` рекомендуется использовать для однократного запуска задачи. 

* Если вам нужна информация об точку входа по умолчанию, установите в образ контейнера, используйте [проверьте образ docker](https://docs.docker.com/engine/reference/commandline/image_inspect/) команды.

## <a name="command-line-syntax"></a>Синтаксис командной строки

Синтаксис командной строки зависит от Azure API или средство, используемое для создания экземпляров. Если указать среду оболочки также заметили соглашения о синтаксисе командной оболочки.

* [Создание контейнера AZ] [ az-container-create] команды: Передайте строку `--command-line` параметра. Пример: `--command-line "python myscript.py arg1 arg2"`).

* [Новый-AzureRmContainerGroup] [ new-azurermcontainergroup] командлет Azure PowerShell: Передайте строку `-Command` параметра. Пример: `-Command "echo hello"`.

* Портал Azure: В **переопределяют** свойство конфигурации контейнера, укажите разделенный запятыми список строк, без кавычек. Пример: `python, myscript.py, arg1, arg2`). 

* Шаблон Resource Manager или yaml-файл или пакетов SDK для Azure: Укажите свойство командной строки как массив строк. Пример: массив JSON `["python", "myscript.py", "arg1", "arg2"]` в шаблоне Resource Manager. 

  Если вы знакомы с [Dockerfile](https://docs.docker.com/engine/reference/builder/) синтаксис, этот формат аналогичен *exec* инструкции CMD форме.

### <a name="examples"></a>Примеры

|    |  Инфраструктура CLI Azure   | Портал | Шаблон | 
| ---- | ---- | --- | --- |
| Одной команды | `--command-line "python myscript.py arg1 arg2"` | **Команда переопределение**: `python, myscript.py, arg1, arg2` | `"command": ["python", "myscript.py", "arg1", "arg2"]` |
| Несколько команд | `--command-line "/bin/bash -c 'mkdir test; touch test/myfile; tail -f /dev/null'"` |**Команда переопределение**: `/bin/bash, -c, mkdir test; touch test/myfile; tail -f /dev/null` | `"command": ["/bin/bash", "-c", "mkdir test; touch test/myfile; tail -f /dev/null"]` |

## <a name="azure-cli-example"></a>Пример для Azure CLI

Например, изменения поведения [aci/microsoft-wordcount] [ aci-wordcount] образ контейнера, который анализирует текст в Шекспира *Гамлет* для поиска наиболее часто встречающиеся слова. Вместо того чтобы анализировать *Гамлет*, можно задать командную строку, которая указывает на источник другим текстом.

Для просмотра выходных данных [aci/microsoft-wordcount] [ aci-wordcount] контейнера, когда он анализирует текст по умолчанию, запустите его с помощью следующих [создать контейнер az] [ az-container-create] команды. Указывается без запуска командной строки, поэтому выполняется команда контейнера по умолчанию. Для наглядности, в этом примере устанавливается [переменные среды](container-instances-environment-variables.md) найти первые три слова, по крайней мере пять букв long:

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer1 \
    --image mcr.microsoft.com/azuredocs/aci-wordcount:latest \
    --environment-variables NumWords=3 MinLength=5 \
    --restart-policy OnFailure
```

Когда состояние контейнера *Terminated* (использовать [az контейнера show] [ az-container-show] для проверки состояния), отобразить журнал с [журналы контейнеров az] [ az-container-logs] просмотр выходных данных.

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer1
```

Выходные данные:

```console
[('HAMLET', 386), ('HORATIO', 127), ('CLAUDIUS', 120)]
```

Теперь настройте второй пример контейнер для анализа различных текста, указав новую командную строку. Скрипт Python в качестве контейнера, *wordcount.py*, URL-адрес в качестве аргумента принимает и обрабатывает содержимое этой страницы, вместо значения по умолчанию.

Например, для определения верхней 3 слов, которые менее пяти буквы длинное в *Ромео и Джульетта*:

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

Сценарии, основанные на задачах, такие как пакетная обработка большого набора данных с несколькими контейнерами, могут использовать пользовательские командные строки во время выполнения. Дополнительные сведения о запуске контейнеров на основе задач см. в разделе [выполнение контейнерных задач с помощью политики перезапуска](container-instances-restart-policy.md).

<!-- LINKS - External -->
[aci-wordcount]: https://hub.docker.com/_/microsoft-azuredocs-aci-wordcount

<!-- LINKS Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show
[new-azurermcontainergroup]: /powershell/module/azurerm.containerinstance/new-azurermcontainergroup
[portal]: https://portal.azure.com
