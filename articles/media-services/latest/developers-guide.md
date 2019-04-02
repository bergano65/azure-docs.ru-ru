---
title: Пакеты SDK Служб мультимедиа Azure версии 3 в Azure
description: В этой статье представлен обзор того, как приступить к разработке с помощью API Служб мультимедиа Azure версии 3, используя пакеты SDK и службы.
services: media-services
documentationcenter: na
author: Juliako
manager: femila
editor: ''
tags: ''
keywords: ''
ms.service: media-services
ms.devlang: multiple
ms.topic: overview
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 03/20/2019
ms.author: juliako
ms.custom: ''
ms.openlocfilehash: cda6c9cd1f9c8b9305349f0904aeb744ba373711
ms.sourcegitcommit: 87bd7bf35c469f84d6ca6599ac3f5ea5545159c9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/22/2019
ms.locfileid: "58350255"
---
# <a name="start-developing-with-media-services-v3-api-using-sdkstools"></a>Начало разработки с использованием API Служб мультимедиа версии 3 с помощью пакетов SDK и служб

Как разработчик вы можете использовать [REST API](https://aka.ms/ams-v3-rest-ref) Служб мультимедиа или клиентские библиотеки, которые позволяют взаимодействовать с REST API для простого создания, контроля и обслуживания настраиваемых рабочих процессов. API [Служб мультимедиа версии 3](https://aka.ms/ams-v3-rest-sdk) основан на спецификации OpenAPI (ранее известной как Swagger).

В этом разделе приведены ссылки на пакеты SDK, инструменты, документацию. Здесь также предоставлена некоторая полезная информация о различных ENV-файлах для разработки.

## <a name="prerequisites"></a>Предварительные требования

Чтобы начать разработку с использованием Служб мультимедиа, вам потребуется:

- Активная подписка Azure. Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio), прежде чем начинать работу.
- [Learn about fundamental concepts](concepts-overview.md) (Основные понятия служб мультимедиа)
- [Создание учетной записи Служб мультимедиа](create-account-cli-how-to.md)

## <a name="start-developing"></a>Начало разработки

Службы мультимедиа Azure поддерживают следующие пакеты SDK и инструменты 

- [Интерфейс командной строки Azure](https://aka.ms/ams-v3-cli) 
- [ПАКЕТ SDK .NET](https://aka.ms/ams-v3-dotnet-sdk)
- [пакет SDK для Java](https://aka.ms/ams-v3-java-sdk)
- [Пакет SDK для Node.js](https://aka.ms/ams-v3-nodejs-sdk)
- [Пакет SDK для Python](https://aka.ms/ams-v3-python-sdk)
- [Пакет SDK для GO](https://aka.ms/ams-v3-go-sdk)
- [Пакет SDK для Ruby](https://aka.ms/ams-v3-ruby-sdk)

### <a name="azure-media-services-explorer"></a>Обозреватель Служб мультимедиа Azure

[Обозреватель Служб мультимедиа Azure](https://github.com/Azure/Azure-Media-Services-Explorer) (AMSE) — это инструмент, доступный для клиентов Windows, которые хотят узнать о Службах мультимедиа. AMSE — это приложение Winforms или C#, которое отправляет, скачивает, кодирует, транслирует видео по запросу и в режиме реального времени с помощью Служб мультимедиа. Инструмент AMSE предназначен для клиентов, которые хотят протестировать Службы мультимедиа без написания кода. Код AMSE предоставляется как ресурс для клиентов, которым необходима разработка с помощью Служб мультимедиа.

AMSE — это проект с открытым кодом, поддержка которого предоставляется сообществом (о проблемах можно сообщать по ссылке https://github.com/Azure/Azure-Media-Services-Explorer/issues). Microsoft Open Source Code of Conduct [Microsoft Open Source Code of Conduct](https://opensource.microsoft.com/codeofconduct/). Дополнительные сведения см. в [вопросах и ответах по правилам поведения](https://opensource.microsoft.com/codeofconduct/faq/). С любыми другими вопросами или комментариями обращайтесь по адресу opencode@microsoft.com.

## <a name="rest"></a>REST

Чтобы начать разработку с помощью REST API Служб мультимедиа, установите клиент REST. Например, **Postman**, **Visual Studio Code** с подключаемым модулем REST или **Telerik Fiddler**. Мы используем [Postman](media-rest-apis-with-postman.md) для примеров Служб мультимедиа версии 3.

Изучите документацию [справочника по REST API](https://aka.ms/ams-v3-rest-ref) и просмотрите следующие примеры:

- [Руководство Кодирование удаленного файла на основе URL-адреса и потоковой передачи видео с помощью REST](stream-files-tutorial-with-rest.md)
- [Передача файлов в учетную запись Служб мультимедиа с помощью REST](upload-files-rest-how-to.md)
- [Создание фильтров с помощью REST API Служб мультимедиа](filters-dynamic-manifest-rest-howto.md)
- [REST API на основе Azure Resource Manager](https://github.com/Azure-Samples/media-services-v3-arm-templates)

<!-- ## CLI -->
[!INCLUDE [media-services-cli-instructions](../../../includes/media-services-cli-instructions.md)]

Изучите документацию [справочника по Azure CLI](https://aka.ms/ams-v3-cli-ref) и просмотрите следующие примеры:

- [Масштабирование зарезервированных единиц мультимедиа с помощью CLI](media-reserved-units-cli-how-to.md)
- [Создание учетной записи Служб мультимедиа с помощью CLI](./scripts/cli-create-account.md) 
- [Сброс данных учетной записи с помощью CLI](./scripts/cli-reset-account-credentials.md)
- [Создание средства с помощью CLI](./scripts/cli-create-asset.md)
- [Отправка файла с помощью CLI](./scripts/cli-upload-file-asset.md)
- [Создание преобразований с помощью CLI](./scripts/cli-create-transform.md)
- [Создание заданий с помощью CLI](./scripts/cli-create-jobs.md)
- [Создание сетки событий с помощью CLI](./scripts/cli-create-event-grid.md)
- [Публикация ресурса с помощью CLI](./scripts/cli-publish-asset.md)
- [Фильтрация с помощью CLI](filters-dynamic-manifest-cli-howto.md)

## <a name="net"></a>.NET

Изучите документацию [справочника по .NET](https://aka.ms/ams-v3-dotnet-ref) и просмотрите следующие примеры:

- [Руководство Отправка, кодирование и потоковая передача видео с помощью .NET](stream-files-tutorial-with-api.md) 
- [Руководство Потоковая трансляция в реальном времени с помощью Служб мультимедиа Azure версии 3 и .NET](stream-live-tutorial-with-api.md)
- [Руководство Анализ видео с помощью Служб мультимедиа версии 3 и .NET](analyze-videos-tutorial-with-api.md)
- [Создание входных данных задания из локального файла с помощью .NET](job-input-from-local-file-how-to.md)
- [Создание входных данных задания из URL-адреса HTTPS с помощью .NET](job-input-from-http-how-to.md)
- [Кодирование с помощью пользовательского преобразования с помощью .NET](customize-encoder-presets-how-to.md)
- [Использование динамического шифрования AES-128 и службы доставки ключей с помощью .NET](protect-with-aes128.md)
- [Использование динамического шифрования DRM и службы доставки лицензий с помощью .NET](protect-with-drm.md)
- [Получение ключа подписи из имеющейся политики с помощью .NET](get-content-key-policy-dotnet-howto.md)
- [Создание фильтров с помощью Служб мультимедиа для .NET](filters-dynamic-manifest-dotnet-howto.md)
- [Дополнительные примеры видео по запросу с использованием Функций Azure версии 2 и Cлужб мультимедиа версии 3](https://aka.ms/ams3functions)

## <a name="java"></a>Java

Ознакомьтесь с документацией по [Java](https://aka.ms/ams-v3-java-ref) Служб мультимедиа.

## <a name="nodejs"></a>Node.js

Изучите справочную документацию [Node.js](https://aka.ms/ams-v3-nodejs-ref) Служб мультимедиа и ознакомьтесь с [примерами](https://github.com/Azure-Samples/media-services-v3-node-tutorials), показывающими как использовать API Служб мультимедиа с Node.js.

## <a name="python"></a>Python

Ознакомьтесь со справочной документации по [Python](https://aka.ms/ams-v3-python-ref) Служб мультимедиа.

## <a name="go"></a>Go

Ознакомьтесь со справочной документации по [Go](https://aka.ms/ams-v3-go-ref) Служб мультимедиа.

## <a name="next-steps"></a>Дополнительная информация

- [Создание учетной записи служб мультимедиа Azure](create-account-cli-how-to.md)
- [Доступ к API Служб мультимедиа Azure с помощью Azure CLI](access-api-cli-how-to.md)

