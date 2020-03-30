---
title: включить файл
description: включить файл
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/12/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 8f2e2081ee31e2ce7b21677bd261ed7eda705334
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79485916"
---
[!INCLUDE [virtual-machines-disks-incremental-snapshots-description](virtual-machines-disks-incremental-snapshots-description.md)]

## <a name="regional-availability"></a>Доступность по регионам
[!INCLUDE [virtual-machines-disks-incremental-snapshots-regions](virtual-machines-disks-incremental-snapshots-regions.md)]

### <a name="restrictions"></a>Ограничения

[!INCLUDE [virtual-machines-disks-incremental-snapshots-restrictions](virtual-machines-disks-incremental-snapshots-restrictions.md)]

## <a name="portal"></a>Портал

Для создания и настройки дополнительных снимков на глобальном портале Azure [https://aka.ms/incrementalsnapshots](https://aka.ms/incrementalsnapshots)необходимо использовать следующую ссылку: Создание инкрементных моментальных снимков пока не доступно на глобальном портале Azure.

1. Войдите на [портал Azure](https://aka.ms/incrementalsnapshots) с предоставленной ссылкой и перейдите на диск, который вы хотите сделать снимок.
1. На диске выберите **Создать снимок**

    :::image type="content" source="media/virtual-machines-disks-incremental-snapshots-portal/create-snapshot-button-incremental.png" alt-text="Скриншот. Лезвие диска с выделенным моментальным снимком , так как это то, что вы должны выбрать.":::

1. Выберите группу ресурсов, которая вы хотите использовать, и введите имя.
1. Выберите **инкрементный** и выберите **Обзор и Создайте**

    :::image type="content" source="media/virtual-machines-disks-incremental-snapshots-portal/incremental-snapshot-create-snapshot-blade.png" alt-text="Скриншот. Создайте лезвие моментального снимка, заполните имя и выберите дополнительные, а затем создайте снимок.":::

1. Выберите **Создать**

    :::image type="content" source="media/virtual-machines-disks-incremental-snapshots-portal/create-incremental-snapshot-validation.png" alt-text="Скриншот. Страница проверки для вашего снимка, подтвердите свой выбор, а затем создайте снимок.":::

## <a name="next-steps"></a>Дальнейшие действия

Если вы хотите увидеть пример кода, демонстрирующий дифференциальную возможность дополнительных снимков, с помощью .NET, см. [Copy Azure Managed Disks в другой регион с дифференциальной возможностью дополнительных снимков.](https://github.com/Azure-Samples/managed-disks-dotnet-backup-with-incremental-snapshots)
