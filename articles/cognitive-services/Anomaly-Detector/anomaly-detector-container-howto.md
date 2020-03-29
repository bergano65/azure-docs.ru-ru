---
title: Как установить и запустить контейнеры для использования API детектора аномалий
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79220350"
---
# <a name="install-and-run-anomaly-detector-containers-preview"></a>Установка и запуск контейнеров детектор аномалий (Предварительный просмотр)

Детектор аномалий имеет следующую функцию контейнера:

| Компонент | Компоненты |
|--|--|
| Детектор аномалий | <li> Обнаруживает аномалии, возникающие в режиме реального времени. <li> Обнаруживает аномалии во всем наборе данных в виде пакета. <li> Выводы ожидаемого нормального диапазона данных. <li> Поддерживает регулировку чувствительности обнаружения аномалий, чтобы лучше соответствовать вашим данным. |

Для получения подробной информации об AIS, пожалуйста, смотрите:
* [Подробнее об услуге API Anomaly Detector API](https://go.microsoft.com/fwlink/?linkid=2080698&clcid=0x409)

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

Перед использованием контейнеров Anomaly Detector необходимо выполнить следующие требования:

|Обязательно|Назначение|
|--|--|
|Модуль Docker| На [главном компьютере](#the-host-computer) должен быть установлен модуль Docker. Docker предоставляет пакеты, которые настраивают среду с Docker для [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) и [Linux](https://docs.docker.com/engine/installation/#supported-platforms). См. [общие сведения о Docker и контейнерах](https://docs.docker.com/engine/docker-overview/).<br><br> Docker нужно настроить таким образом, чтобы контейнеры могли подключать и отправлять данные о выставлении счетов в Azure. <br><br> **В ОС Windows** для Docker нужно также настроить поддержку контейнеров Linux.<br><br>|
|Опыт работы с Docker | Требуется базовое представление о понятиях Docker, включая реестры, репозитории, контейнеры и образы контейнеров, а также знание основных команд `docker`.| 
|Ресурс детектора аномалий |Для использования контейнеров необходимо следующее:<br><br>Ресурс _детектора аномалий_ Azure для получения связанного ключа API и URI с конечными точками. Оба значения доступны на страницах **Anomaly Detector** И страниц портала Azure и страниц ключей и необходимы для запуска контейнера.<br><br>**«API_KEY:** Один из двух доступных ключей ресурса на странице **Ключей**<br><br>**(ENDPOINT_URI:** конечная точка, представленная на странице **Обзор**|

[!INCLUDE [Gathering required container parameters](../containers/includes/container-gathering-required-parameters.md)]

## <a name="request-access-to-the-container-registry"></a>Запрос доступа к реестру контейнеров

Сначала необходимо заполнить и отправить форму запроса доступа к контейнеру [Anomaly Detector Container.](https://aka.ms/adcontainer)

[!INCLUDE [Request access to the container registry](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Authenticate to the container registry](../../../includes/cognitive-services-containers-access-registry.md)]

## <a name="the-host-computer"></a>Главный компьютер

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

<!--* [Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/). For instructions of deploying Anomaly Detector module in IoT Edge, see [How to deploy Anomaly Detector module in IoT Edge](how-to-deploy-anomaly-detector-module-in-iot-edge.md).-->

### <a name="container-requirements-and-recommendations"></a>Требования к контейнеру и рекомендации

В следующей таблице описаны минимальные и рекомендуемые ядра процессора и память для контейнера Anomaly Detector.

| КПС (Запросы в секунду) | Минимальные | Рекомендуемая |
|-----------|---------|-------------|
| 10 КПС | 4 ядра, память 1 ГБ | 8 основных 2-ГБ памяти |
| 20 КПС | 8 ядра, 2-ГБ памяти | 16 основных 4-ГБ памяти |

Частота каждого ядра должна быть минимум 2,6 ГГц.

Ядро и память соответствуют параметрам `--cpus` и `--memory`, которые используются как часть команды `docker run`.

## <a name="get-the-container-image-with-docker-pull"></a>Получение образа контейнера с помощью `docker pull`

Используйте [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) команду для загрузки изображения контейнера.

| Контейнер | Хранилище |
|-----------|------------|
| когнитивно-услуг-анома-детектор | `containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector:latest` |

<!--
For a full description of available tags, such as `latest` used in the preceding command, see [anomaly-detector](https://go.microsoft.com/fwlink/?linkid=2083827&clcid=0x409) on Docker Hub.
-->
[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="docker-pull-for-the-anomaly-detector-container"></a>Докер тянуть для контейнера детектор аномалий

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector:latest
```

## <a name="how-to-use-the-container"></a>Использование контейнера

После размещения контейнера на [главном компьютере](#the-host-computer) воспользуйтесь следующей процедурой для работы с ним.

1. [Запустите контейнер](#run-the-container-with-docker-run) с необходимыми настройками выставления счетов. Доступны дополнительные [примеры](anomaly-detector-container-configuration.md#example-docker-run-commands) команды `docker run`.
1. [Запрос конечная точка прогнозирования контейнера.](#query-the-containers-prediction-endpoint)

## <a name="run-the-container-with-docker-run"></a>Запуск контейнера с помощью команды `docker run`

Воспользуйтесь командой [docker run](https://docs.docker.com/engine/reference/commandline/run/) для запуска контейнера. Обратитесь к [сбору необходимых параметров](#gathering-required-parameters) `{ENDPOINT_URI}` для `{API_KEY}` получения подробной информации о том, как получить и значения.

[Приведены примеры](anomaly-detector-container-configuration.md#example-docker-run-commands) `docker run` команды.

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector:latest \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Эта команда:

* Запускает контейнер детектор аномалий с изображения контейнера
* выделяет одно ядро ЦП и 4 ГБ памяти;
* предоставляет TCP-порт 5000 и выделяет псевдотелетайп для контейнера;
* автоматически удаляет контейнер после завершения его работы. Образ контейнера остается доступным на главном компьютере. 

> [!IMPORTANT]
> Для запуска контейнера необходимо указать параметры `Eula`, `Billing` и `ApiKey`. В противном случае контейнер не запустится.  Дополнительные сведения см. в [разделе о выставлении счетов](#billing).

### <a name="running-multiple-containers-on-the-same-host"></a>Запуск нескольких контейнеров на одном и том же узлах

Если вы планируете запускать несколько контейнеров при открытых портах, обязательно назначьте каждому контейнеру отдельный порт. Например, запускайте первый контейнер на порте 5000, а второй — на порте 5001.

Замените `<container-registry>` и `<container-name>` значениями используемых контейнеров. При этом можно использовать разные контейнеры. Вы можете иметь контейнер детектор аномалии и контейнер LUIS работает на HOST вместе или вы можете иметь несколько контейнеров детектор аномалии работает. 

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

Контейнеры Anomaly Detector отправляют информацию о выставлении счетов в Azure, используя ресурс _Anomaly Detector_ в вашей учетной записи Azure. 

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Дополнительные сведения об этих параметрах см. в статье [Настройка контейнеров](anomaly-detector-container-configuration.md).

<!--blogs/samples/video coures -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>Сводка

В этой статье вы изучили концепции и рабочий процесс для загрузки, установки и запуска контейнеров anomaly Detector. В разделе "Сводка" сделайте следующее.

* Anomaly Detector предоставляет один linux-контейнер для Docker, инкапсулируя обнаружение аномалий с помощью партии против потоковой передачи, ожидаемого определения диапазона и настройки чувствительности.
* Изображения контейнеров загружаются из частного реестра контейнеров Azure, предназначенного для предварительного просмотра контейнеров.
* Образы контейнеров выполняются в Docker.
* Вы можете использовать либо REST API, либо SDK для вызова операций в контейнерах Anomaly Detector, указав хост URI контейнера.
* При создании экземпляра контейнера нужно указать данные для выставления счетов.

> [!IMPORTANT]
> Контейнеры Cognitive Services не лицензируются для запуска без подключения к Azure для отслеживания использования. Клиенты должны разрешить контейнерам непрерывную передачу данных для выставления счетов в службу контроля потребления. Контейнеры Cognitive Services не отправляют данные клиентов (например, анализируемые данные временных рядов) в корпорацию Майкрософт.

## <a name="next-steps"></a>Дальнейшие действия

* Ознакомьтесь со статьей о [конфигурации контейнеров](anomaly-detector-container-configuration.md).
* [Развертывание контейнера детектора аномалий в экземплярах контейнеров Azure](how-to/deploy-anomaly-detection-on-container-instances.md)
* [Подробнее об услуге API Anomaly Detector API](https://go.microsoft.com/fwlink/?linkid=2080698&clcid=0x409)
