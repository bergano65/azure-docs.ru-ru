---
author: mgottein
ms.service: search
ms.topic: include
ms.date: 11/25/2018
ms.author: magottei
ms.openlocfilehash: 0f11ea02afb1f59afda215c7d95a660df6155b62
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/27/2018
ms.locfileid: "52440416"
---
* PDF;
* форматы Microsoft Office: DOCX/DOC, XLSX/XLS, PPTX/PPT, MSG (сообщения электронной почты Outlook);  
* HTML
* XML
* ZIP;
* EML
* RTF
* обычные текстовые файлы (см. также [индексирование обычного текста](../articles/search/search-howto-indexing-azure-blob-storage.md#IndexingPlainText));
* JSON (см. [индексирование BLOB-объектов JSON](../articles/search/search-howto-index-json-blobs.md));
* CSV (сведения о предварительной версии функции см. в статье [Индексирование больших двоичных объектов CSV с помощью индексатора больших двоичных объектов службы поиска Azure](../articles/search/search-howto-index-csv-blobs.md)).

> [!IMPORTANT]
> Массивы CSV и JSON сейчас поддерживаются только в предварительной версии. Эти форматы доступны только при использовании версии **2016-09-01-Preview** REST API или версии 2.x-preview пакета SDK для .NET. Помните, что предварительные версии API предназначены для тестирования и ознакомления. Они не должны использоваться в рабочей среде.
>