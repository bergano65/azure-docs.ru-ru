---
title: Модуль PowerShell для служб лаборатории Azure | Документация Майкрософт
description: В этой статье содержатся сведения о модуле PowerShell, который помогает управлять артефактами в службах лаборатории Azure.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/01/2019
ms.author: spelluru
ms.openlocfilehash: cc82355ee43f9fee4f9c2e1bb1bcc0481e4dcea3
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/05/2019
ms.locfileid: "73609402"
---
# <a name="azlabservices-powershell-module-preview"></a>AZ. Лабсервицес модуль PowerShell (Предварительная версия)
AZ. Лабсервицес — это модуль PowerShell, упрощающий управление службами лаборатории Azure. Она предоставляет функции с композицией для создания, запроса, обновления и удаления учетных записей лаборатории, лабораторий, виртуальных машин и образов. Дополнительные сведения об этом модуле см. на [домашней странице AZ. лабсервицес на сайте GitHub](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Modules/Library).

> [!NOTE]
> Этот модуль находится на **этапе предварительной версии**. 

## <a name="example-command"></a>Пример команды
Ниже приведен пример использования команды PowerShell для завершения работы всех работающих виртуальных машин во всех лабораториях.

```powershell
Get-AzLabAccount | Get-AzLab | Get-AzLabVm -Status Running | Stop-AzLabVm
```

## <a name="get-started"></a>Приступая к работе
1. Установите [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) , если он не существует на вашем компьютере. 
2. Скачайте файл [AZ. лабсервицес. PSM1](https://github.com/Azure/azure-devtestlab/blob/master/samples/ClassroomLabs/Modules/Library/Az.LabServices.psm1) на свой компьютер.
3. Импортируйте модуль:

    ```powershell
    Import-Module .\Az.LabServices.psm1
    ```
4. Выполните следующую команду, чтобы получить список всех лабораторий в подписке.

    ```powershell
    Get-AzLabAccount | Get-AzLab
    ```

## <a name="next-steps"></a>Дальнейшие действия
См. [домашнюю страницу AZ. лабсервицес на сайте GitHub](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Modules/Library).
