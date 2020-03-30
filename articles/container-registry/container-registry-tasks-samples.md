---
title: Образцы задач ACR
description: Пример задач реестра контейнеров Azure (ACR Tasks) для создания, выполнения и патча изображений контейнеров
ms.topic: article
ms.date: 11/14/2019
ms.openlocfilehash: 49df3bf565052a729ac3c587bd2ba11a299d05f1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74456079"
---
# <a name="acr-tasks-samples"></a>Образцы задач ACR

В этой статье `task.yaml` ссылки на примеры файлов и связанных с ними Dockerfiles для нескольких [задач реестра контейнеров Azure](container-registry-tasks-overview.md) (ACR Tasks) сценариев. 

Дополнительные примеры можно найти в репо [образцов Azure.][task-examples]

## <a name="scenarios"></a>Сценарии

* **Сборка изображения** - [YAML](https://github.com/Azure-Samples/acr-tasks/blob/master/build-hello-world.yaml), [Dockerfile](https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.dockerfile)

* **Выполнить контейнер** - [YAML](https://github.com/Azure-Samples/acr-tasks/blob/master/bash-echo.yaml)

* **Сборка и нажатие изображения** - [YAML](https://github.com/Azure-Samples/acr-tasks/blob/master/build-push-hello-world.yaml), [Dockerfile](https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.dockerfile)

* **Сборка и запуск изображения** - [YAML](https://github.com/Azure-Samples/acr-tasks/blob/master/build-run-hello-world.yaml), [Dockerfile](https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.dockerfile)

* **Сборка и нажатие нескольких изображений** -  [YAML](https://github.com/Azure-Samples/acr-tasks/blob/master/build-push-hello-world-multi.yaml), [Dockerfile](https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.dockerfile)

* **Сборка и тестирование изображений в параллельных** -  [YAML](https://github.com/Azure-Samples/acr-tasks/blob/master/when-parallel.yaml), [Dockerfile](https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.dockerfile)

* **Сборка и нажатие изображений в несколько реестров** - [YAML](https://github.com/Azure-Samples/acr-tasks/blob/master/multipleRegistries/testtask.yaml), [Dockerfile](https://github.com/Azure-Samples/acr-tasks/blob/master/multipleRegistries/hello-world.dockerfile)


## <a name="next-steps"></a>Дальнейшие действия

Подробнее о задачах ACR:

* [Многоступенчатые задачи](container-registry-tasks-multi-step.md) - рабочие процессы на основе ACR для создания, тестирования и исправления изображений контейнеров в облаке.
* [Справочник по задачам](container-registry-tasks-reference-yaml.md). Типы шагов задач, их свойства и использование.
* [Cmd REPO](https://github.com/AzureCR/cmd) - Сбор контейнеров в качестве команд для задач ACR.


<!-- LINKS - External -->
[task-examples]: https://github.com/Azure-Samples/acr-tasks
