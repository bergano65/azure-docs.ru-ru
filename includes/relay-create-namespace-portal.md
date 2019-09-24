---
author: clemensv
ms.service: service-bus-relay
ms.topic: include
ms.date: 11/09/2018
ms.author: clemensv
ms.openlocfilehash: 3e3617f68d58e51f24affaae7d69812195b72fd0
ms.sourcegitcommit: 7df70220062f1f09738f113f860fad7ab5736e88
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/24/2019
ms.locfileid: "71210326"
---
1. Войдите на [портале Azure][Azure portal].
1. Выберите **Создать ресурс**. Выберите **Интеграция** > **Ретрансляция**. Если пункт **Ретрансляция** в списке отсутствует, щелкните **Просмотреть все** в правом верхнем углу.
1. Выберите **создать**и введите имя пространства имен в поле **имя** . Портал Azure проверяет, доступно ли имя.
1. Выберите подписку Azure, в которой будет создано пространство имен.
1. В поле [Группа ресурсов](../articles/azure-resource-manager/manage-resource-groups-portal.md)выберите существующую группу ресурсов, в которой будет размещено пространство имен, или создайте новую.  
1. Выберите страну или регион, в котором должно размещаться пространство имен.

    ![Создать пространство имен][create-namespace]

1. Нажмите кнопку **Создать**. Портал Azure создает пространство имен и включает его. Через несколько минут система подготовит к работе ресурсы для вашей учетной записи.

### <a name="get-management-credentials"></a>Получение учетных данных управления

1. Выберите **все ресурсы**, а затем выберите только что созданное имя пространства имен.
1. Выберите **Политики общего доступа**.  
1. В окне **Политики общего доступа** выберите **RootManageSharedAccessKey**.
1. В **разделе Политика SAS: RootManageSharedAccessKey**, нажмите кнопку **копирования** рядом с **основной строкой подключения**. Это действие копирует строку подключения в буфер обмена для последующего использования. Вставьте на время эти значения в Блокноте или любом другом месте.
1. Повторите предыдущий шаг, скопировав и вставив значение **первичного ключа** во временное расположение для последующего использования.  

    ![Строка подключения][connection-string]

<!--Image references-->

[create-namespace]: ./media/relay-create-namespace-portal/create-namespace-vs2019.png
[connection-info]: ./media/relay-create-namespace-portal/connection-info.png
[connection-string]: ./media/relay-create-namespace-portal/connection-string-vs2019.png
[Azure portal]: https://portal.azure.com
