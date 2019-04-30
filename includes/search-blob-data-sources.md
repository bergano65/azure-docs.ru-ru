---
author: mgottein
ms.service: search
ms.topic: include
ms.date: 03/01/2019
ms.author: magottei
ms.openlocfilehash: 4a9ed5dcb32d0e53f933b7da9163a5c8c4b4aa43
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60871344"
---
* PDF;
* Форматы Microsoft Office: DOCX/DOC, XLSX/XLS, PPTX/PPT, MSG (сообщения электронной почты Outlook)  
* HTML
* XML
* ZIP;
* EML
* RTF
* обычные текстовые файлы (см. также [индексирование обычного текста](../articles/search/search-howto-indexing-azure-blob-storage.md#IndexingPlainText));
* JSON (см. [индексирование BLOB-объектов JSON](../articles/search/search-howto-index-json-blobs.md));
* CSV (сведения о предварительной версии функции см. в статье [Индексирование больших двоичных объектов CSV с помощью индексатора больших двоичных объектов службы поиска Azure](../articles/search/search-howto-index-csv-blobs.md)).

> [!IMPORTANT]
> Массивы CSV и JSON сейчас поддерживаются только в предварительной версии. Эти форматы доступны только при использовании версии **2017-11-11-Preview** REST API или версии 7.x-Preview пакета SDK для .NET. Помните, что предварительные версии API предназначены для тестирования и ознакомления. Они не должны использоваться в рабочей среде.
>