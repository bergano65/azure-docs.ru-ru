---
title: Ошибка диспетчера загрузки Windows-недопустимый хэш изображения состояния 0xC0000428
titlesuffix: Azure Virtual Machines
description: В этой статье приводятся инструкции по устранению проблем, когда использовалось предварительное изображение и истек срок действия пробного периода, что предотвращает загрузку виртуальной машины Azure (ВМ).
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 26c411f9-6c22-4f45-a445-c5781e547828
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 09/03/2020
ms.author: v-miegge
ms.openlocfilehash: 22db9c7966b6f988ca0ea799104275f3f86c77ea
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/21/2021
ms.locfileid: "98629578"
---
# <a name="windows-boot-manager-error---0xc0000428-status-invalid-image-hash"></a>Ошибка диспетчера загрузки Windows-недопустимый хэш изображения состояния 0xC0000428

В этой статье приводятся инструкции по устранению проблем, когда использовалось предварительное изображение и истек срок действия пробного периода, что предотвращает загрузку виртуальной машины Azure (ВМ).

## <a name="symptom"></a>Симптом

При использовании [диагностики загрузки](./boot-diagnostics.md) для просмотра снимка экрана виртуальной машины вы увидите, что на снимке экрана отображается диспетчер загрузки Windows с сообщением:

  `File: \windows\system32\boot\winload.exe`

  `Status: 0xc0000428`

  `Info: Windows cannot verify the digital signature for this file.`

  ![На рис. 1 отображается диспетчер загрузки Windows с состоянием «Ox0000428», а «info» Windows не удается проверить цифровую подпись для этого файла».](./media/windows-boot-error-invalid-image-hash/1-cannot-verify-signature.png)

или сообщение:

  `File: \Windows\system32\winload.exe`

  `Status: 0xc0000428`

  `Info: The digital signature for this file couldn't be verified.`
    
  ![На рис. 2 Windows Boot Manager отображается с состоянием «Ox0000428» и «не удалось проверить цифровую подпись для этого файла».](./media/windows-boot-error-invalid-image-hash/2-digital-signature-not-verified.png)

## <a name="cause"></a>Причина

Образ, использованный для создания виртуальной машины, был предварительным изображением с датой истечения срока действия, а не с образом RTM (выпуск в производство). 

Предварительные образы имеют назначенный жизненный цикл, и снимок экрана, который вы видите, отображается при прохождении даты окончания срока действия, то есть пробной версии образа.

### <a name="example-of-preview-images"></a>Пример изображений предварительной версии

`a699494373c04fc0bc8f2bb1389d6106__Windows-Server-Technical-Preview-201505.01-en.us-127GB.vhd`

Вы не можете перенести дату окончания срока действия предварительной версии образа на более поздний строк. После истечения срока действия предварительной версии виртуальная машина больше не сможет загружаться.

- В зависимости от продукта, пробный период может отличаться. Например, образы предварительной версии Windows имеют пробный период в 180 дней.

- В Azure все образы для Windows, которые являются предварительными версиями, будут содержать Примечание в их описании, которое не предназначено для рабочей среды и доступно для использования только в течение указанного пробного периода или в виде предварительной версии.

## <a name="solution"></a>Решение

> [!TIP]
> Если у вас есть недавняя резервная копия виртуальной машины, можно попытаться [восстановить виртуальную машину из резервной копии](../../backup/backup-azure-arm-restore-vms.md) , чтобы устранить проблему загрузки.

Если изображение является изображением предварительного просмотра, то невозможно продлить дату истечения срока действия используемого образа. необходимо [развернуть новую виртуальную машину](../windows/quick-create-portal.md) с помощью образа, не являющегося предварительным. Приведенные ниже инструкции помогут вам определить, использовался ли предварительный образ, а также предоставить ресурсы, которые помогут перенести данные из этой ВИРТУАЛЬНОЙ машины в новую виртуальную машину. Если вы действительно определили образ как изображение для предварительного просмотра, это изображение не будет восстановлено, так как срок его действия истек.

В зависимости от ваших предпочтений можно использовать либо Azure PowerShell, либо Azure CLI для запроса изображения, чтобы определить, является ли он изображением для предварительного просмотра. С помощью этих команд можно убедиться, что изображение является изображением предварительного просмотра.

### <a name="query-using-azure-powershell"></a>Запрос с помощью Azure PowerShell

1. Откройте приложение Windows PowerShell.
1. Выполните следующие команды:

   ```powershell
   $locName = "<LOCATION>" 
   $pubName = "<PUBLISHER NAME>" 
   $offerName = "<OFFER NAME>" 
   $skuName= "<YEAR WHEN THIS IMAGE WAS RELEASED>"
   Get-AzVMImagePublisher -Location $locName | Select $pubName
   Get-AzVMImageOffer -Location $locName -PublisherName $pubName | Select Offer
   Get-AzVMImageSku -Location $locName -PublisherName $pubName -Offer $offerName | Select Skus
   Get-AzVMImage -Location $locName -PublisherName $pubName -Offer $offerName -Skus $skuName | Select Version
   ```

- В предыдущих командах замените `<LOCATION>` ,, `<PUBLISHER NAME>` и на `<OFFER NAME>` `<YEAR WHEN THIS IMAGE WAS RELEASED>` указанные сведения. Также удалите символы "<" и ">".

  См. следующий пример.

  ```powershell
  $locName = "West US" 
  $pubName = "MicrosoftWindowsServer" 
  $offerName = "WindowsServer" 
  $skuName= "2016-Datacenter"
  Get-AzVMImagePublisher -Location $locName | Select $pubName
  Get-AzVMImageOffer -Location $locName -PublisherName $pubName | Select Offer
  Get-AzVMImageSku -Location $locName -PublisherName $pubName -Offer $offerName | Select Skus
  Get-AzVMImage -Location $locName -PublisherName $pubName -Offer $offerName -Skus $skuName | Select Version
  ```

### <a name="query-using-the-azure-cli"></a>Запрос с использованием Azure CLI

1. Если вы этого еще не сделали, вам потребуется [установить Azure CLI](/cli/azure/install-azure-cli).
1. После скачивания используйте командную строку или PowerShell, чтобы ввести `az login` команду, а затем выполните вход с использованием учетных данных вашей учетной записи.
1. После входа в систему введите следующие команды:

   ```powershell
   az vm image list-publishers --location "<LOCATION>"
   az vm image list-offers --location "West US" --publisher "<PUBLISHER NAME>"
   az vm image list-skus --location "West US" --publisher "<PUBLISHER NAME>" --offer "<OFFER NAME>"
   az vm image list  --location "West US" --publisher "<PUBLISHER NAME>" --offer "<OFFER NAME>" --sku "<YEAR WHEN THIS IMAGE WAS RELEASED>"
   ```

- В предыдущих командах замените `<LOCATION>` ,, `<PUBLISHER NAME>` и на `<OFFER NAME>` `<YEAR WHEN THIS IMAGE WAS RELEASED>` указанные сведения. Также удалите символы "<" и ">".

  См. следующий пример.

  ```powershell
  az vm image list-publishers --location "West US"
  az vm image list-offers --location "West US" --publisher "MicrosoftWindowsServer"
  az vm image list-skus --location "West US" --publisher "MicrosoftWindowsServer" --offer "WindowsServer"
  az vm image list  --location "West US" --publisher "MicrosoftWindowsServer" --offer "WindowsServer" --sku "2016-Datacenter"
  ```