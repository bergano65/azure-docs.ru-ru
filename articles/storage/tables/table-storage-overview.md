---
title: Общие сведения о Хранилище таблиц. Хранилище объектов в Azure | Документация Майкрософт
description: Хранение структурированных данных в облаке в хранилище таблиц Azure (хранилище данных NoSQL).
services: storage
author: SnehaGunda
ms.service: storage
ms.devlang: dotnet
ms.topic: overview
ms.date: 04/23/2018
ms.author: sngun
ms.component: tables
ms.openlocfilehash: 18b8fab53e9e2de6d083b3a9e78001a3844b38d5
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51226349"
---
# <a name="introduction-to-table-storage-in-azure"></a>Общие сведения о Хранилище таблиц Azure

[!INCLUDE [storage-table-cosmos-db-tip-include](../../../includes/storage-table-cosmos-db-tip-include.md)]

Хранилище таблиц Azure — это служба, которая хранит структурированные данные NoSQL в облаке, предоставляя хранилище ключей и атрибутов с бессхемной конструкцией. Такая конструкция хранилища таблиц позволяет легко адаптировать данные по мере расширения приложения. Разным типам приложений может быть предоставлен быстрый и экономичный доступ к хранилищу таблиц. Такое хранилище обычно дешевле, чем традиционные хранилища SQL для похожих объемов данных.

Хранилище таблиц можно использовать для хранения гибких наборов данных, например пользовательских данных для веб-приложений, адресных книг, сведений об устройстве или метаданных любого другого типа, которые требуются вашей службе. В таблице можно хранить любое количество сущностей, а учетная запись хранения может содержать любое количество таблиц в пределах емкости учетной записи.

[!INCLUDE [storage-table-concepts-include](../../../includes/storage-table-concepts-include.md)]

## <a name="next-steps"></a>Дополнительная информация

* [Обозреватель хранилищ Microsoft Azure](../../vs-azure-tools-storage-manage-with-storage-explorer.md) — это бесплатное автономное приложение от корпорации Майкрософт, позволяющее визуализировать данные из службы хранилища Azure на платформе Windows, macOS и Linux.

* [Getting Started with Azure Table Storage in .NET](../../cosmos-db/table-storage-how-to-use-dotnet.md) (Приступая к работе с хранилищем таблиц Azure в .NET)

* Дополнительные сведения о доступных API-интерфейсах см. в справочной документации по службе таблиц:

    * [Справочник по клиентской библиотеке хранилища для .NET](https://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409)

    * [Справочник по REST API](https://msdn.microsoft.com/library/azure/dd179355)
