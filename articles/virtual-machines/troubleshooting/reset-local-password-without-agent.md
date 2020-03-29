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
ms.openlocfilehash: becbf88aeda164f7d916cbc1f1ace89262cc1a3f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77921629"
---
# <a name="reset-local-windows-password-for-azure-vm-offline"></a>Сброс локального пароля Windows для виртуальной машины Azure вне сети
Локальный пароль Windows для виртуальной машины Azure можно сбросить с помощью [портала Azure или Azure PowerShell](reset-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (если установлен гостевой агент Azure). Этот метод является основным способом сброса пароля для виртуальной машины Azure. Если в работе гостевого агента Azure возникают неполадки (агент не отвечает или не устанавливается после передачи пользовательского образа), то можно сбросить пароль Windows вручную. В этой статье описывается, как сбросить пароль локальной учетной записи, подключив исходный виртуальный диск операционной системы к другой виртуальной машине. Действия, описанные в этой статье, не применяются к контроллерам домена Windows. 

> [!WARNING]
> Этот метод следует использовать только в крайнем случае. Всегда старайтесь сначала сбросить пароль с помощью [портала Azure или Azure PowerShell.](reset-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="overview-of-the-process"></a>Описание процесса
Ниже приводятся основные шаги, выполнив которые можно сбросить локальный пароль для виртуальной машины Windows в Azure, если нет доступа к гостевому агенту Azure.

1. Остановите затронутую виртуальную машину.
1. Создайте снимок для диска ОС VM.
1. Создайте копию диска ОС из снимка.
1. Прикрепите и установите скопированный диск ОС на другой Windows VM, а затем создайте некоторые файлы конфигурации на диске. Файлы помогут вам сбросить пароль.
1. Открепите и отсоедините скопированный диск ОС от устранения неполадок VM.
1. Поменяйте диск ОС на пораженный VM.

## <a name="detailed-steps-for-the-vm-with-resource-manager-deployment"></a>Подробные шаги для VM с развертыванием менеджера ресурсов

> [!NOTE]
> Приведенные действия не применяются к контроллерам домена Windows. Они подходят только для отдельного сервера или сервера, который является членом домена.

Всегда рекомендуется сначала попытаться сбросить пароль с помощью [портала Azure или Azure PowerShell](reset-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json), прежде чем выполнять следующие шаги. Прежде чем начать, обязательно сохраните резервную копию виртуальной машины.

1. Сфотографать для диска ОС пострадавшего VM, создавать диск из снимка, а затем прикрепить диск к устранению неполадок VM. Для получения дополнительной информации [см. Troubleshoot Windows VM, прикрепив диск ОС к восстановлению VM с помощью портала Azure.](troubleshoot-recovery-disks-portal-windows.md)
2. Подключайтесь к устранению неполадок VM с помощью удаленного рабочего стола.
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
     
     ![Создание файла gpt.ini](./media/reset-local-password-without-agent/create-gpt-ini.png)

4. Создайте файл `scripts.ini` в расположении `\Windows\System32\GroupPolicy\Machines\Scripts\`. Убедитесь, что отображаются скрытые папки. При необходимости создайте папку `Machine` или `Scripts`.
   
   * В созданный файл `scripts.ini` добавьте следующие строки:
     
     ```
     [Startup]
     0CmdLine=C:\Windows\System32\FixAzureVM.cmd
     0Parameters=
     ```
     
     ![Создание файла scripts.ini](./media/reset-local-password-without-agent/create-scripts-ini.png)

5. В расположении `\Windows\System32` создайте файл `FixAzureVM.cmd` со следующим содержимым, заменив `<username>` и `<newpassword>` своими значениями:
   
    ```
    net user <username> <newpassword> /add
    net localgroup administrators <username> /add
    net localgroup "remote desktop users" <username> /add
    ```

    ![Создание файла FixAzureVM.cmd](./media/reset-local-password-without-agent/create-fixazure-cmd.png)
   
    Создаваемый пароль должен удовлетворять настроенным требованиям к сложности пароля для виртуальной машины.

6. На портале Azure отсоедините диск от устранения неполадок VM.

7. [Измените диск ОС для пострадавшего VM.](troubleshoot-recovery-disks-portal-windows.md#swap-the-os-disk-for-the-vm)

8. Когда новая виртуальная машина запущена, подключитесь к ней с помощью удаленного рабочего стола. Используйте при этом новый пароль, указанный в сценарии `FixAzureVM.cmd`.

9. В режиме удаленного сеанса на новой виртуальной машине удалите следующие файлы, чтобы очистить среду:
    
    * Из расположения %windir%\System32
      * удалите файл FixAzureVM.cmd.
    * От %windir%-System32-GroupPolicy-Машина
      * удалите файл scripts.ini.
    * Из расположения %windir%\System32\GroupPolicy
      * удалите файл gpt.ini (если файл gpt.ini уже существовал и был переименован в gpt.ini.bak, то переименуйте BAK-файл обратно в gpt.ini).

## <a name="detailed-steps-for-classic-vm"></a>Подробные шаги для Classic VM

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

> [!NOTE]
> Приведенные действия не применяются к контроллерам домена Windows. Они подходят только для отдельного сервера или сервера, который является членом домена.

Всегда рекомендуется сначала попытаться сбросить пароль с помощью [портала Azure или Azure PowerShell](https://docs.microsoft.com/previous-versions/azure/virtual-machines/windows/classic/reset-rdp), прежде чем выполнять следующие шаги. Прежде чем начать, обязательно сохраните резервную копию виртуальной машины. 

1. Удалите затронутую виртуальную машину на портале Azure. При удалении виртуальной машины удаляются только метаданные, на которые ссылается виртуальная машина в Azure. Когда виртуальная машина удаляется, виртуальные диски остаются.
   
   * Выберите VM на портале Azure, а затем нажмите *Удалить:*
     
     ![Удаление существующей виртуальной машины](./media/reset-local-password-without-agent/delete-vm-classic.png)

2. Подключите диск операционной системы исходной виртуальной машины к машине для устранения неполадок. Виртуальная машина для устранения неполадок должна находиться в том же регионе, что и диск операционной системы исходной виртуальной машины (например `West US`).
   
   1. Выберите виртуальную машину для устранения неполадок на портале Azure. Нажмите Диски*Прикрепите* *существующие:* | 
     
      ![Подключение существующего диска](./media/reset-local-password-without-agent/disks-attach-existing-classic.png)
     
   2. Выберите *VHD-файл*, а затем выберите учетную запись хранения, которая содержит вашу исходную виртуальную машину:
     
      ![Выбор учетной записи хранения](./media/reset-local-password-without-agent/disks-select-storage-account-classic.png)
     
   3. Проверьте поле с пометкой *Показать классические учетные записи хранения,* а затем выберите исходный контейнер. Как правило, исходный контейнер — *vhds*:
     
      ![Выбор контейнера хранилища](./media/reset-local-password-without-agent/disks-select-container-classic.png)

      ![Выбор контейнера хранилища](./media/reset-local-password-without-agent/disks-select-container-vhds-classic.png)
     
   4. Выберите виртуальный жесткий диск операционной системы для подключения. Для завершения процесса нажмите кнопку *Выбрать*:
     
      ![Выбор исходного виртуального диска](./media/reset-local-password-without-agent/disks-select-source-vhd-classic.png)

   5. Нажмите Ok, чтобы прикрепить диск

      ![Подключение существующего диска](./media/reset-local-password-without-agent/disks-attach-okay-classic.png)

3. Подключитесь к виртуальной машине для устранения неполадок, используя удаленный рабочий стол, и убедитесь, что диск операционной системы исходной виртуальной машины отображается.

   1. Выберите виртуальную машину для устранения неполадок на портале Azure и нажмите кнопку *Подключить*.

   2. Откройте RDP-файл, который скачается. Введите имя пользователя и пароль виртуальной машины для устранения неполадок.

   3. В проводнике найдите подключенный диск данных. Если виртуальный жесткий диск исходной виртуальной машины является единственным диском данных, подключенным к машине для устранения неполадок, то это должен быть диск F:
     
      ![Просмотр подключенного диска данных](./media/reset-local-password-without-agent/troubleshooting-vm-file-explorer-classic.png)

4. `gpt.ini` Создайте `\Windows\System32\GroupPolicy` на диске источника VM `gpt.ini` (если существует, переименовать `gpt.ini.bak`в):
   
   > [!WARNING]
   > Убедитесь, что вы случайно не `C:\Windows`создать следующие файлы в , Диск ОС для устранения неполадок VM. Создайте файлы на диске операционной системы исходной виртуальной машины, который подключен в качестве диска данных.
   
   * В созданный файл `gpt.ini` добавьте следующие строки:
     
     ```
     [General]
     gPCFunctionalityVersion=2
     gPCMachineExtensionNames=[{42B5FAAE-6536-11D2-AE5A-0000F87571E3}{40B6664F-4972-11D1-A7CA-0000F87571E3}]
     Version=1
     ```
     
     ![Создание файла gpt.ini](./media/reset-local-password-without-agent/create-gpt-ini-classic.png)

5. Создайте файл `scripts.ini` в расположении `\Windows\System32\GroupPolicy\Machines\Scripts\`. Убедитесь, что отображаются скрытые папки. При необходимости создайте папку `Machine` или `Scripts`.
   
   * В созданный файл `scripts.ini` добавьте следующие строки:

     ```
     [Startup]
     0CmdLine=C:\Windows\System32\FixAzureVM.cmd
     0Parameters=
     ```
     
     ![Создание файла scripts.ini](./media/reset-local-password-without-agent/create-scripts-ini-classic.png)

6. В расположении `\Windows\System32` создайте файл `FixAzureVM.cmd` со следующим содержимым, заменив `<username>` и `<newpassword>` своими значениями:
   
    ```
    net user <username> <newpassword> /add
    net localgroup administrators <username> /add
    net localgroup "remote desktop users" <username> /add
    ```

    ![Создание файла FixAzureVM.cmd](./media/reset-local-password-without-agent/create-fixazure-cmd-classic.png)
   
    Создаваемый пароль должен удовлетворять настроенным требованиям к сложности пароля для виртуальной машины.

7. На портале Azure отключите диск от машины для устранения неполадок.
   
   1. На портале Azure выберите виртуальную машину для устранения неполадок и щелкните *Диски*.
   
   2. Выберите диск данных, прикрепленный в шаге 2, нажмите **Detach,** затем нажмите **OK**.

     ![Отключение диска](./media/reset-local-password-without-agent/data-disks-classic.png)
     
     ![Отключение диска](./media/reset-local-password-without-agent/detach-disk-classic.png)

8. Создайте виртуальную машину на основе диска операционной системы исходной виртуальной машины.
   
     ![Создание виртуальной машины на основе шаблона](./media/reset-local-password-without-agent/create-new-vm-from-template-classic.png)

     ![Создание виртуальной машины на основе шаблона](./media/reset-local-password-without-agent/choose-subscription-classic.png)

     ![Создание виртуальной машины на основе шаблона](./media/reset-local-password-without-agent/create-vm-classic.png)

## <a name="complete-the-create-virtual-machine-experience"></a>Завершите работу с виртуальной машиной

1. Когда новая виртуальная машина запущена, подключитесь к ней с помощью удаленного рабочего стола. Используйте при этом новый пароль, указанный в сценарии `FixAzureVM.cmd`.

2. В режиме удаленного сеанса на новой виртуальной машине удалите следующие файлы, чтобы очистить среду:
    
    * От `%windir%\System32`
      * Удалить`FixAzureVM.cmd`
    * От `%windir%\System32\GroupPolicy\Machine\Scripts`
      * Удалить`scripts.ini`
    * От `%windir%\System32\GroupPolicy`
      * удалить `gpt.ini` (если `gpt.ini` существовали раньше, `gpt.ini.bak`и вы `.bak` переименовали `gpt.ini`его, переименовать файл обратно в )

## <a name="next-steps"></a>Дальнейшие действия
Если все еще не удается подключиться с помощью удаленного рабочего стола, то см. статью [Устранение неполадок с подключением к удаленному рабочему столу на виртуальной машине Azure под управлением Windows](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). [Подробное руководство по устранению неполадок с подключением к удаленному рабочему столу на виртуальной машине Windows в Azure](detailed-troubleshoot-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) скорее посвящено методам устранения неполадок, чем конкретным действиям. Вы также можете [отправить запрос в службу поддержки Azure](https://azure.microsoft.com/support/options/) и получить практическую помощь.
