---
title: Скачивание виртуального жесткого диска Windows из Azure
description: Скачайте виртуальный жесткий диск Windows с помощью портала Azure.
author: cynthn
manager: gwallace
ms.service: virtual-machines-windows
ms.subservice: disks
ms.workload: infrastructure-services
ms.topic: article
ms.date: 01/13/2019
ms.author: cynthn
ms.openlocfilehash: e87f2436f69abed4c0d0ee415226f99b52f2e804
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2020
ms.locfileid: "82085371"
---
# <a name="download-a-windows-vhd-from-azure"></a>Скачивание виртуального жесткого диска Windows из Azure

В этой статье описано, как скачать файл виртуального жесткого диска (VHD) Windows из Azure, используя портал Azure.

## <a name="optional-generalize-the-vm"></a>Дополнительно: Обобщить VM

Если вы хотите использовать VHD в качестве [изображения](tutorial-custom-images.md) для создания других vMs, вы должны использовать [Sysprep](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--generalize--a-windows-installation) для обобщения операционной системы. 

Чтобы использовать VHD в качестве изображения для создания других VM, обобщить VM.

1. Если вы еще не сделали этого, вопийте на [порталaz.](https://portal.azure.com/)
2. [Подключайтесь к VM](connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 
3. На виртуальной машине откройте окно командной строки с правами администратора.
4. Измените каталог на *%windir%\system32\sysprep* и запустите файл sysprep.exe.
5. В диалоговом окне "Программа подготовки системы" выберите **Переход в окно приветствия системы (OOBE)** и убедитесь, что установлен флажок **Подготовка к использованию**.
6. В опциях выключения выберите **Shutdown,** а затем нажмите **OK**. 


## <a name="stop-the-vm"></a>Остановка виртуальной машины

VHD невозможно скачать из Azure, если он подключен к запущенной виртуальной машине. Для скачивания VHD необходимо остановить виртуальную машину. 

1. В главном меню на портале Azure и щелкните **Виртуальные машины**.
1. Выберите виртуальную машину из списка.
1. В колонке виртуальной машины нажмите кнопку **Остановить**.


## <a name="generate-download-url"></a>Создание URL-адреса загрузки

Чтобы загрузить файл VHD, необходимо создать [URL-адрес общей подписи доступа (SAS).](../../storage/common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) Когда этот URL-адрес создан, ему назначается срок действия.

1. На странице для VM щелкните **диски** в левом меню.
1. Выберите диск операционной системы для VM.
1. На странице диска выберите **Диск Экспорт** из левого меню.
1. Время истечения срока действия URL-адреса по умолчанию составляет *3600* секунд. Увеличьте этот срок до **36000** для дисков ОС Windows.
1. Нажмите кнопку **Создать URL-адрес**.

> [!NOTE]
> Заданное по умолчанию значение срока действия увеличивается, чтобы предоставить достаточно времени на скачивание большого VHD-файл для операционной системы Windows Server. Скачивание VHD-файл, содержащего операционную систему Windows Server, может занять несколько часов в зависимости от качества подключения. Если вы скачиваете VHD для диска данных, то время, заданное по умолчанию, является достаточным. 
> 
> 

## <a name="download-vhd"></a>Скачивание VHD

1. Под созданным URL-адресом щелкните ссылку "Скачать VHD-файл".
1. Возможно, вам придется нажать **Сохранить** в вашем браузере, чтобы начать загрузку. По умолчанию VHD-файлу присваивается имя *abcd*.

## <a name="next-steps"></a>Дальнейшие действия

- Узнайте, как [передать VHD-файл в Azure](upload-generalized-managed.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 
- [Создавайте управляемые диски из неуправляемых дисков в учетной записи хранилища.](attach-disk-ps.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
- [Управляйте дисками Azure с помощью PowerShell.](tutorial-manage-data-disk.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

