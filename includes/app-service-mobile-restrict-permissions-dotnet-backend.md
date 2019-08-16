---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.author: crdun
ms.openlocfilehash: b609a708a987194398c53bdf83f0d6e1f281808d
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/15/2019
ms.locfileid: "67185780"
---
По умолчанию API-интерфейсы в серверной части мобильных приложений могут вызываться анонимно. Далее необходимо ограничить доступ всем клиентам, не прошедшим проверку подлинности.  

* **Серверная часть Node.js (через портал Azure):**  

    В разделе "Параметры" мобильных приложений щелкните **Простые таблицы** и выберите таблицу. Щелкните **Изменить разрешения**, выберите для всех разрешений параметр **Authenticated access only** (Доступ только с проверкой подлинности) и нажмите кнопку **Сохранить**.
* **Серверная часть .NET (C#):**  

    В серверном проекте выберите **Контроллеры** > **TodoItemController.cs**. Примените атрибут `[Authorize]` к классу **TodoItemController** следующим образом. Чтобы предоставить доступ только определенным методам, этот атрибут можно также применить именно к ним, а не к классу. Повторная публикация серверного проекта

        [Authorize]
        public class TodoItemController : TableController<TodoItem>

* **Серверная служба Node.js (через код Node.js)** :  

    Чтобы доступ к таблице предоставлялся только после проверки подлинности, добавьте в серверный скрипт Node.js следующую строку:

        table.access = 'authenticated';

    Дополнительные сведения см. в [разделе как Требовать проверку подлинности для](../articles/app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#howto-tables-auth)доступа к таблицам. Сведения о том, как скачать проект кода быстрого запуска с сайта, см [. в разделе как Скачайте проект кода краткого руководства по Node. js с](../articles/app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#download-quickstart)помощью Git.
