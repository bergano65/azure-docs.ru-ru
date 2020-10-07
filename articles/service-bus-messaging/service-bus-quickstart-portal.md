---
title: Создание очереди Служебной шины с помощью портала Azure
description: Из этого краткого руководства вы узнаете, как создать пространство имен и очередь в пространстве имен Служебной шины с помощью портала Azure.
author: spelluru
ms.topic: quickstart
ms.date: 08/12/2020
ms.author: spelluru
ms.openlocfilehash: 0e5a8698da03ee4d9c7d8b0d4d59eda1be3b1883
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/05/2020
ms.locfileid: "88185508"
---
# <a name="use-azure-portal-to-create-a-service-bus-namespace-and-a-queue"></a>Создайте пространство имен и очередь Служебной шины на портале Azure.
Из этого краткого руководства вы узнаете, как создать пространство имен и очередь Служебной шины с помощью [портала Azure][Azure portal]. В нем также описывается, как получить учетные данные авторизации, которые могут использоваться клиентским приложением для отправки сообщений в очередь и их получения из нее. 

[!INCLUDE [howto-service-bus-queues](../../includes/howto-service-bus-queues.md)]

## <a name="prerequisites"></a>Обязательные условия

Для работы с этим руководством требуется подписка Azure. Если у вас еще нет подписки Azure, вы можете создать [бесплатная учетная запись][], прежде чем начинать работу.

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

[!INCLUDE [service-bus-create-queue-portal](../../includes/service-bus-create-queue-portal.md)]

## <a name="next-steps"></a>Дальнейшие действия
С помощью инструкций из этой статьи вы создали пространство имен и очередь Служебной шины. Чтобы узнать, как отправлять сообщения в очередь и получать их из нее, см. одно из следующих кратких руководств в разделе **Отправка и получение сообщений**. 

- [.NET](service-bus-dotnet-get-started-with-queues.md)
- [Java](service-bus-java-how-to-use-queues.md)
- [JavaScript](service-bus-nodejs-how-to-use-queues-new-package.md)
- [Python](service-bus-python-how-to-use-queues.md)
- [PHP](service-bus-php-how-to-use-queues.md)
- [Ruby](service-bus-ruby-how-to-use-queues.md)

[бесплатная учетная запись]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
[Azure portal]: https://portal.azure.com/

[service-bus-flow]: ./media/service-bus-quickstart-portal/service-bus-flow.png
