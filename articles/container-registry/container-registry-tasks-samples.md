---
title: Примеры задач реестра контейнеров Azure
description: Примеры задач реестра контейнеров Azure (задачи записи контроля доступа) для создания, запуска и исправления образов контейнеров
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 11/14/2019
ms.author: danlep
ms.openlocfilehash: 488e13ed0d1961fbafad545057accb61957a7005
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/17/2019
ms.locfileid: "74152857"
---
# <a name="acr-tasks-samples"></a>Примеры задач записи контроля доступа

В этой статье приводятся ссылки на примеры `task.yaml` файлов и связанных с ними файлы dockerfile для нескольких [задач реестра контейнеров Azure](container-registry-tasks-overview.md) (задач записи контроля доступа). 

Дополнительные примеры см. в репозитории [примеров Azure][task-examples] .

## <a name="scenarios"></a>Сценарии

* **Образ сборки** - [YAML](https://github.com/Azure-Samples/acr-tasks/blob/master/build-hello-world.yaml), [Dockerfile](https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.dockerfile)

* **Запустить контейнер** - [YAML](https://github.com/Azure-Samples/acr-tasks/blob/master/bash-echo.yaml)

* **Сборка и отправка образа** - [YAML](https://github.com/Azure-Samples/acr-tasks/blob/master/build-push-hello-world.yaml), [Dockerfile](https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.dockerfile)

* **Сборка и запуск образа** - [YAML](https://github.com/Azure-Samples/acr-tasks/blob/master/build-run-hello-world.yaml), [Dockerfile](https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.dockerfile)

* **Создание и отправка нескольких образов** -  [YAML](https://github.com/Azure-Samples/acr-tasks/blob/master/build-push-hello-world-multi.yaml), [Dockerfile](https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.dockerfile)

* **Создавайте и тестируйте образы параллельно** -  [YAML](https://github.com/Azure-Samples/acr-tasks/blob/master/when-parallel.yaml), [Dockerfile](https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.dockerfile)

* **Создание и отправка образов в несколько реестров** - [YAML](https://github.com/Azure-Samples/acr-tasks/blob/master/multipleRegistries/testtask.yaml), [Dockerfile](https://github.com/Azure-Samples/acr-tasks/blob/master/multipleRegistries/hello-world.dockerfile)


## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о задачах контроля доступа:

* [Многоэтапные задачи](container-registry-tasks-multi-step.md) — рабочие процессы на основе задач записи контроля доступа для сборки, тестирования и исправления образов контейнеров в облаке.
* [Справочник по задачам](container-registry-tasks-reference-yaml.md). Типы шагов задач, их свойства и использование.
* [Репозиторий cmd](https://github.com/AzureCR/cmd) — коллекция контейнеров в виде команд для задач контроля доступа.


<!-- LINKS - External -->
[task-examples]: https://github.com/Azure-Samples/acr-tasks
