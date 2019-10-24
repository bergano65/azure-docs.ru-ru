---
title: Использование командной строки запуска в службе "экземпляры контейнеров Azure"
description: Переопределение точки входа, настроенной в образе контейнера при развертывании экземпляра контейнера Azure
services: container-instances
author: dlepow
manager: gwallace
ms.service: container-instances
ms.topic: article
ms.date: 04/15/2019
ms.author: danlep
ms.openlocfilehash: 40d946db48a65452d2da529098c07d0d0c60d472
ms.sourcegitcommit: 08d3a5827065d04a2dc62371e605d4d89cf6564f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/29/2019
ms.locfileid: "68619664"
---
# <a name="set-the-command-line-in-a-container-instance-to-override-the-default-command-line-operation"></a>Установка командной строки в экземпляре контейнера для переопределения операции командной строки по умолчанию

При создании экземпляра контейнера при необходимости можно указать команду для переопределения инструкции командной строки по умолчанию, помогут в образ контейнера. Это поведение аналогично `--entrypoint` аргументу командной строки для `docker run`.

Как и при настройке [переменных среды](container-instances-environment-variables.md) для экземпляров контейнеров, указание начальной командной строки полезно для пакетных заданий, в которых необходимо динамически подготовить каждый контейнер с помощью конфигурации конкретной задачи.

## <a name="command-line-guidelines"></a>Рекомендации по использованию командной строки

* По умолчанию командная строка указывает *один процесс, который запускается без оболочки* в контейнере. Например, Командная строка может выполнять сценарий Python или исполняемый файл. В процессе можно указать дополнительные параметры или аргументы.

* Чтобы выполнить несколько команд, запустите командную строку, задав среду оболочки, которая поддерживается в операционной системе контейнера. Примеры:

  |Операционная система  |Оболочка по умолчанию  |
  |---------|---------|
  |Ubuntu     |   `/bin/bash`      |
  |Alpine     |   `/bin/sh`      |
  |Windows     |    `cmd`     |

  Следуйте соглашениям оболочки, чтобы объединить несколько команд для последовательного выполнения.

* В зависимости от конфигурации контейнера может потребоваться задать полный путь к исполняемому файлу или аргументам командной строки.

* Задайте соответствующую [политику перезапуска](container-instances-restart-policy.md) для экземпляра контейнера в зависимости от того, указывает ли Командная строка длительное выполнение задачи или выполнение задачи однократной работы. Например, для задачи однократного выполнения `Never` рекомендуется `OnFailure` использовать политику перезапуска или. 

* Если вам нужны сведения о точке входа по умолчанию, заданной в образе контейнера, используйте команду [проверки образа DOCKER](https://docs.docker.com/engine/reference/commandline/image_inspect/) .

## <a name="command-line-syntax"></a>Синтаксис командной строки

Синтаксис командной строки зависит от интерфейса API или средства Azure, используемого для создания экземпляров. Если вы указали среду оболочки, также следите за синтаксисом команд оболочки.

* Команда [AZ Container Create][az-container-create] : Передайте строку с `--command-line` параметром. Пример: `--command-line "python myscript.py arg1 arg2"`).

* [New-AzureRmContainerGroup][new-azurermcontainergroup] Azure PowerShell командлет: Передайте строку с `-Command` параметром. Пример: `-Command "echo hello"`.

* Портал Azure: В свойстве **Переопределение команды** в конфигурации контейнера Укажите разделенный запятыми список строк без кавычек. Пример: `python, myscript.py, arg1, arg2`). 

* Диспетчер ресурсов шаблон или файл YAML или один из пакетов SDK для Azure: Укажите свойство командной строки в виде массива строк. Пример. массив `["python", "myscript.py", "arg1", "arg2"]` JSON в шаблоне диспетчер ресурсов. 

  Если вы знакомы с синтаксисом [Dockerfile](https://docs.docker.com/engine/reference/builder/) , этот формат аналогичен форме *exec* инструкции cmd.

### <a name="examples"></a>Примеры

|    |  Azure CLI   | Портал | Шаблон | 
| ---- | ---- | --- | --- |
| Одна команда | `--command-line "python myscript.py arg1 arg2"` | **Переопределение команды**:`python, myscript.py, arg1, arg2` | `"command": ["python", "myscript.py", "arg1", "arg2"]` |
| Несколько команд | `--command-line "/bin/bash -c 'mkdir test; touch test/myfile; tail -f /dev/null'"` |**Переопределение команды**:`/bin/bash, -c, mkdir test; touch test/myfile; tail -f /dev/null` | `"command": ["/bin/bash", "-c", "mkdir test; touch test/myfile; tail -f /dev/null"]` |

## <a name="azure-cli-example"></a>Пример для Azure CLI

В качестве примера измените поведение образа контейнера [Microsoft/ACI-WordCount][aci-wordcount] , который анализирует текст в *Гамлет* Шекспир для поиска наиболее часто встречающихся слов. Вместо анализа *Гамлет*можно задать командную строку, которая указывает на другой источник текста.

Чтобы просмотреть выходные данные контейнера [Microsoft/ACI-WordCount][aci-wordcount] при анализе текста по умолчанию, запустите его, выполнив следующую команду [AZ Container Create][az-container-create] . Не указана Командная строка запуска, поэтому выполняется команда контейнера по умолчанию. Для иллюстрации в этом примере задаются [переменные среды](container-instances-environment-variables.md) для поиска первых 3 слов, которые имеют длину не менее пяти букв:

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer1 \
    --image mcr.microsoft.com/azuredocs/aci-wordcount:latest \
    --environment-variables NumWords=3 MinLength=5 \
    --restart-policy OnFailure
```

Когда состояние контейнера будет отображаться как *завершенное* (используйте команду [AZ Container Показать][az-container-show] для проверки состояния), отобразите журнал с помощью команды [AZ Container Logs][az-container-logs] , чтобы просмотреть выходные данные.

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer1
```

Выходные данные:

```console
[('HAMLET', 386), ('HORATIO', 127), ('CLAUDIUS', 120)]
```

Теперь настройте второй контейнер примеров для анализа другого текста, указав другую командную строку. Скрипт Python, выполняемый контейнером *WordCount.py*, принимает URL-адрес в качестве аргумента и обрабатывает содержимое страницы вместо значения по умолчанию.

Например, чтобы определить первые 3 слова, которые имеют длину не менее пяти букв в *тексте пьесы Ромео и Джульетта*:

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

## <a name="next-steps"></a>Следующие шаги

Сценарии, основанные на задачах, такие как пакетная обработка большого набора данных с несколькими контейнерами, могут использовать преимущества настраиваемых командных строк во время выполнения. Дополнительные сведения о выполнении контейнеров на основе задач см. [в статье выполнение контейнерных задач с помощью политик перезапуска](container-instances-restart-policy.md).

<!-- LINKS - External -->
[aci-wordcount]: https://hub.docker.com/_/microsoft-azuredocs-aci-wordcount

<!-- LINKS Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show
[new-azurermcontainergroup]: /powershell/module/azurerm.containerinstance/new-azurermcontainergroup
[portal]: https://portal.azure.com
