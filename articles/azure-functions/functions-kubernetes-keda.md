---
title: Функции Azure на Kubernetes с KEDA
description: Понять, как запускать функции Azure в Kubernetes в облаке или на месте с помощью KEDA, kubernetes на основе автоматического масштабирования событий.
author: jeffhollan
ms.topic: conceptual
ms.date: 11/18/2019
ms.author: jehollan
ms.openlocfilehash: 2c06fdba8f60243acf4e0fabd23df8b832c210db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78301681"
---
# <a name="azure-functions-on-kubernetes-with-keda"></a>Функции Azure на Kubernetes с KEDA

Время выполнения функций Azure обеспечивает гибкость в хостинге, где и как вы хотите.  [KEDA](https://keda.sh) (Kubernetes на основе Event Driven Autoscaling) плавно сочетается с временем выполнения функций Azure и инструментарием, чтобы обеспечить масштаб событий в Kubernetes.

## <a name="how-kubernetes-based-functions-work"></a>Как работают функции, основанные на Kubernetes

Служба Функции Azure состоит из двух ключевых компонентов: времени выполнения и контроллера масштаба.  Время выполнения функций выполняется и выполняет сядек.  Время выполнения включает в себя логику, как вызвать, войти и управлять выполнением функций.  Время выполнения функций Azure может выполняться *в любом месте.*  Другим компонентом является контроллер масштаба.  Контроллер масштаба отслеживает скорость событий, ориентированных на вашу функцию, и активно масштабирует количество экземпляров, запускаемых приложением.  Дополнительные сведения см. в статье [Масштабирование и размещение Функций Azure](functions-scale.md).

Функции, основанные на Kubernetes, обеспечивают время выполнения функций в [контейнере Docker](functions-create-function-linux-custom-image.md) с масштабированием на основе событий через KEDA.  KEDA может масштабироваться до 0 экземпляров (когда никаких событий не происходит) и до *n* экземпляров. Он делает это, подвергая пользовательские метрики для kubernetes autoscaler (Horizontal Pod Autoscaler).  Использование контейнеров Функции с KEDA позволяет воспроизводить возможности серверных функций в любом кластере Kubernetes.  Эти функции также могут быть развернуты с помощью [функции виртуальных узлов Azure Kubernetes Services (AKS)](../aks/virtual-nodes-cli.md) для инфраструктуры без серверов.

## <a name="managing-keda-and-functions-in-kubernetes"></a>Управление KEDA и функции в Kubernetes

Для выполнения функций в кластере Kubernetes необходимо установить компонент KEDA. Вы можете установить этот компонент с помощью [основных инструментов Azure Functions.](functions-run-local.md)

### <a name="installing-with-helm"></a>Установка с шлемом

Существуют различные способы установки KEDA в любом кластере Kubernetes, включая Helm.  Параметры развертывания задокументированы на [сайте KEDA.](https://keda.sh/deploy/)

## <a name="deploying-a-function-app-to-kubernetes"></a>Развертывание приложения функции в Kubernetes

Можно развернуть любое функциональное приложение в кластер Kubernetes под управлением KEDA.  Так как ваши функции работают в `Dockerfile`контейнере Docker, ваш проект нуждается в .  Если его еще нет, можно добавить Dockerfile, запустив следующую команду в корне проекта Функции:

```cli
func init --docker-only
```

Чтобы создать изображение и развернуть свои функции в Kubernetes, запустите следующую команду:

> [!NOTE]
> Основные инструменты будут использовать докер CLI для создания и публикации изображения. Обязательно установить докер уже и подключиться `docker login`к вашей учетной записи с .

```cli
func kubernetes deploy --name <name-of-function-deployment> --registry <container-registry-username>
```

> Замените `<name-of-function-deployment>` на имя приложения-функции.

Это создает ресурс `Deployment` Kubernetes, `ScaledObject` ресурс `Secrets`и , который включает переменные среды, импортируемые из файла. `local.settings.json`

### <a name="deploying-a-function-app-from-a-private-registry"></a>Развертывание функционального приложения из частного реестра

Вышеуказанный поток работает и для частных реестров.  Если вы тянете ваш контейнер изображения из `--pull-secret` частного реестра, включите флаг, который ссылается `func kubernetes deploy`на Kubernetes секрет проведения частных учетных данных реестра при запуске.

## <a name="removing-a-function-app-from-kubernetes"></a>Удаление приложения функции из Kubernetes

После развертывания можно удалить функцию, удалив `Secrets` связанную `Deployment`с `ScaledObject`ним возможность.

```cli
kubectl delete deploy <name-of-function-deployment>
kubectl delete ScaledObject <name-of-function-deployment>
kubectl delete secret <name-of-function-deployment>
```

## <a name="uninstalling-keda-from-kubernetes"></a>Установка KEDA из Кубернете

Шаги по удалению KEDA задокументированы [на сайте KEDA.](https://keda.sh/deploy/)

## <a name="supported-triggers-in-keda"></a>Поддерживаемые триггеры в KEDA

KEDA поддерживает следующие триггеры функции Azure:

* [Очереди службы хранилища Azure](functions-bindings-storage-queue.md)
* [Очереди автобусов Azure](functions-bindings-service-bus.md)
* [Событие Azure / Концентраторы IoT](functions-bindings-event-hubs.md)
* [Апач Кафка](https://github.com/azure/azure-functions-kafka-extension)
* [Очередь КроликаМЗ](https://github.com/azure/azure-functions-rabbitmq-extension)

### <a name="http-trigger-support"></a>Поддержка триггера HTTP

Функции Azure могут разоблачать триггеры HTTP, но KEDA напрямую ими не управляет.  Вы можете использовать триггер KEDA prometheus для [масштабирования функций HTTP Azure от 1 до *n* экземпляров.](https://dev.to/anirudhgarg_99/scale-up-and-down-a-http-triggered-function-app-in-kubernetes-using-keda-4m42)

## <a name="next-steps"></a>Next Steps
Дополнительные сведения см. в следующих ресурсах:

* [Создание функции с помощью пользовательского изображения](functions-create-function-linux-custom-image.md)
* [Как программировать и тестировать функции Azure в локальной среде](functions-develop-local.md)
* [Как работает план потребления функций Azure](functions-scale.md)