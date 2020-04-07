---
title: Ограничения
description: Ограничения кода для функций SDK
author: erscorms
ms.author: erscor
ms.date: 02/11/2020
ms.topic: reference
ms.openlocfilehash: 6e23c716a965cdf178c9f847900e66c141d5d7eb
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80680340"
---
# <a name="limits"></a>Ограничения

Ряд функций имеют ограничения по размеру или количеству из-за внутренних деталей запущенной системы.

## <a name="azure-frontend"></a>Лазурный фронтенд

* Всего экземпляров AzureFrontend за процесс: 16.
* Всего экземпляров AzureSession в AzureFrontend: 16.

## <a name="objects"></a>Объекты

* Всего допустимых объектов одного типа (Entity, CutPlaneComponent и т.д.): 16 777 215.
* Всего допустимых активных плоскостей разреза: 8.

## <a name="materials"></a>Материалы

* Всего допустимых материалов в активе: 65 535.

## <a name="overall-number-of-polygons"></a>Общее количество полигонов

Допустимое количество полигонов для всех загруженных моделей зависит от размера VM, переданного в [управление сеансами REST API:](../how-tos/session-rest-api.md#create-a-session)

| Размер виртуальной машины | Максимальное количество полигонов |
|:--------|:------------------|
|standard| 20 миллионов |
|Премиум| без ограничений |



