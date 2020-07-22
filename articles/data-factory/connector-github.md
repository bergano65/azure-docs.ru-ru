---
title: Подключение к GitHub
description: Использование GitHub для указания ссылок на сущности общих моделей данных
author: djpmsft
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 06/03/2020
ms.author: daperlov
ms.openlocfilehash: 0e17580524d6a67934aed83c6f745583b92e2422
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84771042"
---
# <a name="use-github-to-read-common-data-model-entity-references"></a>Использование GitHub для чтения ссылок на сущности общих моделей данных

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Соединитель GitHub в фабрике данных Azure используется только для получения схемы ссылки на сущность для формата [общей модели данных](format-common-data-model.md) в потоке сопоставления данных.

## <a name="linked-service-properties"></a>Свойства связанной службы

Для связанной службы GitHub поддерживаются следующие свойства.

| Свойство. | Описание | Обязательно |
|:--- |:--- |:--- |
| type | Для свойства Type необходимо задать значение **GitHub**. | да
| userName | Имя пользователя GitHub | да |
| password | Пароль GitHub | да |

## <a name="next-steps"></a>Следующие шаги

Создайте [исходный набор данных](data-flow-source.md) в сопоставлении потока данных.