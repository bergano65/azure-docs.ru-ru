---
title: Установка и запуск контейнеров для использования API детектора аномалий
titleSuffix: Azure Cognitive Services
description: Используйте расширенные алгоритмы API Детектора аномалий для определения аномалий в данных временных рядов.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: dapine
ms.openlocfilehash: 45abd904ea95cf8e68583ba5630a485af59479ec
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/22/2019
ms.locfileid: "74327259"
---
# <a name="install-and-run-anomaly-detector-containers-preview"></a>Установка и запуск контейнеров детекторов аномалий (Предварительная версия)

Средство обнаружения аномалий имеет следующие функциональные возможности контейнера:

| Функция | Функции |
|--|--|
| Детектор аномалий | <li> Обнаруживает аномалии по мере их возникновения в режиме реального времени. <li> Обнаруживает аномалии во всем наборе данных как пакет. <li> Выводит ожидаемый нормальный диапазон данных. <li> Поддерживает настройку чувствительности к обнаружению аномалий, чтобы лучше соответствовать вашим данным. |

Подробные сведения об API см. в следующих статьях:
* [Дополнительные сведения о службе API детектора аномалий](https://go.microsoft.com/fwlink/?linkid=2080698&clcid=0x409)

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) , прежде чем начинать работу.

## <a name="prerequisites"></a>предварительным требованиям

Прежде чем использовать контейнеры детекторов аномалий, необходимо выполнить следующие предварительные требования:

|обязательные|Назначение|
|--|--|
|Модуль Docker| На [главном компьютере](#the-host-computer) должен быть установлен модуль Docker. Docker предоставляет пакеты, которые настраивают среду Docker в ОС [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) и [Linux](https://docs.docker.com/engine/installation/#supported-platforms). См. [общие сведения о Docker и контейнерах](https://docs.docker.com/engine/docker-overview/).<br><br> Docker нужно настроить таким образом, чтобы контейнеры могли подключать и отправлять данные о выставлении счетов в Azure. <br><br> **В ОС Windows** для Docker нужно также настроить поддержку контейнеров Linux.<br><br>|
|Опыт работы с Docker | Требуется базовое представление о понятиях Docker, включая реестры, репозитории, контейнеры и образы контейнеров, а также знание основных команд `docker`.| 
|Ресурс детектора аномалий |Для использования контейнеров необходимо следующее:<br><br>Ресурс _детектора аномалий_ Azure для получения соответствующего ключа API и URI конечной точки. Оба значения доступны на страницах "Обзор **детекторов аномалий** " и "ключи" портал Azure и необходимы для запуска контейнера.<br><br>**{API_KEY}** : один из двух доступных ключей ресурсов на странице " **ключи** "<br><br>**{ENDPOINT_URI}** : конечная точка, указанная на странице **обзора**|

[!INCLUDE [Gathering required container parameters](../containers/includes/container-gathering-required-parameters.md)]

## <a name="request-access-to-the-container-registry"></a>Запрос доступа к реестру контейнеров

Сначала необходимо завершить и отправить [форму запроса контейнера детектора аномалий](https://aka.ms/adcontainer) , чтобы запросить доступ к контейнеру.

[!INCLUDE [Request access to the container registry](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Authenticate to the container registry](../../../includes/cognitive-services-containers-access-registry.md)]

## <a name="the-host-computer"></a>Главный компьютер

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

<!--* [Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/). For instructions of deploying Anomaly Detector module in IoT Edge, see [How to deploy Anomaly Detector module in IoT Edge](how-to-deploy-anomaly-detector-module-in-iot-edge.md).-->

### <a name="container-requirements-and-recommendations"></a>Требования к контейнеру и рекомендации

В следующей таблице описаны минимальные и Рекомендуемые ядра ЦП и память, выделяемые для контейнера детекторов обнаружения аномалий.

| QPS (запросов в секунду) | Минимальная | Рекомендуется |
|-----------|---------|-------------|
| 10 QPS | 4 ядра, 1 ГБ памяти | 8-ядерный 2 ГБ памяти |
| 20 QPS | 8 ядер, 2 ГБ памяти | 16-ядерный 4 ГБ памяти |

Частота каждого ядра должна быть минимум 2,6 ГГц.

Ядро и память соответствуют параметрам `--cpus` и `--memory`, которые используются как часть команды `docker run`.

## <a name="get-the-container-image-with-docker-pull"></a>Получение образа контейнера с помощью `docker pull`

Воспользуйтесь командой [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/), чтобы скачать образ контейнера.

| Контейнер | Репозиторий |
|-----------|------------|
| обнаружение аномалий-службы-детектор | `containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector:latest` |

<!--
For a full description of available tags, such as `latest` used in the preceding command, see [anomaly-detector](https://go.microsoft.com/fwlink/?linkid=2083827&clcid=0x409) on Docker Hub.
-->
[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="docker-pull-for-the-anomaly-detector-container"></a>Извлечение DOCKER для контейнера детекторов аномалий

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector:latest
```

## <a name="how-to-use-the-container"></a>Использование контейнера

После размещения контейнера на [главном компьютере](#the-host-computer) воспользуйтесь следующей процедурой для работы с ним.

1. [Запустите контейнер](#run-the-container-with-docker-run) с необходимыми настройками выставления счетов. Доступны дополнительные [примеры](anomaly-detector-container-configuration.md#example-docker-run-commands) команды `docker run`.
1. [Запрос конечной точки прогнозирования контейнера](#query-the-containers-prediction-endpoint).

## <a name="run-the-container-with-docker-run"></a>Запуск контейнера с помощью команды `docker run`

Воспользуйтесь командой [docker run](https://docs.docker.com/engine/reference/commandline/run/) для запуска контейнера. Сведения о том, как получить значения `{ENDPOINT_URI}` и `{API_KEY}`, см. в разделе [сбор обязательных параметров](#gathering-required-parameters) .

Доступны [примеры](anomaly-detector-container-configuration.md#example-docker-run-commands) команды `docker run`.

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector:latest \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Эта команда:

* Запускает контейнер детектора аномалий из образа контейнера
* выделяет одно ядро ЦП и 4 ГБ памяти;
* открывает TCP-порт 5000 и выделяет псевдотелетайп для контейнера;
* автоматически удаляет контейнер после завершения его работы. Образ контейнера остается доступным на главном компьютере. 

> [!IMPORTANT]
> Для выполнения контейнера необходимо указать параметры `Eula`, `Billing` и `ApiKey`. В противном случае контейнер не запустится.  Дополнительные сведения см. в [разделе о выставлении счетов](#billing).

### <a name="running-multiple-containers-on-the-same-host"></a>Запуск нескольких контейнеров на одном узле

Если вы планируете запускать несколько контейнеров при открытых портах, обязательно назначьте каждому контейнеру отдельный порт. Например, запускайте первый контейнер на порте 5000, а второй — на порте 5001.

Замените `<container-registry>` и `<container-name>` значениями используемых контейнеров. При этом можно использовать разные контейнеры. Контейнер детектора аномалий и контейнер LUIS, выполняющиеся на узле, можно использовать совместно, либо можно использовать несколько контейнеров обнаружения аномалий. 

Выполните первый контейнер на порте 5000. 

```bash 
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
<container-registry>/microsoft/<container-name> \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Запустите второй контейнер на порте 5001.


```bash 
docker run --rm -it -p 5000:5001 --memory 4g --cpus 1 \
<container-registry>/microsoft/<container-name> \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Каждому последующему контейнеру должен быть назначен отдельный порт. 

## <a name="query-the-containers-prediction-endpoint"></a>Запрос конечной точки прогнозирования контейнера

Контейнер предоставляет API запроса конечной точки прогнозирования на основе REST. 

Используйте узел http://localhost:5000 для API контейнера.

<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>Остановка контейнера

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Устранение неполадок

Если контейнер запускается с выходным [подключением](anomaly-detector-container-configuration.md#mount-settings) и включенным ведением журнала, контейнер создает файлы журнала, которые удобно использовать для устранения неполадок, возникающих во время запуска или работы контейнера.

[!INCLUDE [Cognitive Services FAQ note](../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>Выставление счетов

Контейнеры детекторов аномалий отправляют сведения о выставлении счетов в Azure с помощью ресурса _детектора аномалий_ в учетной записи Azure. 

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Дополнительные сведения об этих параметрах см. в статье [Настройка контейнеров](anomaly-detector-container-configuration.md).

<!--blogs/samples/video coures -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>summary

В этой статье вы узнали основные понятия и рабочий процесс по скачиванию, установке и запуску контейнеров детекторов аномалий. Краткая сводка.

* Детектор аномалий предоставляет один контейнер Linux для DOCKER, инкапсуляцию обнаружения аномалий с помощью пакетной и потоковой передачи, ожидаемого определения диапазона и настройки чувствительности.
* Образы контейнеров загружаются из закрытого реестра контейнеров Azure, выделенного для предварительной версии контейнеров.
* Образы контейнеров, которые выполняются в Docker.
* Вы можете использовать REST API или пакет SDK для вызова операций в контейнерах детектора аномалий, указав универсальный код ресурса (URI) узла контейнера.
* При создании контейнера необходимо указать данные для выставления счетов.

> [!IMPORTANT]
> Контейнеры Cognitive Services не лицензируются для запуска без подключения к Azure для отслеживания использования. Клиенты должны разрешить контейнерам непрерывную передачу данных для выставления счетов в службу контроля потребления. Cognitive Services контейнеры не отправляют данные клиента (например, анализируемые данные временных рядов) в корпорацию Майкрософт.

## <a name="next-steps"></a>Дополнительная информация

* Ознакомьтесь со статьей о [параметрах конфигурации](anomaly-detector-container-configuration.md).
* [Развертывание контейнера детекторов аномалий в службе "экземпляры контейнеров Azure"](how-to/deploy-anomaly-detection-on-container-instances.md)
* [Дополнительные сведения о службе API детектора аномалий](https://go.microsoft.com/fwlink/?linkid=2080698&clcid=0x409)
