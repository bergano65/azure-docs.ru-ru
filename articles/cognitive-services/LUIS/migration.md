---
title: Обзор миграции — LUIS
description: Общие сведения о пути перехода
ms.topic: how-to
ms.date: 05/22/2020
ms.openlocfilehash: 4080dd27414c2da551b60ad430278c18768337c9
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/19/2020
ms.locfileid: "88587826"
---
# <a name="migration-in-luis"></a>Миграция в LUIS

Несколько элементов находятся на пути миграции. Используйте следующую таблицу, чтобы понять, что влияет на воздействие и когда требуется выполнить полную миграцию.

|Область|Описание|Дата завершения миграции|
|--|--|--|
|[API-интерфейсы прогнозирования](luis-migration-api-v3.md)|Переход на API V3.|TBD|
|[Разработка API-интерфейсов](luis-migration-authoring-entities.md)|Переход на API V3.|TBD|
|[Создание ресурса](luis-migration-authoring.md)|Выполните миграцию без ресурсов разработки, чтобы использовать ресурс LUIS Authoring на портале LUIS.|2 ноября 2020 г. |
|[Обязательный компонент](luis-migration-authoring-entities.md#api-change-constraint-replaced-with-required-feature)|Это изменение было внесено в мае 2020 на Конференции//Build и относится только к API-интерфейсам разработки версии 3, где приложение использует ограниченную функцию.|19 июня 2020 г.|
|[Составная сущность](migrate-from-composite-entity.md)|Обновите составной объект до сущности машинного обучения, чтобы создать сущность, которая получает более полные прогнозы с улучшенными декомпосабилити для отладки сущности.|TBD|
