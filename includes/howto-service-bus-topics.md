---
author: spelluru
ms.service: service-bus-messaging
ms.topic: include
ms.date: 11/25/2018
ms.author: spelluru
ms.openlocfilehash: 7b05f3d8bcca5f26161f4c362078fa134518cafd
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/17/2019
ms.locfileid: "59689091"
---
## <a name="what-are-service-bus-topics-and-subscriptions"></a>Что такое разделы и подписки служебной шины?
Разделы и подписки служебной шины поддерживают модель обмена сообщениями " *публикация и подписка* ". При использовании разделов и подписок компоненты распределенного приложения не взаимодействуют между собой напрямую, а обмениваются сообщениями через раздел, который выступает в качестве посредника.

![TopicConcepts](./media/howto-service-bus-topics/sb-topics-01.png)

В отличие от очередей служебной шины, где каждое сообщение обрабатывается одним потребителем, разделы и подписки предоставляют вид связи "одного со многими" с помощью шаблона публикации или подписки. Можно зарегистрировать несколько подписок на раздел. Когда сообщение отправляется в раздел, оно затем может обрабатываться независимо каждой подпиской.

Раздел подписки напоминает виртуальную очередь, которая получает копии сообщений, отправленных в раздел. Вы можете зарегистрировать правила фильтрации для раздела на основе подписки. Эти правила позволят указать, какие сообщения и от каких подписок могут быть получены разделом.

Разделы и подписки служебной шины обеспечивают возможность масштабирования и обработки большого количества сообщений для многих пользователей и приложений.

[Azure portal]: https://portal.azure.com
[create-namespace]: ./media/howto-service-bus-topics/create-namespace.png
[connection-info]: ./media/howto-service-bus-topics/connection-info.png
[connection-string]: ./media/howto-service-bus-topics/connection-string.png


