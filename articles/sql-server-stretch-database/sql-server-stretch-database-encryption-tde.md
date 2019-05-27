---
title: Включение прозрачного шифрования данных для Stretch Database в Azure | Документация Майкрософт
description: Включение прозрачного шифрования данных (TDE) для SQL Server Stretch Database в Azure
services: sql-server-stretch-database
documentationcenter: ''
ms.assetid: a44ed8f5-b416-4c41-9b1e-b7271f10bdc3
ms.service: sql-server-stretch-database
ms.workload: data-management
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 06/14/2016
author: blazem-msft
ms.author: blazem
ms.reviewer: jroth
manager: jroth
ms.openlocfilehash: 61f556476958484b78b9c3dff2583eb6db043637
ms.sourcegitcommit: 13cba995d4538e099f7e670ddbe1d8b3a64a36fb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/22/2019
ms.locfileid: "66003040"
---
# <a name="enable-transparent-data-encryption-tde-for-stretch-database-on-azure"></a>Включение прозрачного шифрования данных (TDE) для Stretch Database в Azure
> [!div class="op_single_selector"]
> * [портал Azure](sql-server-stretch-database-encryption-tde.md)
> * [TSQL](sql-server-stretch-database-tde-tsql.md)
>
>

Прозрачное шифрование данных (TDE) помогает защититься от угрозы вредоносных атак за счет шифрования и расшифровки базы данных, связанных резервных копий и файлов журналов транзакций при хранении в реальном времени, не внося изменения в само приложение.

При использовании TDE хранилище всей базы данных шифруется с помощью симметричного ключа, который называется ключом шифрования базы данных. Ключ шифрования базы данных защищается встроенным сертификатом сервера. Каждый сервер Azure обладает уникальным встроенным сертификатом. Корпорация Майкрософт автоматически изменяет эти сертификаты не реже, чем раз в 90 дней. Общие сведения о прозрачном шифровании данных см. в [Прозрачное шифрование данных (TDE)].

## <a name="enabling-encryption"></a>Включение шифрования
Чтобы включить прозрачное шифрование для базы данных Azure, где хранятся данные, перенесенные из Базы данных SQL Server Stretch, выполните следующее.

1. Откройте базу данных на [Портал Azure](https://portal.azure.com)
2. В колонке базы данных нажмите кнопку **Параметры** .
3. Выберите параметр **Прозрачное шифрование данных** . ![][1]
4. Выберите параметр **Включить**, а затем — **Сохранить**
   ![][2].

## <a name="disabling-encryption"></a>Отключение шифрования
Чтобы отключить прозрачное шифрование для базы данных Azure, где хранятся данные, перенесенные из Базы данных SQL Server Stretch, выполните следующее.

1. Откройте базу данных на [Портал Azure](https://portal.azure.com)
2. В колонке базы данных нажмите кнопку **Параметры** .
3. Выберите параметр **Прозрачное шифрование данных** .
4. Выберите параметр **Отключить**, а затем — **Сохранить**.

<!--Anchors-->
[Прозрачное шифрование данных (TDE)]: https://msdn.microsoft.com/library/bb934049.aspx


<!--Image references-->
[1]: ./media/sql-server-stretch-database-encryption-tde/stretchtde1.png
[2]: ./media/sql-server-stretch-database-encryption-tde/stretchtde2.png


<!--Link references-->
