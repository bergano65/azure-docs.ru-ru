---
title: Установка и запуск контейнеров - Лицо
titleSuffix: Azure Cognitive Services
description: Эта статья показывает вам, как загружать, устанавливать и запускать контейнеры для Face в этом пошаговом уроке.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: dapine
ms.openlocfilehash: e467b195ab1e2124286bfef74d7d1b71a4d99dd6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "76165979"
---
# <a name="install-and-run-face-containers-preview"></a>Установка и запуск контейнеров face (Предварительный просмотр)

Azure Cognitive Services Face предоставляет стандартизированный linux-контейнер для Docker, который обнаруживает человеческие лица в изображениях. Он также определяет атрибуты, которые включают в себя лица ориентиры, такие как носы и глаза, пол, возраст, и другие машины предсказал черты лица. В дополнение к обнаружению, Face может проверить, если два лица в одном изображении или разные изображения одинаковы с помощью оценки доверия. Face также может сравнить лица с базой данных, чтобы увидеть, если похожие или идентичные лица уже существует. Он также может организовать аналогичные лица в группы, используя общие визуальные черты.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

Перед использованием контейнеров службы Face необходимо выполнить следующие предпосылки.

|Обязательно|Назначение|
|--|--|
|Модуль Docker| Докер-двигатель должен быть установлен на [компьютере-хозяине.](#the-host-computer) Docker предоставляет пакеты, которые настраивают среду с Docker для [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) и [Linux](https://docs.docker.com/engine/installation/#supported-platforms). См. [общие сведения о Docker и контейнерах](https://docs.docker.com/engine/docker-overview/).<br><br> Docker нужно настроить таким образом, чтобы контейнеры могли подключать и отправлять данные о выставлении счетов в Azure. <br><br> На Windows Docker также должен быть настроен для поддержки контейнеров Linux.<br><br>|
|Опыт работы с Docker | Вам необходимо базовое понимание концепций Docker, таких как реестры, репозитории, контейнеры и изображения контейнеров. Вам также необходимо `docker` знание основных команд.| 
|Фейс-ресурс |Для использования контейнера необходимо:<br><br>Ресурс Azure **Face** и связанный с ним ключ API и конечный uri. Оба значения доступны на страницах **«Обзор»** и **«Ключи»** для ресурса. Они необходимы для запуска контейнера.<br><br>**«API_KEY:** Один из двух доступных ключей ресурса на странице **Ключей**<br><br>**(ENDPOINT_URI:** конечная точка, представленная на странице **Обзор**

[!INCLUDE [Gathering required container parameters](../containers/includes/container-gathering-required-parameters.md)]

## <a name="request-access-to-the-private-container-registry"></a>Запрос доступа к частному реестру контейнеров

[!INCLUDE [Request access to private container registry](../../../includes/cognitive-services-containers-request-access.md)]

### <a name="the-host-computer"></a>Главный компьютер

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>Требования к контейнеру и рекомендации

В следующей таблице описаны минимальные и рекомендуемые ядра процессора и память для каждого контейнера обслуживания Face.

| Контейнер | Минимальные | Рекомендуемая | Транзакций в секунду<br>(Минимальное, максимальное)|
|-----------|---------|-------------|--|
|Распознавание лиц | 1 ядро, 2-ГБ памяти | 1 ядро, 4-ГБ памяти |10, 20|

* Каждое ядро должно быть не менее 2,6 ГГц или быстрее.
* Транзакции в секунду (TPS).

Ядро и память соответствуют параметрам `--cpus` и `--memory`, которые используются как часть команды `docker run`.

## <a name="get-the-container-image-with-docker-pull"></a>Получить изображение контейнера с докертянуть

Доступны изображения контейнеров для службы Face. 

| Контейнер | Хранилище |
|-----------|------------|
| Распознавание лиц | `containerpreview.azurecr.io/microsoft/cognitive-services-face:latest` |

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="docker-pull-for-the-face-container"></a>docker pull для контейнера распознавания лиц

```
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-face:latest
```

## <a name="use-the-container"></a>Использование контейнера

После размещения контейнера на [главном компьютере](#the-host-computer) выполните приведенные ниже инструкции для работы с ним.

1. [Запустите контейнер](#run-the-container-with-docker-run) с необходимыми настройками выставления счетов. Доступны дополнительные [примеры](./face-resource-container-config.md#example-docker-run-commands) команды `docker run`. 
1. [Запрос конечная точка прогнозирования контейнера.](#query-the-containers-prediction-endpoint) 

## <a name="run-the-container-with-docker-run"></a>Запуск контейнера с докером

Воспользуйтесь командой [docker run](https://docs.docker.com/engine/reference/commandline/run/) для запуска контейнера. Обратитесь к [сбору необходимых параметров](#gathering-required-parameters) `{ENDPOINT_URI}` для `{API_KEY}` получения подробной информации о том, как получить и значения.

[Приведены примеры](face-resource-container-config.md#example-docker-run-commands) `docker run` команды.

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-face \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Эта команда:

* Запускает контейнер с изображением контейнера.
* Выделяет одно ядро процессора и 4 ГБ памяти.
* Выставляет порт TCP 5000 и выделяет псевдо TTY для контейнера.
* автоматически удаляет контейнер после завершения его работы. Образ контейнера остается доступным на главном компьютере. 

Доступны дополнительные [примеры](./face-resource-container-config.md#example-docker-run-commands) команды `docker run`. 

> [!IMPORTANT]
> `Billing`Необходимо `Eula`указать `ApiKey` параметры для запуска контейнера или не запускать контейнер. Дополнительные сведения см. в [разделе о выставлении счетов](#billing).

[!INCLUDE [Running multiple containers on the same host](../../../includes/cognitive-services-containers-run-multiple-same-host.md)]


## <a name="query-the-containers-prediction-endpoint"></a>Запрос конечной точки прогнозирования контейнера

Контейнер предоставляет API запроса конечной точки прогнозирования на основе REST. 

Используйте узел `http://localhost:5000` для API контейнера.


<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>Остановка контейнера

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Устранение неполадок

Если вы запустите контейнер с выходным [креплением](./face-resource-container-config.md#mount-settings) и включенжурнал, контейнер генерирует файлы журнала, которые полезны для устранения неполадок, которые возникают при запуске или запуске контейнера.

[!INCLUDE [Cognitive Services FAQ note](../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>Выставление счетов

Контейнеры службы Face отправляют платежную информацию в Azure, используя ресурс Face в учетной записи Azure. 

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Дополнительные сведения об этих параметрах см. в статье [Настройка контейнеров](./face-resource-container-config.md).

<!--blogs/samples/video coures -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>Сводка

В этой статье вы изучили концепции и рабочий процесс для загрузки, установки и запуска контейнеров обслуживания Face. В разделе "Сводка" сделайте следующее.

* Изображения контейнеров загружаются из реестра контейнеров Azure.
* Образы контейнеров выполняются в Docker.
* Можно использовать aPI REST или SDK для вызова операций в контейнерах обслуживания Face, указав хост URI контейнера.
* Необходимо указать платежную информацию при мгновенном воспроизведении контейнера.

> [!IMPORTANT]
> Контейнеры Cognitive Services не имеют лицензии на запуск без подключения к Azure для измерения. Клиенты должны постоянно сообщать контейнерам информацию о выставлении счетов в службу учета. Контейнеры Cognitive Services не отправляют в корпорацию Майкрософт данные клиента, например анализируемые изображения или тексты.

## <a name="next-steps"></a>Дальнейшие действия

* Для настроек конфигурации [см.](face-resource-container-config.md)
* Чтобы узнать больше о том, как обнаружить и идентифицировать лица, смотрите [обзор Face](Overview.md).
* Для получения информации о методах, [Face API](//westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)поддерживаемых контейнером, см.
* Чтобы использовать больше контейнеров Cognitive Services, [см.](../cognitive-services-container-support.md)
