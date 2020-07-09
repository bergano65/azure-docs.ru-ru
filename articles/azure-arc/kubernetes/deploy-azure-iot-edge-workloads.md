---
title: Развертывание рабочих нагрузок Azure IoT Edge (предварительная версия)
services: azure-arc
ms.service: azure-arc
ms.date: 05/19/2020
ms.topic: article
author: mlearned
ms.author: mlearned
description: Развертывание рабочих нагрузок Azure IoT Edge
keywords: Kubernetes, Arc, Azure, K8s, контейнеры
ms.openlocfilehash: bfaa43a03ddd98616b22fc3fc7b4dccb4c38f44c
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/08/2020
ms.locfileid: "86103982"
---
# <a name="deploy-azure-iot-edge-workloads-preview"></a>Развертывание рабочих нагрузок Azure IoT Edge (предварительная версия)

## <a name="overview"></a>Обзор

Azure Arc и Azure IoT Edge дополняют возможности друг друга. Служба Azure Arc предоставляет операторам кластера механизмы для настройки базовых компонентов кластера, а также для применения политик кластера. IoT Edge позволяет операторам приложений удаленно развертывать рабочие нагрузки и управлять ими в масштабе с помощью удобного приема данных из облака и примитивов с двунаправленной связью. Использование аналитики представлено на схеме ниже.

![Настройка IoT Arc](./media/edge-arc.png)

## <a name="pre-requisites"></a>Предварительные требования

* [Зарегистрируйте устройства IoT Edge ](../../iot-edge/quickstart-linux.md#register-an-iot-edge-device) и [разверните имитируемый модуль датчика температуры](../../iot-edge/quickstart-linux.md#deploy-a-module). Обязательно запишите строку подключения устройства.

* Для развертывания Kubernetes с помощью оператора Flux в Azure Arc используйте [поддержку IoT Edge](https://aka.ms/edgek8sdoc).

* Скачайте файл [**values.yaml**](https://github.com/Azure/iotedge/blob/master/kubernetes/charts/edge-kubernetes/values.yaml) для диаграммы IoT Edge Helm и замените заполнитель **deviceConnectionString** в конце файла на тот, который указан на шаге 1. При необходимости можно задать любые другие поддерживаемые параметры установки диаграммы. Создайте пространство имен для рабочей нагрузки IoT Edge и создайте в ней секрет:

    ```
    $ kubectl create ns iotedge

    $ kubectl create secret generic dcs --from-file=fully-qualified-path-to-values.yaml --namespace iotedge
    ```

    Кроме того, это можно сделать удаленно с помощью [примера конфигурации кластера](./use-gitops-connected-cluster.md).

## <a name="connect-a-cluster"></a>Подключение кластера

Используйте расширение `az` CLI `connectedk8s` для подключения кластера Kubernetes к службе Azure Arc:

  ```
  az connectedk8s connect --name AzureArcIotEdge --resource-group AzureArcTest
  ```

## <a name="create-a-configuration-for-iot-edge"></a>Создание конфигурации для IoT Edge

Пример репозитория: https://github.com/veyalla/edgearc

Этот репозиторий указывает на диаграмму IoT Edge Helm и ссылается на секрет, созданный в разделе "Предварительные требования".

1. Используйте расширение `az` CLI `k8sconfiguration`, чтобы создать конфигурацию для связывания подключенного кластера с репозиторием Git:

    ```
    az k8sconfiguration create --name iotedge --cluster-name AzureArcIotEdge --resource-group AzureArcTest --operator-instance-name iotedge --operator-namespace azure-arc-iot-edge --enable-helm-operator --helm-operator-chart-version 0.6.0 --helm-operator-chart-values "--set helm.versions=v3" --repository-url "git://github.com/veyalla/edgearc.git" --cluster-scoped
    ```

    Через пару минут вы увидите, что модули рабочей нагрузки IoT Edge развернуты в пространстве имен `iotedge` в кластере. Чтобы просмотреть примеры генерируемых значений, можно обратиться к журналам объекта pod `SimulatedTemperatureSensor`. Вы также можете просматривать сообщения, которые поступают в Центр Интернета вещей, используя [расширение Azure IoT Hub Toolkit для Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit).

## <a name="cleanup"></a>Очистка

Конфигурацию можно удалить с помощью следующей команды:

```
az k8sconfiguration delete -g AzureArcTest --cluster-name AzureArcIotEdge --name iotedge
```

## <a name="next-steps"></a>Дальнейшие действия

[Использование Политики Azure для управления конфигураций кластера](./use-azure-policy.md)
