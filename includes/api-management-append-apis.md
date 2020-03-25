---
author: vladvino
ms.service: api-management
ms.topic: include
ms.date: 11/09/2018
ms.author: vlvinogr
ms.openlocfilehash: 2bfa356deeede1c16bd5a464ea7081132a67faf6
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/24/2020
ms.locfileid: "67185803"
---
## <a name="append-other-apis"></a>Добавление других интерфейсов API

API может включать другие API, предоставляемые разными службами, включая спецификацию OpenAPI, SOAP API, функцию приложений API Службы приложений Azure, приложение Функций Azure, Azure Logic Apps и Azure Service Fabric.

![Импорт API](./media/api-management-append-apis/import.png)

Чтобы добавить другой API к существующему, выполните следующие действия. После импорта другого API операции добавляются к текущему API.

1. Перейдите к экземпляру управления API Azure на портале Azure.
2. Выберите **API** в меню слева.
3. Щелкните **...** рядом с API, к которому нужно добавить другой API.
4. В раскрывающемся меню выберите **Импорт**.
5. Выберите службу, из которой следует импортировать API.