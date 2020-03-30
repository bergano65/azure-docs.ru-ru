---
author: ggailey777
ms.author: glenga
ms.date: 7/24/2019
ms.topic: include
ms.service: azure-functions
ms.openlocfilehash: 1928a8238cd73087e3c199675574dd1395f4d76d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "68881319"
---
#### <a name="built-in-log-streaming"></a>Встроенная потоковая передача журналов

Используйте `logstream` опцию, чтобы начать получать потоковые журналы определенного приложения функции, работающее в Azure, как в следующем примере:

```bash
func azure functionapp logstream <FunctionAppName>
```

#### <a name="live-metrics-stream"></a>Динамический поток метрик

Вы также можете просмотреть [Live Metrics Stream](../articles/azure-monitor/app/live-stream.md) для приложения функции в новом окне браузера, включив опцию, `--browser` как в следующем примере:

```bash
func azure functionapp logstream <FunctionAppName> --browser
```
