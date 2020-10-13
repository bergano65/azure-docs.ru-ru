---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 06/15/2020
ms.author: glenga
ms.openlocfilehash: 49818cf59da2d63cef4bb0bdca38d38a2feafca5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "86169920"
---
| | |
|--|--|
|**`<DESTINATION>`**| Место назначения, в которое отправляются журналы. Допустимые значения: `AppInsights` и `Blob`.<br/>При использовании `AppInsights` Убедитесь, что [в приложении-функции включен Application Insights](../articles/azure-functions/functions-monitoring.md#enable-application-insights-integration).<br/>Если для назначения задано значение `Blob` , то журналы создаются в контейнере больших двоичных объектов с именем `azure-functions-scale-controller` в учетной записи хранения по умолчанию, заданной в `AzureWebJobsStorage` параметре приложения. |
|**`<VERBOSITY>`** | Задает уровень ведения журнала. Поддерживаются значения `None`, `Warning` и `Verbose`.<br/>Если задано значение `Verbose` , контроллер масштабирования регистрирует причину каждого изменения в количестве рабочих ролей, а также сведения о триггерах, которые учитываются в этих решениях. Подробные журналы содержат предупреждения триггеров и хэши, используемые триггерами до и после запуска контроллера масштабирования. |

> [!CAUTION]
> Не оставляйте ведение журнала контроллера масштабирования включенным. Включите ведение журнала, пока не будет собрано достаточно данных, чтобы понять, как работает контроллер масштабирования, а затем отключите его.