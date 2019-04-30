---
title: Примеры Azure CLI для Служб мультимедиа Azure | Документация Майкрософт
description: Примеры Azure CLI для Служб мультимедиа Azure
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.custom: seodec18
ms.date: 03/11/2019
ms.author: juliako
ms.openlocfilehash: dee7f831562dc1f4b2478d13b204aab1d8455e1e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60733187"
---
# <a name="azure-cli-examples-for-azure-media-services"></a>Примеры Azure CLI для Служб мультимедиа Azure

В следующей таблице содержатся ссылки на примеры Azure CLI для Служб мультимедиа Azure.

## <a name="examples"></a>Примеры

|  |  |
|---|---|
|**Масштабирование**||
| [Масштабирование зарезервированных единиц мультимедиа](media-reserved-units-cli-how-to.md)|Для заданий анализа аудио и видео, которые активируют Службы мультимедиа версии 3 или Индексатор видео, настоятельно рекомендуется подготовить к работе вашу учетную запись с помощью 10 единиц MRU S3. <br/>В скрипте показано, как масштабировать зарезервированные единицы мультимедиа (MRU) с помощью CLI.|
|**Учетная запись.**||
| [Создание учетной записи Служб мультимедиа](create-account-cli-how-to.md) | Этот скрипт создает учетную запись служб мультимедиа Azure. |
| [Сброс данных учетной записи](./scripts/cli-reset-account-credentials.md)|Сброс данных учетной записи и восстановление параметров в файле app.config обратно.|
|**Ресурсы**||
| [Создание ресурсов](./scripts/cli-create-asset.md)|Создание ресурса служб мультимедиа для передачи содержимого.|
| [Передача файла](./scripts/cli-upload-file-asset.md)|Передача локального файла в контейнер хранилища.|
| **Преобразования** и **задания**||
| [Создание преобразований](./scripts/cli-create-transform.md)|Показано, как создавать преобразования. Преобразования описывают простой рабочий процесс задач для обработки видео- и аудиофайлов (его часто называют "рецептом").<br/> Всегда следует выполнять проверку при преобразовании с нужное имя и «recipe» уже существует. Если он существует, используйте его. |
| [Кодирование с помощью пользовательского преобразования](custom-preset-cli-howto.md) | Показано, как создать пользовательскую предустановку для конкретных требований сценария или устройства.|
| [Создание заданий](./scripts/cli-create-jobs.md)|Отправка задания для простого преобразования с кодированием с помощью URL-адреса HTTPs.|
| [Создание сетки событий](./scripts/cli-create-event-grid.md)|Создание подписки службы "Сетка событий" уровня учетной записи для изменения состояния заданий.|
| **Доставка**||
| [Публикация ресурса](./scripts/cli-publish-asset.md)| Создание указателя потоковой передачи и получение URL-адреса потоковой передачи. |
| [Фильтр](filters-dynamic-manifest-cli-howto.md)| Настройка фильтра для ресурса-контейнера видео по запросу и инструкции по созданию [фильтров учетных записей](https://docs.microsoft.com/cli/azure/ams/account-filter?view=azure-cli-latest) и [фильтров ресурсов-контейнеров](https://docs.microsoft.com/cli/azure/ams/asset-filter?view=azure-cli-latest) с помощью CLI. 

## <a name="see-also"></a>См. также

- [Интерфейс командной строки Azure](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)
- [Краткое руководство. Потоковая передача видеофайлов с помощью CLI](stream-files-cli-quickstart.md)
