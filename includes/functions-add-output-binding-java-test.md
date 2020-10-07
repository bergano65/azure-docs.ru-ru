---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/18/2020
ms.author: glenga
ms.openlocfilehash: eb54439f89cc2443eeed2d3b63dfbe7fedb4bf17
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/05/2020
ms.locfileid: "80673441"
---
## <a name="update-the-tests"></a>Обновление тестов

Поскольку архетип также создает набор тестов, необходимо обновить эти тесты для обработки нового параметра `msg` в сигнатуре метода `run`.  

Найдите местоположение тестового кода в _src/test/java_, откройте файл проекта *Function.java* и замените строку кода в `//Invoke` следующим кодом.

:::code language="java" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/src/test/java/com/function/FunctionTest.java" range="48-50":::
