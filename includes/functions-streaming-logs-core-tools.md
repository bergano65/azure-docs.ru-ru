---
author: ggailey777
ms.author: glenga
ms.date: 7/24/2019
ms.topic: include
ms.service: azure-functions
ms.openlocfilehash: 1928a8238cd73087e3c199675574dd1395f4d76d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "68881319"
---
#### <a name="built-in-log-streaming"></a>Встроенная потоковая передача журналов

Используйте `logstream` параметр, чтобы начать получать журналы потоковой передачи конкретного приложения-функции, работающего в Azure, как показано в следующем примере:

```bash
func azure functionapp logstream <FunctionAppName>
```

#### <a name="live-metrics-stream"></a>Динамический поток метрик

Вы также можете просмотреть [Live Metrics Stream](../articles/azure-monitor/app/live-stream.md) для приложения-функции в новом окне браузера, включив `--browser` параметр, как показано в следующем примере:

```bash
func azure functionapp logstream <FunctionAppName> --browser
```
