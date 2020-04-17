---
title: включить файл
description: включить файл
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 12/27/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: cd7b889560acbe484581f065b641375c222f7ca8
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81536504"
---
По умолчанию стандартные файловые акции могут охватывать только до 5 TiB, хотя лимит акций может быть увеличен до 100 TiB. Для этого на уровне учетной записи хранилища должна быть включена *большая* функция общего обмена файлами. Премиум-аккаунты хранения (учетные записи хранения*файлов)* не имеют большого флага функции файла, так как все премиум-файлы уже включены для обеспечения полной 100 емкости TiB.

Вы можете включить большие файлы только на локально избыточных или зонных избыточных стандартных учетных записей хранения. После включения большого флага функции обмена файлами нельзя изменить уровень избыточности на геоизбыточное или геозонное хранилище.

Для включения больших долей файлов в существующую учетную запись хранилища перейдите к представлению **конфигурации** в таблице содержимого учетной записи хранилища и переключите большой переключатель рокера доли файла на включенный:

![Скриншот включения большого переключателя рокера обмена файлами на портале Azure](media/storage-files-tiers-enable-large-shares/enable-lfs-0.png)

Вы также можете включить 100 акций [`Set-AzStorageAccount`](https://docs.microsoft.com/powershell/module/az.storage/set-azstorageaccount) TiB файла [`az storage account update`](https://docs.microsoft.com/cli/azure/storage/account#az-storage-account-update) через смдлет PowerShell и команду Azure CLI. Для получения подробных инструкций по включению больших файлов, [см.](../articles/storage/files/storage-files-how-to-create-large-file-share.md)

Чтобы узнать больше о том, как создавать [creating an Azure file share](../articles/storage/files/storage-how-to-create-file-share.md)файлы на новых учетных записях хранения, см.
