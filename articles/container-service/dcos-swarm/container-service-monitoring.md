---
title: Мониторинг кластера DC/OS Azure с помощью Datadog (не рекомендуется)
description: Мониторинг кластера службы контейнеров Azure с помощью Datadog. Использование веб-интерфейса DC/OS для развертывания агентов Datadog в кластере.
services: container-service
author: sauryadas
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 07/28/2016
ms.author: saudas
ms.custom: mvc
ms.openlocfilehash: a094369d467b3b1f3d5fe93f870dccc9eae7519c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60508121"
---
# <a name="deprecated-monitor-an-azure-container-service-dcos-cluster-with-datadog"></a>Мониторинг кластера DC/OS в Службе контейнеров Azure с помощью Datadog (не рекомендуется)

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

В этой статье описывается развертывание агентов Datadog на всех узлах агентов в кластере службы контейнеров Azure. Для работы с этой конфигурацией вам понадобится учетная запись с Datadog. 

## <a name="prerequisites"></a>Технические условия
[Разверните](container-service-deployment.md) и [подключите](../container-service-connect.md) кластер, настроенный службой контейнеров Azure. Изучите [пользовательский интерфейс Marathon](container-service-mesos-marathon-ui.md). Перейдите по адресу [https://datadoghq.com](https://datadoghq.com) для настройки учетной записи Datadog. 

## <a name="datadog"></a>Datadog
Datadog представляет собой службу мониторинга, которая собирает данные мониторинга из контейнеров в кластере службы контейнеров Azure. Datadog имеет панель мониторинга интеграции с Docker, в которой вы можете увидеть некоторые метрики своих контейнеров. Метрики контейнеров собраны в несколько групп: ЦП, память, сеть и ввод-вывод. Datadog разделяет метрики по контейнерам и образам. Ниже представлен пример пользовательского интерфейса для метрик использования ЦП.

![Пользовательский интерфейс Datadog](./media/container-service-monitoring/datadog4.png)

## <a name="configure-a-datadog-deployment-with-marathon"></a>Настройка развертывания Datadog с помощью Marathon
Ниже описано, как настраивать и развертывать в кластере приложения Datadog с помощью Marathon. 

Откройте пользовательский интерфейс DC/OS по адресу [http://localhost:80/](http://localhost:80/). В этом интерфейсе щелкните Universe (Среда) в левом нижнем углу, выполните поиск по запросу "Datadog" и щелкните "Установить".

![Пакет Datadog в среде DC/ОС](./media/container-service-monitoring/datadog1.png)

Чтобы завершить настройку, вам понадобится обычная или бесплатная пробная учетная запись Datadog. Войдите на веб-сайт Datadog с этой учетной записью, и на левой панели щелкните "Integrations" (Интеграции) > [APIs](https://app.datadoghq.com/account/settings#api) (Интерфейсы API). 

![Ключ API Datadog](./media/container-service-monitoring/datadog2.png)

Введите ключ API в конфигурацию Datadog в разделе Universe (Среда) DC/OS. 

![Datadog: конфигурация в разделе Universe (Среда) DC/OS](./media/container-service-monitoring/datadog3.png) 

В примере выше для количества экземпляров задано значение 10000000, чтобы при каждом добавлении узла в кластер служба Datadog автоматически выполняла развертывание агента в этом узле. Это промежуточное решение. После установки пакета вернитесь на веб-сайт Datadog и найдите раздел [Dashboards](https://app.datadoghq.com/dash/list) (Панели мониторинга). Здесь вы увидите пользовательские панели мониторинга и панели интеграции. [Панель мониторинга Docker](https://app.datadoghq.com/screen/integration/docker) содержит все метрики контейнера, необходимые для наблюдения за кластером. 

