---
title: Модуль PowerShell для лабораторных служб Azure (ru) Документы Майкрософт
description: В этой статье содержится информация о модуле PowerShell, который помогает управлять артефактами в службах лаборатории Azure.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73609402"
---
# <a name="azlabservices-powershell-module-preview"></a>Модуль PowerShell Az.LabServices (предварительная версия)
Az.LabServices — это модуль PowerShell, который упрощает управление службами Azure Lab. Он предоставляет композитные функции для создания, запроса, обновления и удаления лабораторных учетных записей, лабораторий, вс-м и изображений. Для получения дополнительной информации об этом модуле, смотрите [домашнюю страницу Az.LabServices на GitHub](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Modules/Library).

> [!NOTE]
> Этот модуль находится в **предварительном просмотре**. 

## <a name="example-command"></a>Пример команды
Вот пример использования команды PowerShell, чтобы остановить все запуск исправления во всех лабораториях.

```powershell
Get-AzLabAccount | Get-AzLab | Get-AzLabVm -Status Running | Stop-AzLabVm
```

## <a name="get-started"></a>Начало работы
1. Установите [Azure PowerShell,](https://docs.microsoft.com/powershell/azure/overview) если она не существует на вашей машине. 
2. Загрузите [Az.LabServices.psm1](https://github.com/Azure/azure-devtestlab/blob/master/samples/ClassroomLabs/Modules/Library/Az.LabServices.psm1) на вашу машину.
3. Импортируйте модуль:

    ```powershell
    Import-Module .\Az.LabServices.psm1
    ```
4. Выполнить следующую команду, чтобы перечислить все лаборатории в вашей подписке.

    ```powershell
    Get-AzLabAccount | Get-AzLab
    ```

## <a name="next-steps"></a>Дальнейшие действия
Смотрите [домашнюю страницу Az.LabServices на GitHub](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Modules/Library).
