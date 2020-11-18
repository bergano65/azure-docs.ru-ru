---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/18/2020
ms.author: glenga
ms.openlocfilehash: 9213bdd8e63fc1e8ab7bdba8ac7a569be0dfe6be
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/06/2020
ms.locfileid: "93424851"
---
Выполните следующую команду, чтобы просмотреть [журналы потоковой передачи](../articles/azure-functions/functions-run-local.md#enable-streaming-logs) в режиме, близком к реальному времени:

```console
func azure functionapp logstream <APP_NAME> 
```

В отдельном окне терминала или в браузере снова вызовите удаленную функцию. В окне терминала отображается подробный журнал выполнения функции в Azure. 