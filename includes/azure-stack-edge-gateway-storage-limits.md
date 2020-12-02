---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 10/13/2020
ms.author: alkohli
ms.openlocfilehash: 1c6a3af7263cb4f1363425aa137fff04a6ad5120
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/01/2020
ms.locfileid: "96467627"
---
В этом разделе описываются ограничения для службы хранилища Azure и необходимые соглашения об именовании для файлов Azure, блочных BLOB-объектов Azure и страничных больших двоичных объектов Azure, которые применимы к службе Azure Stack ребра. Тщательно изучите ограничения хранилища и следуйте всем рекомендациям.

Актуальные сведения об ограничениях службы хранилища Azure и рекомендациях по именованию общих папок, контейнеров и файлов см. в следующих статьях:

- [Именование контейнеров и ссылка на них](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata)
- [Именование общих папок и ссылка на них](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata)
- [Блочные BLOB-объекты и соглашения о страничных объектах](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs)

> [!IMPORTANT]
> Если имеются файлы или каталоги, превышающие ограничения службы хранилища Azure, или не соответствуют соглашениям об именовании файлов и BLOB-объектов Azure, эти файлы или каталоги не будут приняты в службу хранилища Azure через службы Azure Stack ребра.