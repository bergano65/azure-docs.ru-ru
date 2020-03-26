---
title: Обзор Табличного хранилища Azure
description: Хранение структурированных данных в облаке в хранилище таблиц Azure (хранилище данных NoSQL).
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: dotnet
ms.topic: overview
ms.date: 05/20/2019
author: sakash279
ms.author: akshanka
ms.reviewer: sngun
ms.openlocfilehash: 20c799f89394dd8978f120097b054a9adf9f1280
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/26/2020
ms.locfileid: "76770738"
---
# <a name="azure-table-storage-overview"></a>Обзор табличного хранилища

[!INCLUDE [storage-table-cosmos-db-tip-include](../../includes/storage-table-cosmos-db-tip-include.md)]

Хранилище таблиц Azure — это служба, которая хранит структурированные данные NoSQL в облаке, предоставляя хранилище ключей и атрибутов с бессхемной конструкцией. Такая конструкция хранилища таблиц позволяет легко адаптировать данные по мере расширения приложения. Разным типам приложений может быть предоставлен быстрый и экономичный доступ к хранилищу таблиц. Такое хранилище обычно дешевле, чем традиционные хранилища SQL для похожих объемов данных.

Хранилище таблиц можно использовать для хранения гибких наборов данных, например пользовательских данных для веб-приложений, адресных книг, сведений об устройстве или метаданных любого другого типа, которые требуются вашей службе. В таблице можно хранить любое количество сущностей, а учетная запись хранения может содержать любое количество таблиц в пределах емкости учетной записи.

[!INCLUDE [storage-table-concepts-include](../../includes/storage-table-concepts-include.md)]

## <a name="next-steps"></a>Дальнейшие действия

* [Обозреватель хранилищ Microsoft Azure](../vs-azure-tools-storage-manage-with-storage-explorer.md) — это бесплатное автономное приложение от корпорации Майкрософт, позволяющее визуализировать данные из службы хранилища Azure на платформе Windows, macOS и Linux.

* [Начало работы с API таблиц Azure Cosmos DB и хранилищем таблиц Azure с помощью пакета SDK для .NET](table-storage-how-to-use-dotnet.md)

* Дополнительные сведения о доступных API-интерфейсах см. в справочной документации по службе таблиц:

    * [Справочник по клиентской библиотеке хранилища для .NET](https://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409)

    * [Справочник по REST API](https://msdn.microsoft.com/library/azure/dd179355)
