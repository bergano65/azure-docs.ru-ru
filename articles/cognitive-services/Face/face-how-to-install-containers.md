---
title: Установка и запуск контейнеров
titlesuffix: Face - Azure Cognitive Services
description: Скачать, установить и запускать контейнеры для лиц в этом пошаговом руководстве.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: dapine
ms.openlocfilehash: beb9818be05fbb9a9e9c958dccb2e375f7685bd0
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/20/2019
ms.locfileid: "67272800"
---
# <a name="install-and-run-face-containers"></a>Установка и запуск контейнеров лиц

Azure распознавания лиц Cognitive Services предоставляет стандартизированный контейнер Linux для Docker, который обнаруживает человеческих лиц на изображениях. Она также определяет атрибуты, которые включают позу, такие как noses и глаза, пол, возраст и другие машины прогнозируемые черт лица. Помимо обнаружения лиц можно проверить, идентичность двух лиц на тот же образ или различные образы с помощью показатель достоверности. Также лиц можно сравнить фрагменты в базе, существует ли уже похожие или идентичные лиц. Его также можно организовать похожих лиц в группы с помощью общего visual признаков.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

## <a name="prerequisites"></a>Технические условия

Прежде чем использовать контейнеры API распознавания лиц, необходимо выполнить следующие условия.

|Обязательно для заполнения|Назначение|
|--|--|
|Модуль Docker| Подсистема Docker должен быть установлен на [главного компьютера](#the-host-computer). Docker предоставляет пакеты, которые настраивают среду Docker в ОС [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) и [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Ознакомьтесь с [общими сведениями о Docker и контейнерах](https://docs.docker.com/engine/docker-overview/).<br><br> Docker нужно настроить таким образом, чтобы контейнеры могли подключать и отправлять данные о выставлении счетов в Azure. <br><br> В Windows Docker также должна быть настроена для поддержки контейнеров Linux.<br><br>|
|Опыт работы с Docker | Необходимые основные понятия Docker, такие как реестры, репозитории, контейнеры и образы контейнеров. Необходимо знание basic `docker` команды.| 
|Azure `Cognitive Services` ресурсов |Чтобы использовать контейнер, необходимо иметь:<br><br>Ресурс Azure Cognitive Services и соответствующий ключ выставления счетов и выставления счетов URI конечной точки. Оба значения доступны на **Обзор** и **ключи** страницы для ресурса. От них требуется запуска контейнера. Добавление `face/v1.0` маршрутизации к конечной точке URI, как показано в следующем примере BILLING_ENDPOINT_URI: <br><br>**{BILLING_KEY}** : ключ ресурса<br><br>**{BILLING_ENDPOINT_URI}** : пример URI конечной точки `https://westus.api.cognitive.microsoft.com/face/v1.0`|

## <a name="request-access-to-the-private-container-registry"></a>Запрос доступа к частному реестру контейнеров

[!INCLUDE [Request access to private container registry](../../../includes/cognitive-services-containers-request-access.md)]

### <a name="the-host-computer"></a>Главный компьютер

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]


### <a name="container-requirements-and-recommendations"></a>Требования к контейнеру и рекомендации

В следующей таблице описаны минимальное и рекомендуемое число ядра ЦП и памяти для каждого контейнера API распознавания лиц.

| Контейнер | Минимальная | Рекомендуется | Транзакций в секунду<br>(Минимума, максимума)|
|-----------|---------|-------------|--|
|Распознавание лиц | 1 ядро, 2 ГБ памяти | 1 ядро, 4 ГБ памяти |10, 20|

* Каждое ядро должно быть по крайней мере с частотой 2,6 ГГц или быстрее.
* Транзакций в секунду (TPS).

Ядро и память соответствуют параметрам `--cpus` и `--memory`, которые используются как часть команды `docker run`.

## <a name="get-the-container-image-with-docker-pull"></a>Получить образ контейнера с docker на включение внесенных изменений

Доступны образы контейнеров для API распознавания лиц. 

| Контейнер | Репозиторий |
|-----------|------------|
| Распознавание лиц | `containerpreview.azurecr.io/microsoft/cognitive-services-face:latest` |

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="docker-pull-for-the-face-container"></a>docker pull для контейнера распознавания лиц

```
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-face:latest
```

## <a name="use-the-container"></a>Использование контейнера

Поместив контейнера на [главного компьютера](#the-host-computer), воспользуйтесь следующей процедурой для работы с контейнером.

1. [Запуск контейнера](#run-the-container-with-docker-run) с необходимыми выставления счетов параметры. Доступны дополнительные [примеры](./face-resource-container-config.md#example-docker-run-commands) команды `docker run`. 
1. [Запрос конечной точки прогнозирования контейнера](#query-the-containers-prediction-endpoint). 

## <a name="run-the-container-with-docker-run"></a>Запуск контейнера с помощью "run" docker

Воспользуйтесь командой [docker run](https://docs.docker.com/engine/reference/commandline/run/) для запуска любого из трех контейнеров. Команда использует следующие параметры.

| Placeholder | Значение |
|-------------|-------|
|{BILLING_KEY} | Этот ключ используется для запуска контейнера и доступен в Azure `Cognitive Services` **ключи** страницы. |
|{BILLING_ENDPOINT_URI} | Выставления счетов значение URI конечной точки можно найти в Azure `Cognitive Services` **Обзор** страницы. Например, `https://westus.api.cognitive.microsoft.com/face/v1.0`.|

Добавление `face/v1.0` маршрутизации к конечной точке URI, как показано в приведенном выше примере BILLING_ENDPOINT_URI. 

Замените эти параметры собственными значениями в следующем `docker run` пример команды:

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-face \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY}
```

Эта команда:

* Запускает контейнер лиц из образа контейнера.
* Выделяет одно ядро ЦП и 4 ГБ памяти.
* Предоставляет TCP-порт 5000 и выделяет расширенной псевдоформе TTY для контейнера.
* автоматически удаляет контейнер после завершения его работы. Образ контейнера остается доступным на главном компьютере. 

Доступны дополнительные [примеры](./face-resource-container-config.md#example-docker-run-commands) команды `docker run`. 

> [!IMPORTANT]
> `Eula`, `Billing`, И `ApiKey` параметры должны указываться для запуска контейнера или не запускается контейнер. Дополнительные сведения см. в [разделе о выставлении счетов](#billing).

[!INCLUDE [Running multiple containers on the same host](../../../includes/cognitive-services-containers-run-multiple-same-host.md)]


## <a name="query-the-containers-prediction-endpoint"></a>Запрос конечной точки прогнозирования контейнера

Контейнер предоставляет API запроса конечной точки прогнозирования на основе REST. 

Используйте узел `https://localhost:5000` для API контейнера.


<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>Остановка контейнера

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Устранение неполадок

Если вы запустите контейнер с выходом [подключить](./face-resource-container-config.md#mount-settings) и ведение журнала включено, контейнер создает файлы журнала, которые могут быть полезны для устранения неполадок, происходящие во время запуска или запуска контейнера. 


## <a name="billing"></a>Выставление счетов

API распознавания лиц контейнеры отправки сведений о выставлении счетов в Azure с помощью ресурса API распознавания лиц на учетную запись Azure. 

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Дополнительные сведения об этих параметрах см. в статье [Настройка контейнеров](./face-resource-container-config.md).

<!--blogs/samples/video coures -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>Сводка

В этой статье вы узнали основные понятия и рабочий процесс как загрузка, Установка и запуск контейнеров API распознавания лиц. В разделе "Сводка" сделайте следующее.

* API распознавания лиц предоставляет три контейнеры Linux для Docker, которые обеспечивают извлечение ключевых фраз, определение языка и анализ тональности.
* Образы контейнеров, загружаются из реестра контейнеров Azure.
* Образы контейнеров выполняются в Docker.
* REST API или пакет SDK можно использовать для вызова операций в контейнерах API распознавания лиц, указав узла URI контейнера.
* При создании экземпляра контейнера, необходимо указать сведения о выставлении счетов.

> [!IMPORTANT]
> Cognitive Services контейнеры не лицензируются для запуска без подключения к Azure для отслеживания использования. Клиентам необходимо включить контейнеры для обмена данными для выставления счетов в службе контроля использования программных продуктов, все время. Контейнеры Cognitive Services не отправляют в корпорацию Майкрософт данные клиента, например анализируемые изображения или тексты.

## <a name="next-steps"></a>Дальнейшие действия

* Параметры конфигурации, см. в разделе [Настройка контейнеров](face-resource-container-config.md).
* Дополнительные сведения о том, как для обнаружения и идентификации лиц, см. в разделе [обзор лиц](Overview.md).
* Сведения о методах, поддерживаемых контейнера, см. в разделе [API распознавания лиц](//westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).
* Чтобы использовать дополнительные контейнеры Cognitive Services, см. в разделе [контейнеры Cognitive Services](../cognitive-services-container-support.md).
