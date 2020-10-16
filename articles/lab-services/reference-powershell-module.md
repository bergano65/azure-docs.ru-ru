---
title: Модуль PowerShell для служб лаборатории Azure | Документация Майкрософт
description: В этой статье содержатся сведения о модуле PowerShell, который помогает управлять артефактами в службах лаборатории Azure.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 6f5809dcf981a1ecf30cda06af03d2b8a06d3694
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "87078816"
---
# <a name="azlabservices-powershell-module-preview"></a>Модуль PowerShell Az.LabServices (предварительная версия)
AZ. Лабсервицес — это модуль PowerShell, упрощающий управление службами лаборатории Azure. Она предоставляет функции с композицией для создания, запроса, обновления и удаления учетных записей лаборатории, лабораторий, виртуальных машин и образов. Дополнительные сведения об этом модуле см. на [домашней странице AZ. лабсервицес на сайте GitHub](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Modules/Library).

> [!NOTE]
> Этот модуль находится на **этапе предварительной версии**. 

## <a name="example-command"></a>Пример команды
Ниже приведен пример использования команды PowerShell для завершения работы всех работающих виртуальных машин во всех лабораториях.

```powershell
Get-AzLabAccount | Get-AzLab | Get-AzLabVm -Status Running | Stop-AzLabVm
```

## <a name="get-started"></a>Приступая к работе
1. Установите [Azure PowerShell](https://docs.microsoft.com/powershell/azure/) , если он не существует на вашем компьютере. 
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
