---
title: Мониторинг кластера DC/OS Azure с помощью Datadog (не рекомендуется)
description: Мониторинг кластера службы контейнеров Azure с помощью Datadog. Использование веб-интерфейса DC/OS для развертывания агентов Datadog в кластере.
author: sauryadas
ms.service: container-service
ms.topic: conceptual
ms.date: 07/28/2016
ms.author: saudas
ms.custom: mvc
ms.openlocfilehash: fcb005e39f89298b35bf0f3a0ad1e19601ae4d13
ms.sourcegitcommit: 6a4fbc5ccf7cca9486fe881c069c321017628f20
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/27/2020
ms.locfileid: "82166157"
---
# <a name="deprecated-monitor-an-azure-container-service-dcos-cluster-with-datadog"></a>Мониторинг кластера DC/OS в Службе контейнеров Azure с помощью Datadog (не рекомендуется)

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

В этой статье описывается развертывание агентов Datadog на всех узлах агентов в кластере службы контейнеров Azure. Для работы с этой конфигурацией вам понадобится учетная запись с Datadog. 

## <a name="prerequisites"></a>Предварительные требования
[Разверните](container-service-deployment.md) и [подключите](../container-service-connect.md) кластер, настроенный службой контейнеров Azure. Изучите [пользовательский интерфейс Marathon](container-service-mesos-marathon-ui.md). Перейдите к [https://datadoghq.com](https://datadoghq.com) , чтобы настроить учетную запись Datadog. 

## <a name="datadog"></a>Datadog
Datadog представляет собой службу мониторинга, которая собирает данные мониторинга из контейнеров в кластере службы контейнеров Azure. Datadog имеет панель мониторинга интеграции с Docker, в которой вы можете увидеть некоторые метрики своих контейнеров. Метрики контейнеров собраны в несколько групп: ЦП, память, сеть и ввод-вывод. Datadog разделяет метрики по контейнерам и образам. Ниже представлен пример пользовательского интерфейса для метрик использования ЦП.

![Пользовательский интерфейс Datadog](./media/container-service-monitoring/datadog4.png)

## <a name="configure-a-datadog-deployment-with-marathon"></a>Настройка развертывания Datadog с помощью Marathon
Ниже описано, как настраивать и развертывать в кластере приложения Datadog с помощью Marathon. 

Откройте пользовательский интерфейс DC/OS по адресу `http://localhost:80/`. В этом интерфейсе щелкните Universe (Среда) в левом нижнем углу, выполните поиск по запросу "Datadog" и щелкните "Установить".

![Пакет Datadog в среде DC/ОС](./media/container-service-monitoring/datadog1.png)

Чтобы завершить настройку, вам понадобится обычная или бесплатная пробная учетная запись Datadog. Войдите на веб-сайт Datadog с этой учетной записью, и на левой панели щелкните "Integrations" (Интеграции) > [APIs](https://app.datadoghq.com/account/settings#api) (Интерфейсы API). 

![Ключ API Datadog](./media/container-service-monitoring/datadog2.png)

Введите ключ API в конфигурацию Datadog в разделе Universe (Среда) DC/OS. 

![Datadog: конфигурация в разделе Universe (Среда) DC/OS](./media/container-service-monitoring/datadog3.png) 

В примере выше для количества экземпляров задано значение 10000000, чтобы при каждом добавлении узла в кластер служба Datadog автоматически выполняла развертывание агента в этом узле. Это промежуточное решение. После установки пакета вернитесь на веб-сайт Datadog и найдите раздел [Dashboards](https://app.datadoghq.com/dash/list) (Панели мониторинга). Здесь вы увидите пользовательские панели мониторинга и панели интеграции. [Панель мониторинга Docker](https://app.datadoghq.com/screen/integration/docker) содержит все метрики контейнера, необходимые для наблюдения за кластером. 

