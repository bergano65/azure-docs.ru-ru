---
author: stevestein
ms.service: sql-database
ms.subservice: operations
ms.topic: include
ms.date: 08/09/2019
ms.author: sstein
ms.openlocfilehash: f7b385f359311217595352be31ba0687ca87bf40
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/21/2019
ms.locfileid: "69873722"
---
## <a name="create-and-manage-azure-sql-resources-with-the-azure-portal"></a>Создание ресурсов SQL Azure и управление ими с помощью портал Azure

Портал Azure предоставляет одну страницу, где можно управлять [всеми ресурсами Azure SQL](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.Sql%2Fazuresql) , включая виртуальные машины SQL.

Чтобы получить доступ к странице **ресурсов SQL Azure** , выберите **Azure SQL** в меню слева портал Azure. Если **SQL Azure** отсутствует в списке, выберите **все службы**, а затем введите *Azure SQL* в поле поиска.

> [!NOTE]
> **Azure SQL** предоставляет быстрый и простой способ доступа ко всем базам данных SQL, эластичным пулам, серверам баз данных, управляемым экземплярам SQL и виртуальным машинам SQL. SQL Azure не является службой или ресурсом. 

Чтобы управлять существующими ресурсами, выберите нужный элемент в списке. Чтобы создать новые ресурсы SQL Azure, выберите **+ Добавить**. 

![Страница портала SQL Azure](../media/sql-database-technical-overview/azure-sql.png)

После выбора **+ Добавить**просмотрите дополнительные сведения о различных параметрах, выбрав **Показать сведения** на любой плитке.

![сведения о плитке баз данных](../media/sql-database-technical-overview/create-single-database.png)

Дополнительные сведения см. в статье:

- [Создание отдельной базы данных](../sql-database-single-database-get-started.md)
- [Создание эластичного пула](../sql-database-elastic-pool.md#creating-a-new-sql-database-elastic-pool-using-the-azure-portal)
- [Создание управляемого экземпляра](../sql-database-managed-instance-get-started.md)
- [Создание виртуальной машины SQL](../../virtual-machines/windows/sql/quickstart-sql-vm-create-portal.md)