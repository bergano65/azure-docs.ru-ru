---
title: Включить имя файла
description: включить файл
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 12/27/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: cd7b889560acbe484581f065b641375c222f7ca8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81536504"
---
По умолчанию стандартные общие файловые ресурсы могут охватывать только до 5 Тиб, хотя размер общей папки можно увеличить до 100 тиб. Для этого необходимо включить функцию *большого общего файлового ресурса* на уровне учетной записи хранения. Учетные записи хранения класса Premium (учетные записи хранения*филестораже* ) не имеют флага общего файлового ресурса, так как все файловые ресурсы уровня "Премиум" уже включены для подготовки до полной емкости 100 тиб.

Вы можете включить большие файловые ресурсы только в локально избыточных или избыточных учетных записях хранения уровня Standard. После включения флага компонента большой общей папки нельзя изменить уровень избыточности на хранилище, избыточное по геоизбыточности или геозону.

Чтобы включить большие файловые ресурсы в существующей учетной записи хранения, перейдите к представлению **Конфигурация** в содержании учетной записи хранения и переключите переключатель большой файловый ресурс в значение включено:

![Снимок экрана с переключателем "включить большие файловые ресурсы" на портал Azure](media/storage-files-tiers-enable-large-shares/enable-lfs-0.png)

Вы также можете включить файловые ресурсы 100 Тиб с [`Set-AzStorageAccount`](https://docs.microsoft.com/powershell/module/az.storage/set-azstorageaccount) помощью командлета PowerShell [`az storage account update`](https://docs.microsoft.com/cli/azure/storage/account#az-storage-account-update) и команды Azure CLI. Подробные инструкции по включению общих папок с большими файлами см. в разделе [Включение и создание больших файловых ресурсов](../articles/storage/files/storage-files-how-to-create-large-file-share.md).

Дополнительные сведения о создании общих файловых ресурсов в новых учетных записях хранения см. в статье [Создание файлового ресурса Azure](../articles/storage/files/storage-how-to-create-file-share.md).
