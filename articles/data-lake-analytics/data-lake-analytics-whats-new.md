---
title: Последние изменения в Data Lake Analytics
description: В этой статье приведен актуальный список последних изменений, внесенных в Data Lake Analytics.
services: data-lake-analytics
author: xujiang1
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: overview
ms.author: xujiang1
ms.date: 07/31/2020
ms.openlocfilehash: e78389ffc06f1b4cd4e39c15ac66215d514e9bc1
ms.sourcegitcommit: 5f7b75e32222fe20ac68a053d141a0adbd16b347
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/31/2020
ms.locfileid: "87476323"
---
# <a name="whats-new-in-data-lake-analytics"></a>Новые возможности Data Lake Analytics

Определенные компоненты Azure Data Lake Analytics непериодически обновляются. Чтобы вы оставались в курсе последних обновлений, в этой статье предоставлены такие сведения:

- Уведомление о бета-версии ключевого компонента.
- Важная информация о версии компонента, например список доступных версий компонента, текущая версия по умолчанию и т. д.


## <a name="notification-of-key-component-beta-preview"></a>Уведомление о бета-версии ключевого компонента

Для предварительной версии отсутствует бета-версия ключевого компонента. 

## <a name="u-sql-runtime"></a>Среда выполнения U-SQL

Среда выполнения U-SQL Azure Data Lake, включая компилятор, оптимизатор и диспетчер заданий, обрабатывается вашим кодом U-SQL.

При отправке задания Azure Data Lake Analytics из любого средства оно будет использовать в рабочей среде текущую доступную среду выполнения по умолчанию. 

Версия среды выполнения будет обновляться непериодически. Предыдущая среда выполнения будет храниться в течение некоторого времени. Если новая бета-версия готова для предварительной версии, она также будет доступна.

Ниже перечислены версии среды выполнения, которые доступны в настоящее время.

- release-20200124live_adl_16283022_2 --> **текущая версия по умолчанию**;
- release_20200124live_adl_16283022;
- release_20200124_adl_14480125;
- release_20190904_adl_10236248_1;
- release_20190904_adl_10236248;
- release_20190904_adl_9225818.

Чтобы понять, как устранять неполадки со средой выполнения U-SQL, ознакомьтесь с [этой статьей](runtime-troubleshoot.md).

## <a name="net-framework"></a>.NET Framework

Azure Data Lake Analytics теперь использует **.NET Framework v4.7.2**. 

Если в коде скрипта U-SQL Azure Data Lake Analytics используются пользовательские сборки, в которых используются библиотеки .NET, проверьте код, чтобы узнать, есть ли какие либо нарушения.

Чтобы понять, как устранять неполадки при обновлении .NET, ознакомьтесь с [этой статьей](runtime-troubleshoot.md).

## <a name="release-note"></a>Заметки о выпуске

Сведения о последних обновлениях см. в [заметках о выпуске Azure Data Lake Analytics](https://github.com/Azure/AzureDataLake/tree/master/docs/Release_Notes).


## <a name="next-steps"></a>Дальнейшие действия

* Начало работы с Data Lake Analytics с помощью [портала Azure](data-lake-analytics-get-started-portal.md) | [Azure PowerShell](data-lake-analytics-get-started-powershell.md) | [CLI](data-lake-analytics-get-started-cli.md)

