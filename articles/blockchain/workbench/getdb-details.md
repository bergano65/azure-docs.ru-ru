---
title: Получение подробных сведений о базе данных Azure Blockchain Workbench
description: Узнайте, как получить сведения о базе данных Azure Blockchain Workbench и сервере базы данных.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 01/14/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: mmercuri
manager: femila
ms.openlocfilehash: 0d806491c58c4b1881adc2fd830de7c7b9f0859d
ms.sourcegitcommit: dede0c5cbb2bd975349b6286c48456cfd270d6e9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/16/2019
ms.locfileid: "54331934"
---
# <a name="get-information-about-your-azure-blockchain-workbench-database"></a>Получение сведений о базе данных Azure Blockchain Workbench

В этой статье показано, как получить подробные сведения о базе данных Azure Blockchain Workbench.

## <a name="overview"></a>Обзор

Сведения о приложениях, рабочих процессах и выполнении смарт-контракта предоставляются с помощью представлений базы данных в базе данных SQL Workbench Blockchain. Разработчики могут использовать эти сведения, когда применяют такие средства, как Microsoft Excel, Power BI, Visual Studio и SQL Server Management Studio.

Прежде чем разработчик может подключиться к базе данных, ему необходимо следующее.

* Доступ к внешнему клиенту, разрешенный в брандмауэре базы данных. В этой статье о настройке брандмауэра базы данных объясняется, как разрешить доступ.
* Имя сервера базы данных и имя базы данных.

## <a name="connect-to-the-blockchain-workbench-database"></a>Подключение к базе данных Blockchain Workbench

Для подключения к базе данных:

1. Войдите на портал Azure с учетной записью с разрешениями **владельца** для ресурсов Azure Blockchain Workbench.
2. В левой области навигации выберите **Группа ресурсов**.
3. Выберите имя группы ресурсов для развертывания Blockchain Workbench.
4. Выберите **Тип** для сортировки списка ресурсов, а затем выберите **SQL Server**. В отсортированном списке на следующем снимке экрана показаны две базы данных SQL, master и использующая lhgn в качестве **префикса ресурса**.

   ![Отсортированный список ресурсов Blockchain Workbench](./media/getdb-details/sorted-workbench-resource-list.png)

5. Чтобы просмотреть подробные сведения о базе данных Blockchain Workbench, перейдите по ссылке базы данных с **префиксом ресурса**, заданным для развертывания Blockchain Workbench.

   ![Сведения о базе данных](./media/getdb-details/workbench-db-details.png)

Имя сервера базы данных и имя базы данных позволяют подключиться к базе данных Blockchain Workbench с помощью средства разработки или отчетности.

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Database views in Azure Blockchain Workbench](database-views.md) (Представления базы данных в Azure Blockchain Workbench)