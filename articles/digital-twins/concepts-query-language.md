---
title: Язык запросов
titleSuffix: Azure Digital Twins
description: Основные сведения о языке запросов Azure Digital двойников.
author: baanders
ms.author: baanders
ms.date: 3/26/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 0faa0af3bb793cbd75139ab42edd0aa7e20de78a
ms.sourcegitcommit: 8def3249f2c216d7b9d96b154eb096640221b6b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/03/2020
ms.locfileid: "87543849"
---
# <a name="about-the-query-language-for-azure-digital-twins"></a>О языке запросов для Azure Digital двойников

Вспомним, что Центр цифрового двойников Azure — это [**двойника граф**](concepts-twins-graph.md), созданный на основе **цифровых двойников** и **отношений**. Этот граф можно запросить для получения сведений о цифровых двойников и отношениях, которые она содержит. Эти запросы написаны на пользовательском языке запросов, похожем на SQL, который называется **языком запросов Azure Digital двойников**.

Чтобы отправить запрос в службу из клиентского приложения, вы будете использовать [**API запросов**](https://docs.microsoft.com/dotnet/api/azure.digitaltwins.core.digitaltwinsclient.query?view=azure-dotnet-preview)цифровых двойников Azure. Это позволяет разработчикам создавать запросы и применять фильтры для поиска наборов цифровых двойников в графе двойника, а также другую информацию о сценарии цифрового двойников Azure.

[!INCLUDE [digital-twins-query-operations.md](../../includes/digital-twins-query-operations.md)]

## <a name="next-steps"></a>Следующие шаги

Узнайте, как создавать запросы и просматривать примеры кода клиента в разделе "инструкции. Создание [*запросов к графу двойника"*](how-to-query-graph.md).
