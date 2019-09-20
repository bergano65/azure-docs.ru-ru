---
title: Как сбросить локальный пароль Linux на виртуальных машинах Azure | Документация Майкрософт
description: Описывается, как сбросить локальный пароль Linux на виртуальной машине Azure.
services: virtual-machines-linux
documentationcenter: ''
author: Deland-Han
manager: dcscontentpm
editor: ''
tags: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: troubleshooting
ms.date: 08/20/2019
ms.author: delhan
ms.openlocfilehash: 83751538efe4f3d3af5928caa04b265b6c867442
ms.sourcegitcommit: 116bc6a75e501b7bba85e750b336f2af4ad29f5a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/20/2019
ms.locfileid: "71153573"
---
# <a name="how-to-reset-local-linux-password-on-azure-vms"></a>Как сбросить локальный пароль Linux на виртуальных машинах Azure

В этой статье описано несколько способов сброса локальных паролей на виртуальных машинах Linux. Если истек срок действия учетной записи пользователя или требуется создать новую учетную запись, можно использовать приведенные ниже способы, чтобы создать новую учетную запись локального администратора и повторно получить доступ к виртуальной машине.

## <a name="symptoms"></a>Проблемы

Невозможно подключиться к виртуальной машине, и появляется сообщение о том, что указан неправильный пароль. Кроме того, с помощью VMAgent невозможно сбросить пароль на портале Azure.

## <a name="manual-password-reset-procedure"></a>Процедура сброса пароля вручную

> [!NOTE]
> Следующие шаги не применяются к виртуальной машине с неуправляемым диском.

1. Создайте моментальный снимок для диска операционной системы затронутой виртуальной машины, в нем создается диск из моментального снимка, после чего диск подключается к виртуальной машине для устранения неполадок. Дополнительные сведения см. [в разделе Устранение неполадок виртуальной машины Windows путем подключения диска ОС к виртуальной машине восстановления с помощью портал Azure](troubleshoot-recovery-disks-portal-linux.md).

2. Подключитесь к виртуальной машине для устранения неполадок с помощью удаленный рабочий стол.

3.  Выполните следующую команду SSH на виртуальной машине для устранения неполадок, чтобы стать суперпользователем.

    ```bash
    sudo su
    ```

4.  Выполните **fdisk -l** или просмотрите системные журналы, чтобы найти только что подключенный диск. Найдите имя подключаемого диска. Затем на временной виртуальной машине просмотрите соответствующий файл журнала.

    ```bash
    grep SCSI /var/log/kern.log (ubuntu)
    grep SCSI /var/log/messages (centos, suse, oracle)
    ```

    Ниже приведен пример выходных данных команды grep.

    ```bash
    kernel: [ 9707.100572] sd 3:0:0:0: [sdc] Attached SCSI disk
    ```

5.  Создайте точку подключения **tempmount**.

    ```bash
    mkdir /tempmount
    ```

6.  Подключите диск ОС к точке подключения. Обычно требуется подключить *sdc1* или *sdc2*. Это зависит от раздела размещения в каталоге */etc* диска неисправного компьютера.

    ```bash
    mount /dev/sdc1 /tempmount
    ```

7.  Перед внесением любых изменений создайте копии основных файлов учетных данных:

    ```bash
    cp /etc/passwd /etc/passwd_orig    
    cp /etc/shadow /etc/shadow_orig    
    cp /tempmount/etc/passwd /etc/passwd
    cp /tempmount/etc/shadow /etc/shadow 
    cp /tempmount/etc/passwd /tempmount/etc/passwd_orig
    cp /tempmount/etc/shadow /tempmount/etc/shadow_orig
    ```

8.  Сбросьте соответствующий пароль пользователя.

    ```bash
    passwd <<USER>> 
    ```

9.  Переместите измененные файлы на правильное расположение на диске неисправного компьютера.

    ```bash
    cp /etc/passwd /tempmount/etc/passwd
    cp /etc/shadow /tempmount/etc/shadow
    cp /etc/passwd_orig /etc/passwd
    cp /etc/shadow_orig /etc/shadow
    ```

10. Вернитесь к корневому каталогу и отключите диск.

    ```bash
    cd /
    umount /tempmount
    ```

11. В портал Azure отключите диск от виртуальной машины для устранения неполадок.

12. [Измените диск операционной системы для затронутой виртуальной машины](troubleshoot-recovery-disks-portal-linux.md#swap-the-os-disk-for-the-vm).

## <a name="next-steps"></a>Следующие шаги

* [Troubleshoot Azure VM by attaching OS disk to another Azure VM](https://social.technet.microsoft.com/wiki/contents/articles/18710.troubleshoot-azure-vm-by-attaching-os-disk-to-another-azure-vm.aspx) (Устранение неполадок виртуальной машины Azure путем присоединения диска ОС к другой виртуальной машине Azure)

* [Azure CLI: Удаление и повторное развертывание виртуальной машины из виртуального жесткого диска](https://blogs.msdn.microsoft.com/linuxonazure/2016/07/21/azure-cli-how-to-delete-and-re-deploy-a-vm-from-vhd/)
