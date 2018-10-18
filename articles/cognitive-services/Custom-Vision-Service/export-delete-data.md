---
title: Экспорт и удаление данных — пользовательская служба визуального распознавания
titlesuffix: Azure Cognitive Services
description: Узнайте, как экспортировать и удалять данные в пользовательской службе визуального распознавания.
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: conceptual
ms.date: 05/25/2018
ms.author: pafarley
ms.openlocfilehash: d89832b7b68c9a2c0697fa5c97ce1aa83d5d1ee8
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/16/2018
ms.locfileid: "49338477"
---
# <a name="export-or-delete-user-data-in-custom-vision"></a>Экспорт и удаление данных пользователя в Пользовательской службе визуального распознавания

Content Moderator собирает данные пользователей, необходимые для работы службы, но пользователи могут полностью контролировать просмотр, экспорт и удаление своих данных с помощью [API обучения](https://go.microsoft.com/fwlink/?linkid=865446) Пользовательской службы визуального распознавания.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

Дополнительные сведения о том, как экспортировать и удалять данные пользователей в Пользовательской службе визуального распознавания, см. в следующей таблице.

| Данные | Операция экспорта | Операция удаления. |
| ---- | ---------------- | ---------------- |
| Сведения об учетной записи (ключи подписки) | [GetAccountInfo](https://go.microsoft.com/fwlink/?linkid=865446) | Удаляются с помощью портала Azure (подписки Azure) или с помощью кнопки "Удалить учетную запись" на странице параметров CustomVision.ai (подписки учетной записи Майкрософт) |
| Сведения об итерациях | [GetIteration](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteIteration](https://go.microsoft.com/fwlink/?linkid=865446) |
| Сведения о производительности итераций | [GetIterationPerformance](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteIteration](https://go.microsoft.com/fwlink/?linkid=865446) |
| Список итераций | [GetIterations](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteIteration](https://go.microsoft.com/fwlink/?linkid=865446) |
| Проекты и сведения о проектах | [GetProject](https://go.microsoft.com/fwlink/?linkid=865446) и [GetProjects](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteProject](https://go.microsoft.com/fwlink/?linkid=865446) |
| Теги изображений | [GetTag](https://go.microsoft.com/fwlink/?linkid=865446) и [GetTags](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteTag](https://go.microsoft.com/fwlink/?linkid=865446) |
| Образы | [GetTaggedImages](https://go.microsoft.com/fwlink/?linkid=865446) (предоставляется URI для скачивания изображения) и [GetUntaggedImages](https://go.microsoft.com/fwlink/?linkid=865446) (предоставляется URI для скачивания изображения) | [DeleteImages](https://go.microsoft.com/fwlink/?linkid=865446) |
| Экспортированные модели | [GetExports](https://go.microsoft.com/fwlink/?linkid=865446) | Удаляются при удалении учетной записи |
