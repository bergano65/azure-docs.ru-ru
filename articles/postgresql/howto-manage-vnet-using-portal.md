---
title: Использование правил виртуальной сети — портал Azure — "база данных Azure для PostgreSQL" — "один сервер"
description: Создание конечных точек службы виртуальной сети и правил для базы данных Azure для PostgreSQL-Single Server и управление ими с помощью портал Azure
author: niklarin
ms.author: nlarin
ms.service: postgresql
ms.topic: how-to
ms.date: 5/6/2019
ms.openlocfilehash: 377f8eba179253ca6187b10a22970d0eadcda2f6
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/23/2020
ms.locfileid: "92489835"
---
# <a name="create-and-manage-vnet-service-endpoints-and-vnet-rules-in-azure-database-for-postgresql---single-server-by-using-the-azure-portal"></a>Создание конечных точек службы виртуальной сети и правил виртуальной сети в базе данных Azure для PostgreSQL-Single Server и управление ими с помощью портал Azure
Правила и конечные точки служб виртуальной сети расширяют частное адресное пространство виртуальной сети на сервер базы данных Azure для PostgreSQL. Общие сведения о конечных точках службы виртуальной сети базы данных Azure для PostgreSQL, включая ограничения, см. в [этой статье](concepts-data-access-and-security-vnet.md). Конечные точки службы виртуальной сети доступны во всех поддерживаемых регионах базы данных Azure для PostgreSQL.

> [!NOTE]
> Поддержка конечных точек службы виртуальной сети предназначена только для серверов общего назначения и серверов, оптимизированных для операций в памяти.
> В случае пиринга между виртуальными сетями, если трафик проходит через общий шлюз виртуальной сети с конечными точками и должен попадать в кэширующий узел, создайте правило ACL или виртуальной сети, чтобы разрешить виртуальным машинам Azure в шлюзе виртуальной сети доступ к Базе данных Azure для сервера PostgreSQL.


## <a name="create-a-vnet-rule-and-enable-service-endpoints-in-the-azure-portal"></a>Создание правила виртуальной сети и включение конечных точек службы на портале Azure

1. На странице сервера PostgreSQL в разделе "Параметры" щелкните **Connection security** (Безопасность подключения), чтобы открыть соответствующую страницу базы данных Azure для PostgreSQL. 

2. Убедитесь, что параметр Разрешить доступ к управлению службами Azure установлен в **Off**.

> [!Important]
> Если оставить этот элемент управления ВКЛЮЧЕНным, сервер базы данных Azure PostgreSQL будет принимать данные из любой подсети. что сделает доступ слишком незащищенным. Функция конечной точки службы виртуальная сеть Microsoft Azure, согласованная с функцией правила виртуальной сети в базе данных Azure для PostgreSQL, позволяет сократить контактную зону безопасности.

3. Затем щелкните **+ Добавляется существующая виртуальная сеть**. Если у вас нет виртуальной сети, щелкните **+ Создать виртуальную сеть** для ее создания. Дополнительные сведения см. в статье [Краткое руководство. Создание виртуальной сети с помощью портала Azure](../virtual-network/quick-create-portal.md).

   :::image type="content" source="./media/howto-manage-vnet-using-portal/1-connection-security.png" alt-text="портал Azure безопасность подключения":::

4. Введите имя правила виртуальной сети, выберите подписку, виртуальную сеть и имя подсети, а затем нажмите кнопку **Включить**. Это автоматически включает конечные точки службы виртуальной сети в подсети с использованием тега службы **Microsoft.SQL**.

   :::image type="content" source="./media/howto-manage-vnet-using-portal/2-configure-vnet.png" alt-text="портал Azure безопасность подключения" в подсети. 
   > 

5. Затем нажмите кнопку **ОК**, и вы увидите, что конечные точки службы виртуальной сети включены вместе с правилом виртуальной сети.

   :::image type="content" source="./media/howto-manage-vnet-using-portal/3-vnet-service-endpoints-enabled-vnet-rule-created.png" alt-text="портал Azure безопасность подключения":::

## <a name="next-steps"></a>Дальнейшие действия
- Аналогичным образом можно создать сценарий для [включения конечных точек службы виртуальной сети и создания правила виртуальной сети для базы данных Azure для PostgreSQL с помощью Azure CLI](howto-manage-vnet-using-cli.md).
- Дополнительные сведения о подключении к серверу базы данных Azure для PostgreSQL см. в статье [библиотеки подключений для базы данных Azure для PostgreSQL](./concepts-connection-libraries.md) .

<!-- Link references, to text, Within this same GitHub repo. --> 
[resource-manager-portal]: ../azure-resource-manager/management/resource-providers-and-types.md