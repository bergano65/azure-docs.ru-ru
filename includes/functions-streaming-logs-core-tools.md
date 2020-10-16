---
author: ggailey777
ms.author: glenga
ms.date: 7/24/2019
ms.topic: include
ms.service: azure-functions
ms.openlocfilehash: 1928a8238cd73087e3c199675574dd1395f4d76d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "68881319"
---
#### <a name="built-in-log-streaming"></a>Встроенная потоковая передача журналов

Параметр `logstream` позволяет получать журналы потоковой передачи конкретного приложения-функции, работающего в Azure, как показано в следующем примере:

```bash
func azure functionapp logstream <FunctionAppName>
```

#### <a name="live-metrics-stream"></a>Динамический поток метрик

Кроме того, вы можете просмотреть [Live Metrics Stream](../articles/azure-monitor/app/live-stream.md) для своего приложения-функции в новом окне браузера. Для этого включите параметр `--browser`, как показано в следующем примере:

```bash
func azure functionapp logstream <FunctionAppName> --browser
```
