---
title: Скачайте шаблон для виртуальной машины Azure
description: Скачайте шаблон для виртуальной машины с помощью портала или PowerShell.
author: cynthn
manager: gwallace
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 11/17/2017
ms.author: cynthn
ms.openlocfilehash: af6905f0ba62a9053e44134348721312ade6b9d7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "82085388"
---
# <a name="download-the-template-for-a-vm"></a>Скачивание шаблона для виртуальной машины
При создании виртуальной машины в Azure с помощью портала или PowerShell автоматически создается шаблон Resource Manager. Этот шаблон можно использовать для быстрого дублирования развертывания. Шаблон содержит сведения обо всех ресурсах в группе ресурсов. Для виртуальной машины это означает, что шаблон содержит все, что создается для поддержки виртуальных машин в этой группе ресурсов, включая сетевые ресурсы.

## <a name="download-the-template-using-the-portal"></a>Скачивание шаблона с помощью портала
1. Войдите на [портал Azure](https://portal.azure.com/).
2. В меню слева выберите **Виртуальные машины**.
3. Затем выберите виртуальную машину из списка.
4. Выберите **Экспортировать шаблон**.
5. Выберите **Скачать** в меню сверху и сохраните ZIP-файл на локальном компьютере.
6. Откройте ZIP-файл и извлеките файлы в папку. ZIP-файл содержит:
   
   * parameters.json;
   * template.json.

Файл template.json является шаблоном.

## <a name="download-the-template-using-powershell"></a>Скачивание шаблона с помощью PowerShell
JSON-файл шаблона можно также скачать с помощью командлета [Export-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/export-azresourcegroup). Имя файла и путь к JSON-файлу можно указать с помощью параметра `-path`. В этом примере показано, как скачать шаблон для группы ресурсов с именем **myResourceGroup** в папку **C:\users\public\downloads** на локальном компьютере.

```powershell
    Export-AzResourceGroup -ResourceGroupName "myResourceGroup" -Path "C:\users\public\downloads"
```

## <a name="next-steps"></a>Дальнейшие шаги
Дополнительные сведения о развертывании ресурсов с помощью шаблонов см. в статье [Resource Manager template walkthrough](../../azure-resource-manager/resource-manager-template-walkthrough.md) (Пошаговое руководство по шаблону Resource Manager).

