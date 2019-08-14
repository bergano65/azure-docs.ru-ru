---
title: Недостаточный размер папки TEMP по умолчанию для роли | Документация Майкрософт
description: Роль облачной службы располагает ограниченным объемом места в папке TEMP. Эта статья содержит советы о том, как предотвратить нехватку места.
services: cloud-services
documentationcenter: ''
author: simonxjx
manager: felixwu
editor: ''
tags: top-support-issue
ms.assetid: 9f2af8dd-2012-4b36-9dd5-19bf6a67e47d
ms.service: cloud-services
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 06/15/2018
ms.author: v-six
ms.openlocfilehash: 9b731eba4e1cd79a07c20271f1bd33c8c2c35d92
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/10/2019
ms.locfileid: "68945357"
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

## <a name="next-steps"></a>Следующие шаги
См. блог, в котором описывается, [как увеличить размер временной папки ASP.NET веб-ролей Azure](https://blogs.msdn.com/b/kwill/archive/2011/07/18/how-to-increase-the-size-of-the-windows-azure-web-role-asp-net-temporary-folder.aspx).

Просмотрите дополнительные [статьи об устранении неполадок](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/vs-azure-tools-debugging-cloud-services-overview.md) в облачных службах.

Чтобы узнать, как устранять неполадки ролей облачной службы с помощью диагностических данных компьютеров Azure PaaS, изучите [серию статей в блоге Кевина Уильямсона](https://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx).
