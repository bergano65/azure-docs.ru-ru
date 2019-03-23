---
title: Экспорт и удаление данных — пользовательская служба визуального распознавания
titlesuffix: Azure Cognitive Services
description: Узнайте, как экспортировать и удалять данные в пользовательской службе визуального распознавания.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: pafarley
ms.openlocfilehash: e662e61a9df45cf3d57d5698337a26b7b8fc55a3
ms.sourcegitcommit: 87bd7bf35c469f84d6ca6599ac3f5ea5545159c9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/22/2019
ms.locfileid: "58349745"
---
# <a name="export-or-delete-user-data-in-custom-vision"></a>Экспорт и удаление данных пользователя в Пользовательской службе визуального распознавания

Custom Vision собирает данные пользователя для работы службы, но у клиента есть полный контроль над Просмотр, экспорт и удаление своих данных с помощью Custom Vision [API-интерфейсы обучения](https://go.microsoft.com/fwlink/?linkid=865446).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

Чтобы узнать, как экспортировать и удалять данные пользователей в Custom Vision, см. в разделе ниже.

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
