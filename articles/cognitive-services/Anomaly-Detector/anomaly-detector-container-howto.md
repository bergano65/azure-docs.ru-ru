---
title: Установка и запуск контейнеров — средства обнаружения аномалий
titleSuffix: Azure Cognitive Services
description: Используйте API обнаружения аномалий сложные алгоритмы для определения аномалий в данных временных рядов.
services: cognitive-services
author: aahill
ms.service: cognitive-services
ms.subservice: anomaly-detection
ms.topic: article
ms.date: 05/07/2019
ms.author: aahi
ms.openlocfilehash: cc82e091ae4c033bda7f1d91c9aed36bb081de88
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/07/2019
ms.locfileid: "65233614"
---
# <a name="install-and-run-anomaly-detector-containers"></a>Установка и запуск средства обнаружения аномалий контейнеров

Средства обнаружения аномалий имеет следующий контейнер: 

|Функция|Функции|
|-|-|
|Средства обнаружения аномалий| <li> Обнаруживает аномалии в реальном времени. <li> Обнаруживает аномалии на протяжении всего набора данных в пакетном режиме. <li> Выводит ожидаемое обычный диапазон данных. <li> Корректировки чувствительность обнаружения аномалий поддерживает лучше разместить вашей информации. |

Подробные сведения об интерфейсах API см.:
* [Дополнительные сведения о службе API обнаружения аномалий](https://go.microsoft.com/fwlink/?linkid=2080698&clcid=0x409)

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

## <a name="prerequisites"></a>Технические условия

Перед использованием средства обнаружения аномалий контейнеры, необходимо выполнить следующие условия:

|Обязательно для заполнения|Назначение|
|--|--|
|Модуль Docker| На [главном компьютере](#the-host-computer) должен быть установлен модуль Docker. Docker предоставляет пакеты, которые настраивают среду Docker в ОС [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) и [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Ознакомьтесь с [общими сведениями о Docker и контейнерах](https://docs.docker.com/engine/docker-overview/).<br><br> Docker нужно настроить таким образом, чтобы контейнеры могли подключать и отправлять данные о выставлении счетов в Azure. <br><br> **В ОС Windows** для Docker нужно также настроить поддержку контейнеров Linux.<br><br>|
|Опыт работы с Docker | Требуется базовое представление о понятиях Docker, включая реестры, репозитории, контейнеры и образы контейнеров, а также знание основных команд `docker`.| 
|Ресурсов обнаружения аномалий |Для использования контейнеров необходимо следующее:<br><br>_Обнаружения аномалий_ ресурсов Azure, чтобы получить связанный ключ выставления счетов и выставления счетов URI конечной точки. Оба значения доступны на страницах Обзор средства обнаружения аномалий и ключей на портале Azure и необходимые для запуска контейнера.<br><br>**{BILLING_KEY}**: ключ ресурса<br><br>**{BILLING_ENDPOINT_URI}**: пример URI конечной точки: `https://westus2.api.cognitive.microsoft.com`|

## <a name="request-access-to-the-container-registry"></a>Запрос доступа к реестру контейнеров

Сначала выполните и отправить [форму запроса контейнера обнаружения аномалий](https://aka.ms/adcontainer) чтобы запросить доступ к контейнеру.

[!INCLUDE [Request access to the container registry](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Authenticate to the container registry](../../../includes/cognitive-services-containers-access-registry.md)]

## <a name="the-host-computer"></a>Главный компьютер

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

<!--* [Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/). For instructions of deploying Anomaly Detector module in IoT Edge, see [How to deploy Anomaly Detector module in IoT Edge](how-to-deploy-anomaly-detector-module-in-iot-edge.md).-->

### <a name="container-requirements-and-recommendations"></a>Требования к контейнеру и рекомендации

Ниже перечислены минимальные и рекомендуемые ядер ЦП и памяти, выделяемой для контейнера обнаружения аномалий.

| Приблизительное число (запросов в секунду) | Минимум | Рекомендуется |
|-----------|---------|-------------|
| ПРИБЛИЗИТЕЛЬНОЕ ЧИСЛО 10 | 4 ядра, 1 ГБ памяти | 8 ядер 2 ГБ памяти |
| ПРИБЛИЗИТЕЛЬНОЕ ЧИСЛО 20 | 8 ядро, 2 ГБ памяти | 16 ядер 4 ГБ памяти |

Частота каждого ядра должна быть минимум 2,6 ГГц.

Ядро и память соответствуют параметрам `--cpus` и `--memory`, которые используются как часть команды `docker run`.

## <a name="get-the-container-image-with-docker-pull"></a>Получение образа контейнера с помощью `docker pull`

Воспользуйтесь командой [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/), чтобы скачать образ контейнера.

| Контейнер | Репозиторий |
|-----------|------------|
| Cognitive-services аномалий — обнаружения | `containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector:latest` |

<!--
For a full description of available tags, such as `latest` used in the preceding command, see [anomaly-detector](https://go.microsoft.com/fwlink/?linkid=2083827&clcid=0x409) on Docker Hub.
-->
[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]


### <a name="docker-pull-for-the-anomaly-detector-container"></a>Получение образов docker для контейнера обнаружения аномалий

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector:latest
```

## <a name="how-to-use-the-container"></a>Использование контейнера

После размещения контейнера на [главном компьютере](#the-host-computer) воспользуйтесь следующей процедурой для работы с ним.

1. [Запустите контейнер](#run-the-container-with-docker-run) с необходимыми настройками выставления счетов. Доступны дополнительные [примеры](anomaly-detector-container-configuration.md#example-docker-run-commands) команды `docker run`. 
1. [Запрос конечной точки прогнозирования контейнера](#query-the-containers-prediction-endpoint). 

## <a name="run-the-container-with-docker-run"></a>Запуск контейнера с помощью команды `docker run`

Воспользуйтесь командой [docker run](https://docs.docker.com/engine/reference/commandline/run/) для запуска любого из трех контейнеров. В команде используются следующие параметры:

| Местозаполнитель | Value |
|-------------|-------|
|{BILLING_KEY} | Этот ключ используется для запуска контейнера, а также доступны на странице ключей обнаружения аномалий на портале Azure.  |
|{BILLING_ENDPOINT_URI} | Выставления счетов значение URI конечная точка доступна на странице обзора обнаружения аномалий на портале Azure.|

В следующем примере команды `docker run` замените имена параметров собственными значениями.

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector:latest \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY}
```

Эта команда:

* Запускает контейнер обнаружения аномалий на основе образа контейнера
* выделяет одно ядро ЦП и 4 ГБ памяти;
* предоставляет TCP-порт 5000 и выделяет псевдотелетайп для контейнера;
* автоматически удаляет контейнер после завершения его работы. Образ контейнера остается доступным на главном компьютере. 

> [!IMPORTANT]
> Для запуска контейнера необходимо указать параметры `Eula`, `Billing` и `ApiKey`. В противном случае контейнер не запустится.  Дополнительные сведения см. в [разделе о выставлении счетов](#billing).

### <a name="running-multiple-containers-on-the-same-host"></a>Запуск нескольких контейнеров на одном узле

Если вы планируете запускать несколько контейнеров при открытых портах, обязательно назначьте каждому контейнеру отдельный порт. Например, запускайте первый контейнер на порте 5000, а второй — на порте 5001.

Замените `<container-registry>` и `<container-name>` значениями используемых контейнеров. При этом можно использовать разные контейнеры. Может иметь контейнер обнаружения аномалий и LUIS контейнеров, работающих на узле друг с другом или у вас есть несколько контейнеров обнаружения аномалий. 

Выполните первый контейнер на порте 5000. 

```bash 
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
<container-registry>/microsoft/<container-name> \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY}
```

Запустите второй контейнер на порте 5001.


```bash 
docker run --rm -it -p 5000:5001 --memory 4g --cpus 1 \
<container-registry>/microsoft/<container-name> \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY}
```

Каждому последующему контейнеру должен быть назначен отдельный порт. 

## <a name="query-the-containers-prediction-endpoint"></a>Запрос конечной точки прогнозирования контейнера

Контейнер предоставляет API запроса конечной точки прогнозирования на основе REST. 

Используйте узел https://localhost:5000 для API контейнера.

<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>Остановка контейнера

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Устранение неполадок

Если контейнер запускается с выходным [подключением](anomaly-detector-container-configuration.md#mount-settings) и включенным ведением журнала, контейнер создает файлы журнала, которые удобно использовать для устранения неполадок, возникающих во время запуска или работы контейнера. 

## <a name="billing"></a>Выставление счетов

Отправить контейнеры обнаружения аномалий, выставления счетов в Azure, с помощью _обнаружения аномалий_ ресурсов на учетную запись Azure. 

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Дополнительные сведения об этих параметрах см. в статье [Настройка контейнеров](anomaly-detector-container-configuration.md).

## <a name="summary"></a>Сводка

В этой статье вы узнали основные понятия и рабочий процесс для загрузки, Установка и запуск средства обнаружения аномалий контейнеры. В разделе "Сводка" сделайте следующее.

* Средства обнаружения аномалий предоставляет один контейнер Linux для Docker, инкапсулируя обнаружение аномалий с помощью пакета vs потоковой передачи, определение ожидаемого диапазона, а также Настройка чувствительности.
* Образы контейнеров, загружаются из частного реестра контейнеров Azure выделенный для предварительной версии контейнеры.
* Образы контейнеров выполняются в Docker.
* Пакет SDK или REST API можно использовать для вызова операций в контейнерах обнаружения аномалий, указав узла URI контейнера.
* При создании экземпляра контейнера нужно указать данные для выставления счетов.

> [!IMPORTANT]
> Контейнеры Cognitive Services не лицензируются для запуска без подключения к Azure для отслеживания использования. Клиенты должны разрешить контейнерам непрерывную передачу данных для выставления счетов в службу контроля потребления. Cognitive Services контейнеры не отправляются в корпорацию Майкрософт данные клиента (например, данные временных рядов, который анализируется).

## <a name="next-steps"></a>Дальнейшие действия

* Ознакомьтесь со статьей о [конфигурации контейнеров](anomaly-detector-container-configuration.md).
* [Дополнительные сведения о службе API обнаружения аномалий](https://go.microsoft.com/fwlink/?linkid=2080698&clcid=0x409)
