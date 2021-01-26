---
title: Действия Power Query в фабрике данных Azure
description: Узнайте, как использовать Power Query действия для функций структурирование данных в конвейере фабрики данных.
services: data-factory
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/18/2021
ms.openlocfilehash: c0ad769ceba4fc3fa7f602d70188ea1942ca73aa
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/26/2021
ms.locfileid: "98791700"
---
# <a name="power-query-activity-in-data-factory"></a>Активность Power Query в фабрике данных

Действие Power Query позволяет создавать и выполнять Power Queryные гибридные структурирование для выполнения масштабирования данных в конвейере фабрики данных. Вы можете создать новое Power Query из меню "создать ресурсы" или добавить в конвейер действие "Электропитание".

![Снимок экрана, на котором показано Power Query в области "ресурсы фабрики".](media/data-flow/power-query-wrangling.png)

Ранее структурирование данных в фабрике данных Azure был создан с помощью пункта меню поток данных. Это было изменено для создания новой Power Query действия. Вы можете работать непосредственно внутри Power Query комбинированного редактора, чтобы выполнить интерактивное исследование данных, а затем сохранить работу. После завершения можно выполнить действие Power Query и добавить его в конвейер. Фабрика данных Azure автоматически масштабирует ее и эксплуатацию данные структурирование с помощью среды потока данных фабрики данных Azure.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4MFYn]

## <a name="translation-to-data-flow-script"></a>Преобразование в скрипт потока данных

Чтобы добиться масштабирования с помощью действия Power Query, фабрика данных Azure преобразует ```M``` скрипт в сценарий потока данных, чтобы можно было выполнять Power Query в нужном масштабе с помощью среды потока данных фабрики данных Azure в среде Spark. Создайте поток данных структурирование, используя подготовку данных без кода. Список доступных функций см. в разделе [функции преобразования](wrangling-functions.md).

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения об основных понятиях структурирование данных с помощью [Power Query в фабрике данных Azure](wrangling-tutorial.md)
