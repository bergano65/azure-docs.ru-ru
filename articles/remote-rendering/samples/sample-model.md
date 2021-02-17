---
title: Примеры моделей
description: Список источников для примеров моделей.
author: florianborn71
ms.author: flborn
ms.date: 01/29/2020
ms.topic: sample
ms.openlocfilehash: 6817601659c841ca98031f4e3e1590743bbed171
ms.sourcegitcommit: 7ec45b7325e36debadb960bae4cf33164176bc24
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/16/2021
ms.locfileid: "100530541"
---
# <a name="sample-models"></a>Примеры моделей

В этой статье приводится список некоторых ресурсов для примеров данных, которые можно использовать для тестирования службы "Удаленная отрисовка Azure".

## <a name="built-in-sample-model"></a>Стандартный пример модели

Мы предоставляем стандартный пример модели, который всегда можно скачать по URL-адресу **builtin://Engine**.

![Пример модели](./media/sample-model.png "Пример модели")

Статистика модели:

| Имя | Значение |
|-----------|:-----------|
| [Требуемый размер сервера](../reference/vm-sizes.md) | standard |
| Количество треугольников | 18,7 млн |
| Количество подвижных деталей | 2073 |
| Количество материалов | 94 |

## <a name="third-party-data"></a>Сторонние данные

Khronos Group хранит набор примеров моделей glTF для тестирования. Служба "Удаленная отрисовка Azure" поддерживает формат glTF в текстовых ( *.gltf*) и двоичных файлах ( *.glb*). Мы рекомендуем использовать модели PBR для получения лучших визуальных результатов:

* [Примеры моделей glTF](https://github.com/KhronosGroup/glTF-Sample-Models)

## <a name="next-steps"></a>Дальнейшие действия

* [Краткое руководство. Отрисовка модели с помощью Unity](../quickstarts/render-model.md)
* [Краткое руководство. Преобразование модели для отрисовки](../quickstarts/convert-model.md)
