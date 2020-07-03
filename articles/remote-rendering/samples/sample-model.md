---
title: Примеры моделей
description: Список источников для примеров моделей.
author: florianborn71
ms.author: flborn
ms.date: 01/29/2020
ms.topic: sample
ms.openlocfilehash: 0c28d36c7934fbbac0ddd11562c8dc237b0360ee
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80678495"
---
# <a name="sample-models"></a>Примеры моделей

В этой статье приводится список некоторых ресурсов для примеров данных, которые можно использовать для тестирования службы "Удаленная отрисовка Azure".

## <a name="built-in-sample-model"></a>Стандартный пример модели

Мы предоставляем стандартный пример модели, который всегда можно скачать по URL-адресу **builtin://Engine**.

![Пример модели](./media/sample-model.png "Пример модели")

Статистика модели:

| Имя | Значение |
|-----------|:-----------|
| [Требуемый размер виртуальной машины](../how-tos/session-rest-api.md#create-a-session) | standard |
| Количество треугольников | 18,7 млн |
| Количество подвижных деталей | 2073 |
| Количество материалов | 94 |

## <a name="third-party-data"></a>Сторонние данные

Khronos Group хранит набор примеров моделей glTF для тестирования. Служба "Удаленная отрисовка Azure" поддерживает формат glTF в текстовых ( *.gltf*) и двоичных файлах ( *.glb*). Мы рекомендуем использовать модели PBR для получения лучших визуальных результатов:

* [Примеры моделей glTF](https://github.com/KhronosGroup/glTF-Sample-Models)

## <a name="next-steps"></a>Дальнейшие действия

* [Краткое руководство. Отрисовка модели с помощью Unity](../quickstarts/render-model.md)
* [Краткое руководство. Преобразование модели для отрисовки](../quickstarts/convert-model.md)
