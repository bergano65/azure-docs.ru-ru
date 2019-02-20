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
ms.date: 02/11/2019
ms.author: juliako
ms.openlocfilehash: 919dbd057f2fa1d762775ed799505796a420864a
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/12/2019
ms.locfileid: "56109254"
---
# <a name="azure-cli-examples-for-azure-media-services"></a>Примеры Azure CLI для Служб мультимедиа Azure

В следующей таблице содержатся ссылки на примеры Azure CLI для Служб мультимедиа Azure.

## <a name="examples"></a>Примеры

|  |  |
|---|---|
|**Масштабирование**||
| [Масштабирование зарезервированных единиц мультимедиа](media-reserved-units-cli-how-to.md)|Для заданий анализа аудио и видео, которые активируют Службы мультимедиа версии 3 или Индексатор видео, настоятельно рекомендуется подготовить к работе вашу учетную запись с помощью 10 единиц MRU S3. <br/>В скрипте показано, как масштабировать зарезервированные единицы мультимедиа (MRU) с помощью CLI.|
|**Учетная запись.**||
| [Создание учетной записи Служб мультимедиа](./scripts/cli-create-account.md) | Создает учетную запись Служб мультимедиа Azure, а также субъект-службу, с помощью которого можно получить доступ к API-интерфейсам и программным образом управлять учетной записью. |
| [Сброс данных учетной записи](./scripts/cli-reset-account-credentials.md)|Сброс данных учетной записи и восстановление параметров в файле app.config обратно.|
|**Ресурсы**||
| [Создание ресурсов](./scripts/cli-create-asset.md)|Создание ресурса служб мультимедиа для передачи содержимого.|
| [Передача файла](./scripts/cli-upload-file-asset.md)|Передача локального файла в контейнер хранилища.|
| **Преобразования** и **задания**||
| [Создание преобразований](./scripts/cli-create-transform.md)|Показано, как создавать преобразования. Преобразования описывают простой рабочий процесс задач для обработки видео- и аудиофайлов (его часто называют "рецептом").<br/> Всегда проверяйте, существует ли уже "рецепт" и преобразование с нужным именем. Если он существует, используйте его. |
| [Создание заданий](./scripts/cli-create-jobs.md)|Отправка задания для простого преобразования с кодированием с помощью URL-адреса HTTPs.|
| [Создание сетки событий](./scripts/cli-create-event-grid.md)|Создание подписки службы "Сетка событий" уровня учетной записи для изменения состояния заданий.|
| **Доставка**||
| [Публикация ресурса](./scripts/cli-publish-asset.md)| Создание указателя потоковой передачи и получение URL-адреса потоковой передачи. |
| [Filter](filters-dynamic-manifest-cli-howto.md)| Настройка фильтра для ресурса-контейнера видео по запросу и инструкции по созданию [фильтров учетных записей](https://docs.microsoft.com/cli/azure/ams/account-filter?view=azure-cli-latest) и [фильтров ресурсов-контейнеров](https://docs.microsoft.com/cli/azure/ams/asset-filter?view=azure-cli-latest) с помощью CLI. 

## <a name="see-also"></a>См. также

[Интерфейс командной строки Azure](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)
