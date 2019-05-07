---
title: Как установить Edge Интернета вещей в Kubernetes | Документация Майкрософт
description: Дополнительные сведения о том, как установить Edge Интернета вещей на платформе Kubernetes в среде кластера локальной разработки
author: kgremban
manager: philmea
ms.author: veyalla
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 66aca7be9a2df93d846d7e78bc64c93279afc2d1
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65160700"
---
# <a name="how-to-install-iot-edge-on-kubernetes-preview"></a>Инструкции по установке IoT Edge на Kubernetes (Предварительная версия)

IoT Edge можно интегрировать с Kubernetes, используя его в качестве надежных, высокодоступных инфраструктуры слоя. Он регистрирует IoT Edge *пользовательское определение ресурсов* (CRD) с сервера API Kubernetes. Кроме того, он предоставляет *оператор* (агент IoT Edge), согласовывает управляемый облаком требуемого состояния с состоянием локального кластера. 

Временем существования модуль управляет планировщик Kubernetes, который обеспечивает доступность модуля и выбирает их размещения. IoT Edge управляет платформа приложения edge, в верхней части страницы, постоянно согласование желаемого состояния, указанного в центре Интернета вещей с состоянием в кластере edge. Граничная модель приложения по-прежнему знакомую модель на основе модулей Edge Интернета вещей и маршруты. Оператор агента IoT Edge выполняет *автоматического* создает преобразование в местные Kubernetes как модули, развертывания, службы и т.д.

Ниже приведена схема архитектура высокого уровня.

![kubernetes arch](./media/how-to-install-iot-edge-kubernetes/k8s-arch.png)

Каждый компонент развертывание edge, ограничиваются конкретного устройства, что позволяет совместно использовать те же ресурсы кластера, среди нескольких устройств edge и развертывание пространств имен Kubernetes.

>[!NOTE]
>IoT Edge на Kubernetes находится в [общедоступной предварительной версии](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="install-locally-for-a-quick-test-environment"></a>Установить локально, для быстрого тестовой среды

### <a name="prerequisites"></a>Технические условия

* Kubernetes 1,10 или более поздней версии. Если у вас нет существующей процедуры настройки кластера, можно использовать [Minikube](https://kubernetes.io/docs/setup/minikube/) для среды локального кластера. 

* [Helm](https://helm.sh/docs/using_helm/#quickstart-guide), диспетчер пакетов Kubernetes.

* [kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/) для просмотра и взаимодействия с кластером.

### <a name="setup-steps"></a>Процедура настройки

1. Запуск **Minikube**

    ``` shell
    minikube start
    ```

1. Инициализировать **Helm** серверный компонент (*tiller*) в кластере

    ``` shell
    helm init
    ```

1. Добавьте репозиторий Edge Интернета вещей и обновления установки helm

    ``` shell
    helm repo add edgek8s https://edgek8s.blob.core.windows.net/helm/
    helm repo update
    ```

1. [Создание центра Интернета вещей](../iot-hub/iot-hub-create-through-portal.md), [зарегистрировать устройство IoT Edge](how-to-register-device-portal.md)и запишите его строку подключения.

1. Установка подключения к кластеру iotedged и агент Edge Интернета вещей

    ```shell
    helm install \
    --name k8s-edge1 \
    --set "deviceConnectionString=replace-with-device-connection-string" \
    edgek8s/edge-kubernetes
    ```
1. Откройте панель мониторинга Kubernetes в браузере

    ```shell
    minikube dashboard
    ```

    В разделе пространства имен кластера, вы увидите, другой — для устройства IoT Edge в соответствии со стандартом *msiot -\<iothub-name > —\<edgedevice-name >*. В этом пространстве имен модулей POD iotedged и агент Edge Интернета вещей следует приступить к работе.

1. Добавление имитированный модуль датчика температуры, следуя указаниям в [развертывание модуля](quickstart-linux.md#deploy-a-module) раздел быстрого запуска. Управление модуля IoT Edge осуществляется на портале центра Интернета вещей, так же, как и любое другое устройство IoT Edge. Внесение локальных изменений в конфигурации модуля с помощью средств Kubernetes не рекомендуется, так как они могут быть перезаписаны.

1. Через несколько секунд, обновление **модулей** будут отображаться на странице в пространстве имен устройство edge на панели мониторинга концентратора IoT Edge и модулей POD имитируемого датчика как концентратор Edge Интернета вещей, выполнив pod приема данных в центр Интернета вещей.

## <a name="clean-up-resources"></a>Очистка ресурсов

Чтобы удалить все ресурсы, созданные при развертывании edge, используйте следующую команду с именем, использовавшееся на шаге 5 в предыдущем разделе.

``` shell
helm delete --purge k8s-edge1
```

## <a name="next-steps"></a>Дальнейшие действия

### <a name="deploy-as-a-highly-available-edge-gateway"></a>Развертывание в качестве шлюза edge высокой доступности 

Пограничное устройство в кластере Kubernetes можно использовать в качестве шлюза Интернета вещей для подчиненных устройств. Его можно настроить, чтобы быть устойчивым к сбоям узла, обеспечивая высокий уровень доступности для развертываний edge. См. в разделе, это [Подробное пошаговое руководство](https://github.com/Azure-Samples/iotedge-gateway-on-kubernetes) использовании Edge Интернета вещей в этом сценарии.