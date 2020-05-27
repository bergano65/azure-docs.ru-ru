---
title: Обзор миграции — LUIS
description: Общие сведения о пути перехода
ms.topic: how-to
ms.date: 05/22/2020
ms.openlocfilehash: a27945b5ac3dc2625c9520a8dd413b774b5d7adf
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/25/2020
ms.locfileid: "83837458"
---
# <a name="migration-in-luis"></a>Миграция в LUIS

Несколько элементов находятся на пути миграции. Используйте следующую таблицу, чтобы понять, что влияет на воздействие и когда требуется выполнить полную миграцию.

|Область|Описание|Дата завершения миграции|
|--|--|--|
|[API-интерфейсы прогнозирования](luis-migration-api-v3.md)|Переход на API V3.|TBD|
|[Разработка API-интерфейсов](luis-migration-authoring-entities.md)|Переход на API V3.|TBD|
|[Создание ресурса](luis-migration-authoring.md)|Выполните миграцию без ресурсов разработки, чтобы использовать ресурс LUIS Authoring на портале LUIS.|30 августа 2020 г. |
|[Обязательный компонент](luis-migration-authoring-entities.md#api-change-constraint-replaced-with-required-feature)|Это изменение было внесено в мае 2020 на Конференции//Build и относится только к API-интерфейсам разработки версии 3, где приложение использует ограниченную функцию.|19 июня 2020 г.|
|[Составная сущность](migrate-from-composite-entity.md)|Обновите составной объект до сущности машинного обучения, чтобы создать сущность, которая получает более полные прогнозы с улучшенными декомпосабилити для отладки сущности.|TBD|
