---
title: Недостаточный размер папки TEMP по умолчанию для роли | Документация Майкрософт
description: Роль облачной службы располагает ограниченным объемом места в папке TEMP. Эта статья содержит советы о том, как предотвратить нехватку места.
services: cloud-services
documentationcenter: ''
author: simonxjx
manager: dcscontentpm
editor: ''
tags: top-support-issue
ms.assetid: 9f2af8dd-2012-4b36-9dd5-19bf6a67e47d
ms.service: cloud-services
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 06/15/2018
ms.author: v-six
ms.openlocfilehash: 77c4f627668b91c4bc21ad73b3511c1775943229
ms.sourcegitcommit: 4a7a4af09f881f38fcb4875d89881e4b808b369b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/04/2020
ms.locfileid: "89460207"
---
# <a name="default-temp-folder-size-is-too-small-on-a-cloud-service-webworker-role"></a>Недостаточный размер стандартной папки TEMP для рабочей роли или веб-роли облачной службы
Максимальный размер временного каталога по умолчанию рабочей роли или веб-роли облачной службы составляет 100 МБ, чего может оказаться недостаточно в определенный момент. В этой статье описано, как можно предотвратить нехватку места для временного каталога.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="why-do-i-run-out-of-space"></a>Почему мне не хватает свободного места?
В выполняемом в вашей программе коде используются стандартные переменные среды Windows: TEMP и TMP. Переменные TEMP и TMP указывают на один каталог с максимальным размером в 100 МБ. Данные, хранящиеся в этом каталоге, не хранятся на протяжении жизненного цикла облачной службы; если экземпляры роли в облачной службе перезапускаются, этот каталог очищается.

## <a name="suggestion-to-fix-the-problem"></a>Предложение по устранению проблемы
Примените один из следующих альтернативных способов.

* Настройте локальный ресурс хранилища и обращайтесь напрямую к нему, не используя TEMP или TMP. Чтобы получить доступ к локальному ресурсу хранилища из кода, запущенного в программе, вызовите метод [RoleEnvironment.GetLocalResource](/previous-versions/azure/reference/ee772845(v=azure.100)) .
* Настройте локальный ресурс хранилища и задайте каталоги TEMP и TMP, чтобы указать путь к этому локальному ресурсу хранилища. Это изменение следует выполнить внутри метода [RoleEntryPoint.OnStart](/previous-versions/azure/reference/ee772851(v=azure.100)) .

В следующем примере кода показано, как изменить целевые каталоги TEMP и TMP из метода OnStart:

```csharp
using System;
using Microsoft.WindowsAzure.ServiceRuntime;

namespace WorkerRole1
{
    public class WorkerRole : RoleEntryPoint
    {
        public override bool OnStart()
        {
            // The local resource declaration must have been added to the
            // service definition file for the role named WorkerRole1:
            //
            // <LocalResources>
            //    <LocalStorage name="CustomTempLocalStore"
            //                  cleanOnRoleRecycle="false"
            //                  sizeInMB="1024" />
            // </LocalResources>

            string customTempLocalResourcePath =
            RoleEnvironment.GetLocalResource("CustomTempLocalStore").RootPath;
            Environment.SetEnvironmentVariable("TMP", customTempLocalResourcePath);
            Environment.SetEnvironmentVariable("TEMP", customTempLocalResourcePath);

            // The rest of your startup code goes here…

            return base.OnStart();
        }
    }
}
```

## <a name="next-steps"></a>Дальнейшие действия
См. блог, в котором описывается, [как увеличить размер временной папки ASP.NET веб-ролей Azure](https://docs.microsoft.com/archive/blogs/kwill/how-to-increase-the-size-of-the-windows-azure-web-role-asp-net-temporary-folder).

Просмотрите дополнительные [статьи об устранении неполадок](https://docs.microsoft.com/visualstudio/azure/vs-azure-tools-debugging-cloud-services-overview) в облачных службах.

Чтобы узнать, как устранять неполадки ролей облачной службы с помощью диагностических данных компьютеров Azure PaaS, изучите [серию статей в блоге Кевина Уильямсона](https://docs.microsoft.com/archive/blogs/kwill/windows-azure-paas-compute-diagnostics-data).
