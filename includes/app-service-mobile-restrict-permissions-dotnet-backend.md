---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.author: crdun
ms.openlocfilehash: b609a708a987194398c53bdf83f0d6e1f281808d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "67185780"
---
По умолчанию API-интерфейсы в серверной части мобильных приложений могут вызываться анонимно. Далее необходимо ограничить доступ всем клиентам, не прошедшим проверку подлинности.  

* **Серверная часть Node.js (через портал Azure):**  

    В настройках мобильных приложений нажмите **Easy Tables** и выберите таблицу. Щелкните **Изменить разрешения**, выберите для всех разрешений параметр **Authenticated access only** (Доступ только с проверкой подлинности) и нажмите кнопку **Сохранить**.
* **Серверная часть .NET (C#):**  

    В проекте сервера перейдите к **контроллерам** > **TodoItemController.cs**. Примените атрибут `[Authorize]` к классу **TodoItemController** следующим образом. Чтобы предоставить доступ только определенным методам, этот атрибут можно также применить именно к ним, а не к классу. Повторная публикация серверного проекта

        [Authorize]
        public class TodoItemController : TableController<TodoItem>

* **Серверная служба Node.js (через код Node.js)** :  

    Чтобы доступ к таблице предоставлялся только после проверки подлинности, добавьте в серверный скрипт Node.js следующую строку:

        table.access = 'authenticated';

    Дополнительные сведения см. в разделе [Практическое руководство. Обязательная аутентификация для доступа к таблицам](../articles/app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#howto-tables-auth). Узнайте, как загрузить проект быстрого запуска кода с веб-узла, в разделе [Загрузка серверной части на основе Node.js в виде готового кода для быстрого запуска с помощью Git](../articles/app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#download-quickstart).
