---
title: Установка IoT Edge в Kubernetes | Документация Майкрософт
description: Узнайте, как установить IoT Edge в Kubernetes с помощью локальной среды кластера разработки
author: kgremban
manager: philmea
ms.author: veyalla
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: a453779ffe4ae20acf55510d0ac9f9483763af21
ms.sourcegitcommit: c4700ac4ddbb0ecc2f10a6119a4631b13c6f946a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/27/2019
ms.locfileid: "72964836"
---
# <a name="how-to-install-iot-edge-on-kubernetes-preview"></a>Установка IoT Edge в Kubernetes (Предварительная версия)

IoT Edge можно интегрировать с Kubernetes, используя его как устойчивый высокодоступный уровень инфраструктуры. Он регистрирует IoT Edge *Определение настраиваемого ресурса* (CRD) с помощью сервера API Kubernetes. Кроме того, он предоставляет *оператор* (IOT Edge агент), который согласовывает требуемое облачное состояние и состояние локального кластера. 

Время существования модуля управляется планировщиком Kubernetes, который поддерживает доступность модуля и выбирает их размещение. IoT Edge управляет платформой приложений пограничной среды, последовательно выполняя согласованность требуемого состояния, указанного в центре Интернета вещей, с состоянием в пограничном кластере. Модель приложения пограничной модели по-прежнему является знакомой моделью, основанной на IoT Edge модулях и маршрутах. Оператор IoT Edge Agent выполняет *Автоматический* перевод в собственные конструкции Kubernetes, такие как модули Pod, развертывания, службы и т. д.

Ниже приведена схема архитектуры высокого уровня.

![kubernetesная дуга](./media/how-to-install-iot-edge-kubernetes/k8s-arch.png)

Каждый компонент развертывания пограничной области ограничен пространством имен Kubernetes, характерным для устройства, что делает возможным совместное использование одних и тех же ресурсов кластера между несколькими пограничными устройствами и их развертываниями.

>[!NOTE]
>IoT Edge в Kubernetes доступна в [общедоступной предварительной версии](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="install-locally-for-a-quick-test-environment"></a>Локальная установка для быстрой тестовой среды

### <a name="prerequisites"></a>Технические условия

* Kubernetes 1,10 или более поздней версии. Если у вас нет существующей настройки кластера, можно использовать [Minikube](https://kubernetes.io/docs/setup/minikube/) для локальной среды кластера. 

* [Helm](https://helm.sh/docs/using_helm/#quickstart-guide), диспетчер пакетов Kubernetes.

* [kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/) для просмотра и взаимодействия с кластером.

### <a name="setup-steps"></a>Процедура настройки

1. Запустить **Minikube**

    ``` shell
    minikube start
    ```

1. Инициализация компонента сервера **Helm** вкластере

    ``` shell
    helm init
    ```

1. Добавление репозитория IoT Edge и обновление установки Helm

    ``` shell
    helm repo add edgek8s https://edgek8s.blob.core.windows.net/helm/
    helm repo update
    ```

1. [Создайте центр Интернета вещей](../iot-hub/iot-hub-create-through-portal.md), [зарегистрируйте устройство IOT Edge](how-to-register-device.md)и запишите его строку подключения.

1. Установка иотеджед и агента IoT Edge в кластер

    ```shell
    helm install \
    --name k8s-edge1 \
    --set "deviceConnectionString=replace-with-device-connection-string" \
    edgek8s/edge-kubernetes
    ```
1. Открытие панели мониторинга Kubernetes в браузере

    ```shell
    minikube dashboard
    ```

    В пространствах имен кластера вы увидите одно для IoT Edge устройства, следуя соглашению *мсиот-\<iothub-name >-\<еджедевице-name >* . В этом пространстве имен должны выполняться и иотеджед агент, и модули, IoT Edge

1. Добавьте смоделированный модуль датчика температуры, выполнив действия, описанные в разделе " [Развертывание модуля](quickstart-linux.md#deploy-a-module) " краткого руководства. IoT Edge управление модулями выполняется на портале Центра Интернета вещей, как и любое другое устройство IoT Edge. Не рекомендуется вносить локальные изменения в конфигурацию модуля через средства Kubernetes, так как они могут быть перезаписаны.

1. Через несколько секунд при **обновлении страницы Pod в пространстве** имен пограничных устройств на панели мониторинга будет отображаться центр IOT EDGE и смоделированные модули-концентраторы датчиков, работающие с модулем концентратора IOT EDGE, принимающим данные в центр Интернета вещей.

## <a name="clean-up-resources"></a>Очистка ресурсов

Чтобы удалить все ресурсы, созданные с помощью развертывания пограничной платформы, используйте следующую команду с именем, которое использовалось на шаге 5 предыдущего раздела.

``` shell
helm delete --purge k8s-edge1
```

## <a name="next-steps"></a>Дальнейшие действия

### <a name="deploy-as-a-highly-available-edge-gateway"></a>Развертывание в качестве шлюза с высокой доступностью 

Пограничное устройство в кластере Kubernetes можно использовать в качестве шлюза IoT для подчиненных устройств. Она может быть устойчивой к сбоям узла, что обеспечивает высокий уровень доступности для развертываний пограничных устройств. См. [подробное пошаговое руководство](https://github.com/Azure-Samples/iotedge-gateway-on-kubernetes) по использованию IOT EDGE в этом сценарии.