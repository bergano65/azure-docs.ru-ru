---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 01/30/2019
ms.author: alkohli
ms.openlocfilehash: 87c28b906b1f810857fee86ba8a94a0834e1de68
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/25/2020
ms.locfileid: "96026648"
---
В этом разделе описываются ограничения для службы хранилища Azure и необходимые соглашения об именовании для файлов Azure, блочных BLOB-объектов Azure и страничных больших двоичных объектов Azure, которые применимы к службе Azure Stack ребра и Шлюз Data Box. Тщательно изучите ограничения хранилища и следуйте всем рекомендациям.

Актуальные сведения об ограничениях службы хранилища Azure и рекомендациях по именованию общих папок, контейнеров и файлов см. в следующих статьях:

- [Именование контейнеров и ссылка на них](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata)
- [Именование общих папок и ссылка на них](/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata)
- [Блочные BLOB-объекты и соглашения о страничных объектах](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs)

> [!IMPORTANT]
> Если файлы или каталоги превышают ограничения службы хранилища Azure или не соответствуют соглашениям об именовании файлов и BLOB-объектов Azure, они не направляются в службу хранилища Azure через службу Azure Stack Edge или Шлюза Data Box.