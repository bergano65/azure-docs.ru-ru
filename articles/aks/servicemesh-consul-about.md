---
title: Общие сведения о Consul
description: Получите общие сведения о Consul
author: paulbouwer
ms.topic: article
ms.date: 10/09/2019
ms.author: pabouwer
ms.openlocfilehash: c518985b360fa3264bd5ac1e3fe76d61b2810b9b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "77594215"
---
# <a name="consul"></a>Consul

## <a name="overview"></a>Обзор

[Consul][consul] — это решение для работы с несколькими центрами обработки данных, которое позволяет подключать и защищать службы на разных платформах среды выполнения. [Connect][consul-features] — это компонент, предоставляющий возможности сетки служб.

## <a name="architecture"></a>Architecture

Consul предоставляет плоскость данных, состоящую из [сидекарс][consul-sidecar] на основе [делегатов][envoy-proxy]по умолчанию. Consul имеет подключаемую архитектуру прокси-сервера. Эти интеллектуальные прокси-серверы контролируют весь входящий и исходящий сетевой трафик приложений и рабочих нагрузок в сети.

Плоскость управления управляет конфигурацией и политикой с помощью следующих [компонентов][consul-architecture]:

- **Сервер** — агент Consul, работающий в режиме сервера, который поддерживает состояние кластера Consul.

- **Клиент** — агент Consul, работающий в режиме упрощенного клиента. На каждом кластерном узле должен выполняться агент клиента. Эта конфигурация клиентских посредников и политика между рабочими нагрузками и конфигурацией Consul. 

На следующей схеме архитектуры показано взаимодействие различных компонентов в плоскости данных и плоскости управления.

![Общие сведения о компонентах и архитектуре Consul.](media/servicemesh/consul/about-architecture.png)


## <a name="selection-criteria"></a>Критерии выбора

При оценке Consul для рабочих нагрузок важно понимать и учитывать следующие аспекты:

- [Принципы Consul](#consul-principles)
- [Возможности](#capabilities)
- [Сценарии](#scenarios)


### <a name="consul-principles"></a>Принципы Consul

Ниже [описаны][consul-principles] принципы проекта Consul.

- Настройка и политика на **основе API** -выражать.

- **Запускайте и подключайте** рабочие нагрузки подключения в любом месте во время выполнения платформ (Kubernetes, виртуальные машины, бессерверные).

- **Расширяйте и интегрируйте** безопасные рабочие нагрузки подключения между инфраструктурой.


### <a name="capabilities"></a>Возможности

Consul предоставляет следующий набор возможностей:

- **Сетка** — шлюз (несколько центров обработки данных), виртуальные машины (из узлов кластера), Синхронизация службы, встроенный параметр отладки

- Прокси- **серверы** — делегат, встроенный прокси-сервер, подключаемый сервер уровня 4, доступный для рабочих нагрузок Windows

- **Управление трафиком** — маршрутизация, разделение, разрешение

- **Политика** — намерения, списки управления доступом

- **Безопасность** — авторизации, проверка подлинности, шифрование, удостоверения на основе спиффе, внешний ЦС (хранилище), Управление сертификатами и вращение

- **Наблюдаемость** — метрики, панель мониторинга пользовательского интерфейса, Prometheus, grafana


### <a name="scenarios"></a>Сценарии

Consul хорошо подходит и предлагается для следующих сценариев:

- Расширение существующих подключенных рабочих нагрузок Consul

- Требования к соответствию требованиям для управления сертификатами

- Сетка службы с несколькими кластерами

- Рабочие нагрузки на основе виртуальных машин для включения в сеть службы



## <a name="next-steps"></a>Дальнейшие шаги

В следующей документации описывается, как можно установить Consul в службе Kubernetes Azure (AKS).

> [!div class="nextstepaction"]
> [Установка Consul в службе Kubernetes Azure (AKS)][consul-install]

Кроме того, вы можете дополнительно исследовать функции и архитектуру Consul:

- [Функции Consul][consul-features]
- [Архитектура Consul][consul-architecture]
- [Consul — как работает подключение][consul-how-connect-works]

<!-- LINKS - external -->
[consul]: https://www.consul.io/mesh.html
[consul-features]: https://www.consul.io/docs/connect/index.html
[consul-architecture]: https://www.consul.io/docs/internals/architecture.html
[consul-sidecar]: https://www.consul.io/docs/connect/proxies.html
[consul-how-connect-works]: https://www.consul.io/docs/connect/connect-internals.html
[consul-principles]: https://www.consul.io/

[envoy-proxy]: https://www.envoyproxy.io/
[grafana]: https://grafana.com/
[prometheus]: https://prometheus.io/

<!-- LINKS - internal -->
[consul-install]: ./servicemesh-consul-install.md
