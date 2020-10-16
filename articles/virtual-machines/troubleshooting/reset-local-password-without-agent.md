---
title: Сброс пароля локальной среды Windows без агента Azure | Документация Майкрософт
description: Описывается, как сбросить пароль локальной учетной записи пользователя Windows, если гостевой агент Azure не установлен или работает на виртуальной машине.
services: virtual-machines-windows
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.assetid: cf353dd3-89c9-47f6-a449-f874f0957013
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 04/25/2019
ms.author: genli
ms.openlocfilehash: 4cec8f77cacc5d3492dd6a5f8a8baa060f910763
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "91650602"
---
# <a name="reset-local-windows-password-for-azure-vm-offline"></a>Сброс локального пароля Windows для виртуальной машины Azure вне сети
Локальный пароль Windows для виртуальной машины Azure можно сбросить с помощью [портала Azure или Azure PowerShell](reset-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (если установлен гостевой агент Azure). Этот метод является основным способом сброса пароля для виртуальной машины Azure. Если в работе гостевого агента Azure возникают неполадки (агент не отвечает или не устанавливается после передачи пользовательского образа), то можно сбросить пароль Windows вручную. В этой статье описывается, как сбросить пароль локальной учетной записи, подключив исходный виртуальный диск операционной системы к другой виртуальной машине. Действия, описанные в этой статье, не применяются к контроллерам домена Windows. 

> [!WARNING]
> Этот метод следует использовать только в крайнем случае. Всегда рекомендуется сначала попытаться сбросить пароль с помощью [портала Azure или Azure PowerShell](reset-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="overview-of-the-process"></a>Описание процесса
Ниже приводятся основные шаги, выполнив которые можно сбросить локальный пароль для виртуальной машины Windows в Azure, если нет доступа к гостевому агенту Azure.

1. Остановите затронутую виртуальную машину.
1. Создайте моментальный снимок диска ОС виртуальной машины.
1. Создайте копию диска ОС на основе моментального снимка.
1. Подключите и присоедините скопированный диск ОС к другой виртуальной машине Windows, а затем создайте на нем файлы конфигурации. С их помощью вы сможете сбросить пароль.
1. Отключите и отсоедините скопированный диск ОС от виртуальной машины, на которой выполняется устранение неполадок.
1. Измените диск ОС для затронутой виртуальной машины.

## <a name="detailed-steps-for-the-vm-with-resource-manager-deployment"></a>Подробные действия для виртуальной машины с развертыванием с помощью Resource Manager

> [!NOTE]
> Приведенные действия не применяются к контроллерам домена Windows. Они подходят только для отдельного сервера или сервера, который является членом домена.

Всегда рекомендуется сначала попытаться сбросить пароль с помощью [портала Azure или Azure PowerShell](reset-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json), прежде чем выполнять следующие шаги. Прежде чем начать, обязательно сохраните резервную копию виртуальной машины.

1. Создайте моментальный снимок диска ОС затронутой виртуальной машины, создайте диск из моментального снимка, затем подключите диск к виртуальной машине, на которой выполняется устранение неполадок. Дополнительные сведения см. в статье [Устранение неполадок с виртуальной машиной Windows при подключении диска операционной системы к виртуальной машине восстановления с помощью портала Azure](troubleshoot-recovery-disks-portal-windows.md).
2. Подключитесь к виртуальной машине, на которой выполняется устранение неполадок, с помощью удаленного рабочего стола.
3. На диске исходной виртуальной машины создайте `gpt.ini` в `\Windows\System32\GroupPolicy` (если файл gpt.ini уже существует, то переименуйте его в gpt.ini.bak).
   
   > [!WARNING]
   > Убедитесь, что эти файлы случайно не созданы в расположении C:\Windows, на диске операционной системы виртуальной машины для устранения неполадок. Создайте файлы на диске операционной системы исходной виртуальной машины, который подключен в качестве диска данных.
   
   * В созданный файл `gpt.ini` добавьте следующие строки:
     
     ```
     [General]
     gPCFunctionalityVersion=2
     gPCMachineExtensionNames=[{42B5FAAE-6536-11D2-AE5A-0000F87571E3}{40B6664F-4972-11D1-A7CA-0000F87571E3}]
     Version=1
     ```
     
     :::image type="content" source="./media/reset-local-password-without-agent/create-gpt-ini.png" alt-text="Снимок экрана, на котором показаны обновления, внесенные в файл gpt.ini.":::

4. Создайте файл `scripts.ini` в расположении `\Windows\System32\GroupPolicy\Machine\Scripts\`. Убедитесь, что отображаются скрытые папки. При необходимости создайте папку `Machine` или `Scripts`. 
   
   * В созданный файл `scripts.ini` добавьте следующие строки:
     
     ```
     [Startup]
     0CmdLine=FixAzureVM.cmd
     0Parameters=
     ```
     
     :::image type="content" source="./media/reset-local-password-without-agent/create-scripts-ini-1.png" alt-text="Снимок экрана, на котором показаны обновления, внесенные в файл gpt.ini." <username> /add
    ```

    :::image type="content" source="./media/reset-local-password-without-agent/create-fixazure-cmd-1.png" alt-text="Снимок экрана, на котором показаны обновления, внесенные в файл gpt.ini.":::
   
    Создаваемый пароль должен удовлетворять настроенным требованиям к сложности пароля для виртуальной машины.

6. На портале Azure отключите диск от виртуальной машины, на которой выполняется устранение неполадок.

7. [Изменение диска ОС для затронутой виртуальной машины](troubleshoot-recovery-disks-portal-windows.md#swap-the-os-disk-for-the-vm)

8. Когда новая виртуальная машина запущена, подключитесь к ней с помощью удаленного рабочего стола. Используйте при этом новый пароль, указанный в сценарии `FixAzureVM.cmd`.

9. В режиме удаленного сеанса на новой виртуальной машине удалите следующие файлы, чтобы очистить среду:
    
    * Из%windir%\System32\GroupPolicy\Machine\Scripts\Startup
      * удалите файл FixAzureVM.cmd.
    * Из расположения %windir%\System32\GroupPolicy\Machine\Scripts
      * удалите файл scripts.ini.
    * Из расположения %windir%\System32\GroupPolicy
      * удалите файл gpt.ini (если файл gpt.ini уже существовал и был переименован в gpt.ini.bak, то переименуйте BAK-файл обратно в gpt.ini).

## <a name="detailed-steps-for-classic-vm"></a>Подробные действия для классической виртуальной машины

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

> [!NOTE]
> Приведенные действия не применяются к контроллерам домена Windows. Они подходят только для отдельного сервера или сервера, который является членом домена.

Всегда рекомендуется сначала попытаться сбросить пароль с помощью [портала Azure или Azure PowerShell](/previous-versions/azure/virtual-machines/windows/classic/reset-rdp), прежде чем выполнять следующие шаги. Прежде чем начать, обязательно сохраните резервную копию виртуальной машины. 

1. Удалите затронутую виртуальную машину на портале Azure. При удалении виртуальной машины удаляются только метаданные, на которые ссылается виртуальная машина в Azure. Когда виртуальная машина удаляется, виртуальные диски остаются.
   
   * На портале Azure выберите виртуальную машину и нажмите кнопку *Удалить*:
     
     :::image type="content" source="./media/reset-local-password-without-agent/delete-vm-classic.png" alt-text="Снимок экрана, на котором показаны обновления, внесенные в файл gpt.ini.":::

2. Подключите диск операционной системы исходной виртуальной машины к машине для устранения неполадок. Виртуальная машина для устранения неполадок должна находиться в том же регионе, что и диск операционной системы исходной виртуальной машины (например `West US`).
   
   1. Выберите виртуальную машину для устранения неполадок на портале Azure. Щелкните *Диски* | *Подключить существующий*:
     
      :::image type="content" source="./media/reset-local-password-without-agent/disks-attach-existing-classic.png" alt-text="Снимок экрана, на котором показаны обновления, внесенные в файл gpt.ini.":::
     
   2. Выберите *VHD-файл*, а затем выберите учетную запись хранения, которая содержит вашу исходную виртуальную машину:
     
      :::image type="content" source="./media/reset-local-password-without-agent/disks-select-storage-account-classic.png" alt-text="Снимок экрана, на котором показаны обновления, внесенные в файл gpt.ini.":::
     
   3. Установите флажок *Показать классические учетные записи хранения*, а затем выберите исходный контейнер. Как правило, исходный контейнер — *vhds*:
     
      :::image type="content" source="./media/reset-local-password-without-agent/disks-select-container-classic.png" alt-text="Снимок экрана, на котором показаны обновления, внесенные в файл gpt.ini.":::

      :::image type="content" source="./media/reset-local-password-without-agent/disks-select-container-vhds-classic.png" alt-text="Снимок экрана, на котором показаны обновления, внесенные в файл gpt.ini.":::
     
   4. Выберите виртуальный жесткий диск операционной системы для подключения. Для завершения процесса нажмите кнопку *Выбрать*:
     
      :::image type="content" source="./media/reset-local-password-without-agent/disks-select-source-vhd-classic.png" alt-text="Снимок экрана, на котором показаны обновления, внесенные в файл gpt.ini.":::

   5. Нажмите кнопку "ОК", чтобы подключить диск.

      :::image type="content" source="./media/reset-local-password-without-agent/disks-attach-okay-classic.png" alt-text="Снимок экрана, на котором показаны обновления, внесенные в файл gpt.ini.":::

3. Подключитесь к виртуальной машине для устранения неполадок, используя удаленный рабочий стол, и убедитесь, что диск операционной системы исходной виртуальной машины отображается.

   1. Выберите виртуальную машину для устранения неполадок на портале Azure и нажмите кнопку *Подключить*.

   2. Откройте RDP-файл, который скачается. Введите имя пользователя и пароль виртуальной машины для устранения неполадок.

   3. В проводнике найдите подключенный диск данных. Если виртуальный жесткий диск исходной виртуальной машины является единственным диском данных, подключенным к машине для устранения неполадок, то это должен быть диск F:
     
      :::image type="content" source="./media/reset-local-password-without-agent/troubleshooting-vm-file-explorer-classic.png" alt-text="Снимок экрана, на котором показаны обновления, внесенные в файл gpt.ini.":::

4. На диске исходной виртуальной машины создайте `gpt.ini` в `\Windows\System32\GroupPolicy` (если файл `gpt.ini` уже существует, то переименуйте его в `gpt.ini.bak`).
   
   > [!WARNING]
   > Убедитесь, что эти файлы случайно не созданы в расположении `C:\Windows` на диске ОС виртуальной машины, на которой выполняется устранение неполадок. Создайте файлы на диске операционной системы исходной виртуальной машины, который подключен в качестве диска данных.
   
   * В созданный файл `gpt.ini` добавьте следующие строки:
     
     ```
     [General]
     gPCFunctionalityVersion=2
     gPCMachineExtensionNames=[{42B5FAAE-6536-11D2-AE5A-0000F87571E3}{40B6664F-4972-11D1-A7CA-0000F87571E3}]
     Version=1
     ```
     
     :::image type="content" source="./media/reset-local-password-without-agent/create-gpt-ini-classic.png" alt-text="Снимок экрана, на котором показаны обновления, внесенные в файл gpt.ini.":::

5. Создайте файл `scripts.ini` в расположении `\Windows\System32\GroupPolicy\Machine\Scripts\`. Убедитесь, что отображаются скрытые папки. При необходимости создайте папку `Machine` или `Scripts`.
   
   * В созданный файл `scripts.ini` добавьте следующие строки:

     ```
     [Startup]
     0CmdLine=FixAzureVM.cmd
     0Parameters=
     ```
     
     :::image type="content" source="./media/reset-local-password-without-agent/create-scripts-ini-classic-1.png" alt-text="Снимок экрана, на котором показаны обновления, внесенные в файл gpt.ini." <username> /add
    ```

    :::image type="content" source="./media/reset-local-password-without-agent/create-fixazure-cmd-1.png" alt-text="Снимок экрана, на котором показаны обновления, внесенные в файл gpt.ini.":::
   
    Создаваемый пароль должен удовлетворять настроенным требованиям к сложности пароля для виртуальной машины.

7. На портале Azure отключите диск от машины для устранения неполадок.
   
   1. На портале Azure выберите виртуальную машину для устранения неполадок и щелкните *Диски*.
   
   2. Выберите диск данных, подключенный на шаге 2, нажмите кнопку **Отключить**, затем нажмите кнопку **ОК**.

     :::image type="content" source="./media/reset-local-password-without-agent/data-disks-classic.png" alt-text="Снимок экрана, на котором показаны обновления, внесенные в файл gpt.ini.":::
     
     :::image type="content" source="./media/reset-local-password-without-agent/detach-disk-classic.png" alt-text="Снимок экрана, на котором показаны обновления, внесенные в файл gpt.ini.":::

8. Создайте виртуальную машину на основе диска операционной системы исходной виртуальной машины.
   
     :::image type="content" source="./media/reset-local-password-without-agent/create-new-vm-from-template-classic.png" alt-text="Снимок экрана, на котором показаны обновления, внесенные в файл gpt.ini.":::

     :::image type="content" source="./media/reset-local-password-without-agent/choose-subscription-classic.png" alt-text="Снимок экрана, на котором показаны обновления, внесенные в файл gpt.ini.":::

     :::image type="content" source="./media/reset-local-password-without-agent/create-vm-classic.png" alt-text="Снимок экрана, на котором показаны обновления, внесенные в файл gpt.ini.":::

## <a name="complete-the-create-virtual-machine-experience"></a>Завершение процедуры создания виртуальной машины

1. Когда новая виртуальная машина запущена, подключитесь к ней с помощью удаленного рабочего стола. Используйте при этом новый пароль, указанный в сценарии `FixAzureVM.cmd`.

2. В режиме удаленного сеанса на новой виртуальной машине удалите следующие файлы, чтобы очистить среду:
    
    * Из расположения `%windir%\System32\GroupPolicy\Machine\Scripts\Startup\`
      * удалите `FixAzureVM.cmd`
    * Из расположения `%windir%\System32\GroupPolicy\Machine\Scripts`
      * удалите `scripts.ini`
    * Из расположения `%windir%\System32\GroupPolicy`
      * удалите `gpt.ini` (если файл `gpt.ini` существовал ранее и вы переименовали его в `gpt.ini.bak`, переименуйте файл `.bak` обратно в `gpt.ini`).

## <a name="next-steps"></a>Дальнейшие действия
Если все еще не удается подключиться с помощью удаленного рабочего стола, то см. статью [Устранение неполадок с подключением к удаленному рабочему столу на виртуальной машине Azure под управлением Windows](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). [Подробное руководство по устранению неполадок с подключением к удаленному рабочему столу на виртуальной машине Windows в Azure](detailed-troubleshoot-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) скорее посвящено методам устранения неполадок, чем конкретным действиям. Вы также можете [отправить запрос в службу поддержки Azure](https://azure.microsoft.com/support/options/) и получить практическую помощь.
