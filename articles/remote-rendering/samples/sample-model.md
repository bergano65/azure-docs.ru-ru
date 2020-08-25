---
title: Примеры моделей
description: Список источников для примеров моделей.
author: florianborn71
ms.author: flborn
ms.date: 01/29/2020
ms.topic: sample
ms.openlocfilehash: 8e5dcb6c9dfa08efc0889fcab779d6cb333d2330
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/18/2020
ms.locfileid: "88507541"
---
# <a name="sample-models"></a>Примеры моделей

В этой статье приводится список некоторых ресурсов для примеров данных, которые можно использовать для тестирования службы "Удаленная отрисовка Azure".

## <a name="built-in-sample-model"></a>Стандартный пример модели

Мы предоставляем стандартный пример модели, который всегда можно скачать по URL-адресу **builtin://Engine**.

![Пример модели](./media/sample-model.png "Пример модели")

Статистика модели:

| Имя | Значение |
|-----------|:-----------|
| [Требуемый размер сервера](../how-tos/session-rest-api.md#create-a-session) | standard |
| Количество треугольников | 18,7 млн |
| Количество подвижных деталей | 2073 |
| Количество материалов | 94 |

## <a name="third-party-data"></a>Сторонние данные

Khronos Group хранит набор примеров моделей glTF для тестирования. Служба "Удаленная отрисовка Azure" поддерживает формат glTF в текстовых ( *.gltf*) и двоичных файлах ( *.glb*). Мы рекомендуем использовать модели PBR для получения лучших визуальных результатов:

* [Примеры моделей glTF](https://github.com/KhronosGroup/glTF-Sample-Models)

## <a name="next-steps"></a>Дальнейшие действия

* [Краткое руководство. Отрисовка модели с помощью Unity](../quickstarts/render-model.md)
* [Краткое руководство. Преобразование модели для отрисовки](../quickstarts/convert-model.md)
