---
title: Установка и запуск контейнеров — API распознавания лиц
titleSuffix: Azure Cognitive Services
description: Загрузка, установка и запуск контейнеров для лиц в этом пошаговом руководстве.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: dapine
ms.openlocfilehash: 93a6d0a42d6d7f07dd8947ce9f8ae99a39d44475
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68564148"
---
# <a name="install-and-run-face-containers"></a>Установка и запуск контейнеров лиц

Лицо Cognitive Services Azure предоставляет стандартизированный контейнер Linux для DOCKER, который обнаруживает людей в образах. Он также определяет атрибуты, которые включают ориентиры для лиц, такие как нос и глаза, пол, возраст и другие функции, прогнозируемые с точки зрения машинного лица. Кроме обнаружения, лицо может проверить, совпадают ли два лица в одном или разных изображениях с помощью оценки достоверности. Кроме того, лицо может сравнить лица с базой данных, чтобы узнать, существует ли уже похожий или аналогичный лицом. Он также может организовывать схожие лица в группы с помощью общих визуальных характеристик.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

Прежде чем использовать контейнеры API распознавания лиц, необходимо выполнить следующие предварительные требования.

|Обязательное значение|Цель|
|--|--|
|Модуль Docker| Подсистема DOCKER должна быть установлена на [главном компьютере](#the-host-computer). Docker предоставляет пакеты, которые настраивают среду Docker в ОС [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) и [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Ознакомьтесь с [общими сведениями о Docker и контейнерах](https://docs.docker.com/engine/docker-overview/).<br><br> Docker нужно настроить таким образом, чтобы контейнеры могли подключать и отправлять данные о выставлении счетов в Azure. <br><br> В Windows DOCKER также должен быть настроен для поддержки контейнеров Linux.<br><br>|
|Опыт работы с Docker | Вам потребуется базовое понимание концепций DOCKER, таких как реестры, репозитории, контейнеры и образы контейнеров. Также необходимо знание основных `docker` команд.| 
|Ресурс лица |Для использования этого контейнера необходимо следующее:<br><br>Ресурс **лица** Azure, а также соответствующий ключ API и URI конечной точки. Оба значения доступны на страницах **обзора** и **ключей** для ресурса. Они необходимы для запуска контейнера.<br><br>**{API_KEY}** : Один из двух доступных ключей ресурсов на странице " **ключи** "<br><br>**{ENDPOINT_URI}** : Конечная точка, указанная на странице **обзора**

## <a name="request-access-to-the-private-container-registry"></a>Запрос доступа к частному реестру контейнеров

[!INCLUDE [Request access to private container registry](../../../includes/cognitive-services-containers-request-access.md)]

### <a name="the-host-computer"></a>Главный компьютер

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>Требования к контейнеру и рекомендации

В следующей таблице описаны минимальное и рекомендуемое число ядра ЦП и памяти для каждого контейнера API распознавания лиц.

| Контейнер | Минимум | Рекомендуется | Транзакций в секунду<br>(Минимум, максимум)|
|-----------|---------|-------------|--|
|Распознавание лиц | 1 ядро, 2 ГБ памяти | 1 ядро, 4 ГБ памяти |10, 20|

* Каждое ядро должно иметь частоту не менее 2,6 ГГц или быстрее.
* Количество транзакций в секунду (TPS).

Ядро и память соответствуют параметрам `--cpus` и `--memory`, которые используются как часть команды `docker run`.

## <a name="get-the-container-image-with-docker-pull"></a>Получение образа контейнера с помощью docker pull

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

После размещения контейнера на [главном компьютере](#the-host-computer) выполните приведенные ниже инструкции для работы с ним.

1. [Запустите контейнер](#run-the-container-with-docker-run) с необходимыми параметрами выставления счетов. Доступны дополнительные [примеры](./face-resource-container-config.md#example-docker-run-commands) команды `docker run`. 
1. [Запрос конечной точки прогнозирования контейнера](#query-the-containers-prediction-endpoint). 

## <a name="run-the-container-with-docker-run"></a>Запуск контейнера с помощью запуска DOCKER

Воспользуйтесь командой [docker run](https://docs.docker.com/engine/reference/commandline/run/) для запуска любого из трех контейнеров. Команда использует следующие параметры.

| Местозаполнитель | Значение |
|-------------|-------|
|{API_KEY} | Этот ключ используется для запуска контейнера и доступен на странице `Cognitive Services` **ключей** Azure. |
|{ENDPOINT_URI} | Значение URI конечной точки выставления счетов доступно на странице `Cognitive Services` **обзора** Azure. Например, `https://westus.api.cognitive.microsoft.com/face/v1.0`.|

`face/v1.0` Добавьте маршрут к универсальному коду ресурса (URI) конечной точки, как показано в предыдущем примере ENDPOINT_URI. 

Замените эти параметры собственными значениями в следующем `docker run` примере команды:

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-face \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Эта команда:

* Запускает контейнер лицевой стороны из образа контейнера.
* Выделяется одно ядро ЦП и 4 ГБ памяти.
* Предоставляет TCP-порт 5000 и выделяет псевдо для контейнера.
* автоматически удаляет контейнер после завершения его работы. Образ контейнера остается доступным на главном компьютере. 

Доступны дополнительные [примеры](./face-resource-container-config.md#example-docker-run-commands) команды `docker run`. 

> [!IMPORTANT]
> Для запуска `Billing`контейнера необходимо `ApiKey` указать параметры ,и,чтобыконтейнернезапускался.`Eula` Дополнительные сведения см. в [разделе о выставлении счетов](#billing).

[!INCLUDE [Running multiple containers on the same host](../../../includes/cognitive-services-containers-run-multiple-same-host.md)]


## <a name="query-the-containers-prediction-endpoint"></a>Запрос конечной точки прогнозирования контейнера

Контейнер предоставляет API запроса конечной точки прогнозирования на основе REST. 

Используйте узел `https://localhost:5000` для API контейнера.


<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>Остановка контейнера

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Устранение неполадок

Если вы запускаете контейнер с выходным [подключением](./face-resource-container-config.md#mount-settings) и ведение журнала включено, контейнер создает файлы журнала, которые полезны для устранения неполадок, возникающих при запуске или запуске контейнера. 


## <a name="billing"></a>Выставление счетов

Контейнеры API распознавания лиц отправляют сведения о выставлении счетов в Azure с помощью ресурса API распознавания лиц в учетной записи Azure. 

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Дополнительные сведения об этих параметрах см. в статье [Настройка контейнеров](./face-resource-container-config.md).

<!--blogs/samples/video coures -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>Сводка

В этой статье вы узнали основные понятия и рабочий процесс загрузки, установки и запуска контейнеров API распознавания лиц. В разделе "Сводка" сделайте следующее.

* API распознавания лиц предоставляет три контейнера Linux для DOCKER, которые обеспечивают извлечение ключевых фраз, определение языка и анализ тональностиности.
* Образы контейнеров загружаются из реестра контейнеров Azure.
* Образы контейнеров выполняются в Docker.
* Можно использовать REST API или пакет SDK для вызова операций в контейнерах API распознавания лиц, указав универсальный код ресурса (URI) узла контейнера.
* При создании экземпляра контейнера необходимо указать сведения о выставлении счетов.

> [!IMPORTANT]
> Cognitive Services контейнеры не лицензированы для запуска без подключения к Azure для измерения. Клиенты должны включить контейнеры для передачи сведений о выставлении счетов в службу контроля использования в любое время. Контейнеры Cognitive Services не отправляют в корпорацию Майкрософт данные клиента, например анализируемые изображения или тексты.

## <a name="next-steps"></a>Следующие шаги

* Параметры конфигурации см. в разделе [Configure Containers](face-resource-container-config.md).
* Дополнительные сведения об обнаружении и определении лиц см. в разделе [Обзор лиц](Overview.md).
* Дополнительные сведения о методах, поддерживаемых контейнером, см. в [API распознавания лиц](//westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).
* Чтобы использовать больше контейнеров Cognitive Services, см. раздел [Cognitive Services Containers](../cognitive-services-container-support.md).
