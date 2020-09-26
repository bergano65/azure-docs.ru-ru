---
title: Изменение размера зашифрованных дисков управления логическими томами с помощью шифрования дисков Azure
description: В этой статье приводятся инструкции по изменению размера зашифрованных дисков ADE с помощью управления логическими томами.
author: jofrance
ms.service: security
ms.topic: article
ms.author: jofrance
ms.date: 09/21/2020
ms.openlocfilehash: ba652b9424b8d5ce1b6a2c5b7d70b8fd9e999323
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91346313"
---
# <a name="how-to-resize-logical-volume-management-devices-encrypted-with-azure-disk-encryption"></a>Изменение размера устройств управления логическими томами, зашифрованных с помощью шифрования дисков Azure

Эта статья представляет собой пошаговый процесс изменения размера данных, зашифрованных из ADE, с помощью управления логическим томом (LVM) в Linux, применимого к нескольким сценариям.

Этот процесс применяется к следующим средам:

- Дистрибутивы Linux
    - RHEL 7 или более поздней версии;
    - Ubuntu 16 +
    - SUSE 12 +
- Расширение для одного прохода шифрования дисков Azure
- Расширение двойного прохода шифрования дисков Azure

## <a name="considerations"></a>Рекомендации

В этом документе предполагается, что:

1. Существует существующая конфигурация LVM.
   
   Дополнительные сведения о настройке LVM на виртуальной машине Linux см. в статье [Настройка LVM на виртуальной машине Linux](configure-lvm.md) .

2. Диски уже зашифрованы с помощью шифрования дисков Azure. Проверьте [настройку LVM при шифровании](how-to-configure-lvm-raid-on-crypt.md) , чтобы получить сведения о настройке LVM-on-encryption.

3. У вас есть необходимые опыт Linux и LVM для выполнения этих примеров.

4. Вы понимаете, что рекомендации по использованию дисков данных в Azure, как упоминалось при [устранении неполадок с именами устройств](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/troubleshoot-device-names-problems), — это использование путей/dev/disk/SCSI1/.

## <a name="scenarios"></a>Сценарии

Процедуры, описанные в этой статье, относятся к следующим сценариям.

### <a name="for-traditional-lvm-and-lvm-on-crypt-configurations"></a>Для традиционных конфигураций LVM и LVM

- Расширение логического тома при наличии свободного места в VG

### <a name="for-traditional-lvm-encryption-the-logical-volumes-are-encrypted-not-the-whole-disk"></a>Для традиционного шифрования LVM (логические тома шифруются, а не весь диск)

- Расширение традиционного LVMного тома Добавление нового PV
- Расширение традиционных LVM томов изменение размера существующего

### <a name="for-lvm-on-crypt-recommended-method-the-entire-disk-is-encrypted-not-only-the-logical-volume"></a>Для LVM-on-encrypted (рекомендуемый метод, весь диск шифруется, а не только логический том)

- Расширение LVM на зашифрованном томе Добавление нового PV
- Расширение LVM на незашифрованном томе изменение размера существующего

> [!NOTE]
> Не рекомендуется смешивать традиционные LVM шифрования и LVM на одной виртуальной машине.

> [!NOTE]
> В этих примерах используются уже существующие имена дисков, физических томов, групп томов, логических томов, FileSystem, UUID и точки подключения, поэтому необходимо заменить значения, приведенные в этих примерах, в соответствии с вашей средой.

#### <a name="extending-a-logical-volume-when-theres-available-space-in-the-vg"></a>Расширение логического тома при наличии свободного места в VG

Традиционный метод, используемый для изменения размера логических томов, он может применяться к незашифрованным дискам, традиционным LVM зашифрованным томам и конфигурациям LVM-on-encrypted.

1. Проверьте текущий размер файловой системы, которую нужно увеличить:

    ``` bash
    df -h /mountpoint
    ```

    ![сценарий a — Check-FS1](./media/disk-encryption/resize-lvm/001-resize-lvm-scenarioa-check-fs.png)

2. Убедитесь, что в VG достаточно места для увеличения LV

    ``` bash
    vgs
    ```

    ![сценарий a-Check-VG](./media/disk-encryption/resize-lvm/002-resize-lvm-scenarioa-check-vgs.png)

    Можно также использовать "вгдисплай"

    ``` bash
    vgdisplay vgname
    ```

    ![сценарий a-Check-вгдисплай](./media/disk-encryption/resize-lvm/002-resize-lvm-scenarioa-check-vgdisplay.png)

3. Указание логического тома для изменения размера

    ``` bash
    lsblk
    ```

    ![сценарий a-Check-lsblk1](./media/disk-encryption/resize-lvm/002-resize-lvm-scenarioa-check-lsblk1.png)

    Для LVM-on-encrypted в этом выводе есть разница, которая показывает, что зашифрованный слой находится на зашифрованном слое, охватывающем весь диск.

    ![сценарий a-Check-lsblk2](./media/disk-encryption/resize-lvm/002-resize-lvm-scenarioa-check-lsblk2.png)

4. Проверка размера логического тома

    ``` bash
    lvdisplay lvname
    ```

    ![сценарий a-Check-lvdisplay01](./media/disk-encryption/resize-lvm/002-resize-lvm-scenarioa-check-lvdisplay01.png)

5. Увеличьте размер в режиме реального времени, используя "-r" для изменения размера файловой системы в сети

    ``` bash
    lvextend -r -L +2G /dev/vgname/lvname
    ```

    ![сценарий-изменение размера — Латвия](./media/disk-encryption/resize-lvm/003-resize-lvm-scenarioa-resize-lv.png)

6. Проверка новых размеров для LV и файловой системы

    ``` bash
    df -h /mountpoint
    ```

    ![сценарий a-Check-FS](./media/disk-encryption/resize-lvm/004-resize-lvm-scenarioa-check-fs.png)

    Новый размер отражается, что означает успешное изменение размера файла LV и файловой системы.

7. Вы можете снова проверить сведения о LV, чтобы подтвердить изменения на уровне LV.

    ``` bash
    lvdisplay lvname
    ```

    ![сценарий a-Check-lvdisplay2](./media/disk-encryption/resize-lvm/004-resize-lvm-scenarioa-check-lvdisplay2.png)

#### <a name="extending-a-traditional-lvm-volume-adding-a-new-pv"></a>Расширение традиционного LVMного тома Добавление нового PV

Применяется, если необходимо добавить новый диск для увеличения размера группы томов.

1. Проверьте текущий размер файловой системы, которую нужно увеличить:

    ``` bash
    df -h /mountpoint
    ```

    ![сценариоб-Check-FS](./media/disk-encryption/resize-lvm/005-resize-lvm-scenariob-check-fs.png)

2. Проверка конфигурации текущего физического тома

    ``` bash
    pvs
    ```

    ![сценариоб — Check-постоянного хранилища версий](./media/disk-encryption/resize-lvm/006-resize-lvm-scenariob-check-pvs.png)

3. Проверка текущих сведений о VG

    ``` bash
    vgs
    ```

    ![сценариоб — Check-VGS](./media/disk-encryption/resize-lvm/007-resize-lvm-scenariob-check-vgs.png)

4. Проверка текущего списка дисков

    Диски данных должны быть идентифицированы с помощью проверки устройств в разделе/dev/disk/Azure/SCSI1/

    ``` bash
    ls -l /dev/disk/azure/scsi1/
    ```

    ![сценариоб — Check-scs1](./media/disk-encryption/resize-lvm/008-resize-lvm-scenariob-check-scs1.png)

5. Проверка выходных данных лсблк 

    ``` bash
    lsbk
    ```

    ![сценариоб — Check-лсблк](./media/disk-encryption/resize-lvm/008-resize-lvm-scenariob-check-lsblk.png)

6. Подключение нового диска к виртуальной машине

    Выполните шаг 4 из приведенного ниже документа.

   - [Подключение диска к виртуальной машине](attach-disk-portal.md)

7. После присоединения диска проверьте список дисков, обратите внимание на новый диск.

    ``` bash
    ls -l /dev/disk/azure/scsi1/
    ```

    ![сценариоб — Check-scsi12](./media/disk-encryption/resize-lvm/009-resize-lvm-scenariob-check-scsi12.png)

    ``` bash
    lsbk
    ```

    ![сценариоб — Check-lsblk12](./media/disk-encryption/resize-lvm/009-resize-lvm-scenariob-check-lsblk1.png)

8. Создать новый параметр ПС поверх нового диска данных

    ``` bash
    pvcreate /dev/newdisk
    ```

    ![сценариоб — пвкреате](./media/disk-encryption/resize-lvm/010-resize-lvm-scenariob-pvcreate.png)

    Этот метод использует весь диск как ПС без секции. при необходимости можно использовать "FDISK" для создания секции, а затем использовать эту секцию для "пвкреате".

9. Убедитесь, что НЗ был добавлен в список PV.

    ``` bash
    pvs
    ```

    ![сценариоб — Check-pvs1](./media/disk-encryption/resize-lvm/011-resize-lvm-scenariob-check-pvs1.png)

10. Расширьте VG, добавив в него новую ПС.

    ``` bash
    vgextend vgname /dev/newdisk
    ```

    ![сценариоб-VG — расширение](./media/disk-encryption/resize-lvm/012-resize-lvm-scenariob-vgextend.png)

11. Проверка нового размера VG

    ``` bash
    vgs
    ```

    ![сценариоб — Check-VGS1](./media/disk-encryption/resize-lvm/013-resize-lvm-scenariob-check-vgs1.png)

12. Используйте лсблк, чтобы указать, какую из них нужно изменить.

    ``` bash
    lsblk
    ```

    ![сценариоб — Check-lsblk1](./media/disk-encryption/resize-lvm/013-resize-lvm-scenariob-check-lsblk1.png)

13. Расширение параметра LV с помощью "-r" для увеличения объема файловой системы

    ``` bash
    lvextend -r -L +2G /dev/vgname/lvname
    ```

    ![сценариоб — лвекстенд](./media/disk-encryption/resize-lvm/013-resize-lvm-scenariob-lvextend.png) 

14. Проверка новых размеров LV и файловой системы

    ``` bash
    df -h /mountpoint
    ```

    ![сценариоб-Check-FS1](./media/disk-encryption/resize-lvm/014-resize-lvm-scenariob-check-fs1.png)

    Важно помнить, что при использовании ADE в традиционных конфигурациях LVM зашифрованный слой создается на уровне LV, а не на уровне диска.

    На этом этапе зашифрованный слой расширяется до нового диска.
    Фактический диск данных не имеет параметров шифрования на уровне платформы, поэтому состояние шифрования не обновляется.

    >[!NOTE]
    >Ниже приведены некоторые причины, по которым LVM-on-шифрования является рекомендуемым подходом. 

15. Проверьте сведения о шифровании на портале:

    ![сценариоб — Check-portal1](./media/disk-encryption/resize-lvm/014-resize-lvm-scenariob-check-portal1.png)

    Чтобы обновить параметры шифрования на диске, необходимо добавить новый объект LV и включить расширение на виртуальной машине.
    
16. Добавьте новый объект LV, создайте на нем файловую систему и добавьте его в/etc/fstab.

17. Снова задайте расширение шифрования, чтобы отмечать параметры шифрования на новом диске данных на уровне платформы.

    Пример.

    CLI

    ``` bash
    az vm encryption enable -g ${RGNAME} --name ${VMNAME} --disk-encryption-keyvault "<your-unique-keyvault-name>"
    ```

18. Проверьте сведения о шифровании на портале:

    ![сценариоб — Check-portal2](./media/disk-encryption/resize-lvm/014-resize-lvm-scenariob-check-portal2.png)

19. После обновления параметров шифрования вы можете удалить новый объект LV, а также удалить запись из/etc/fstab и/etc/crypttab, которые были созданы для нее.

    ![сценариоб-Delete-fstab-крипттаб](./media/disk-encryption/resize-lvm/014-resize-lvm-scenariob-delete-fstab-crypttab.png)

20. Отключение логического тома

    ``` bash
    umount /mountpoint
    ```

21. Закрытие зашифрованного слоя тома

    ``` bash
    cryptsetup luksClose /dev/vgname/lvname
    ```

22. Удаление LV

    ``` bash
    lvremove /dev/vgname/lvname
    ```

#### <a name="extending-a-traditional-lvm-volume-resizing-an-existing-pv"></a>Расширение традиционных LVM томов изменение размера существующего

При определенных сценариях или ограничениях требуется изменить размер существующего диска.

1. Поиск зашифрованных дисков

    ``` bash
    ls -l /dev/disk/azure/scsi1/
    ```

    ![сценариок — Check-SCSI1](./media/disk-encryption/resize-lvm/015-resize-lvm-scenarioc-check-scsi1.png)

    ``` bash
    lsblk -fs
    ```

    ![сценариок — Check-лсблк](./media/disk-encryption/resize-lvm/015-resize-lvm-scenarioc-check-lsblk.png)

2. Проверка сведений о пс

    ``` bash
    pvs
    ```

    ![сценариок — Check-постоянного хранилища версий](./media/disk-encryption/resize-lvm/016-resize-lvm-scenarioc-check-pvs.png)

    В настоящее время используется все пространство на всех постоянного хранилища версий

3. Проверка сведений о VGs

    ``` bash
    vgs
    vgdisplay -v vgname
    ```

    ![сценариок — Check-VGS](./media/disk-encryption/resize-lvm/017-resize-lvm-scenarioc-check-vgs.png)

4. Проверьте размеры дисков. для вывода списка размеров дисков можно использовать FDISK или лсблк.

    ``` bash
    for disk in `ls -l /dev/disk/azure/scsi1/* | awk -F/ '{print $NF}'` ; do echo "fdisk -l /dev/${disk} | grep ^Disk "; done | bash

    lsblk -o "NAME,SIZE"
    ```

    ![сценариок-Check-fdisk](./media/disk-encryption/resize-lvm/018-resize-lvm-scenarioc-check-fdisk.png)

    Мы определили, какие постоянного хранилища версий связаны с тем, что LVs с помощью лсблк-FS, вы можете определить его также, выполнив "лвдисплай".

    ``` bash
    lvdisplay --maps VG/LV
    lvdisplay --maps datavg/datalv1
    ```

    ![Check-лвдисплай](./media/disk-encryption/resize-lvm/019-resize-lvm-scenarioc-check-lvdisplay.png)

    В этом конкретном случае все 4 диска данных являются частью одного и того же VG и одной LV, а конфигурация может отличаться от этого примера.

5. Проверьте текущее использование файловой системы:

    ``` bash
    df -h /datalvm*
    ```

    ![сценариок-Check-DF](./media/disk-encryption/resize-lvm/020-resize-lvm-scenarioc-check-df.png)

6. Измените размер дисков данных:

    Вы можете ссылаться на [диски Linux](expand-disks.md) (см. раздел Изменение размера диска), чтобы выполнить этот шаг с помощью портала, интерфейса командной строки или PowerShell.

    >[!NOTE]
    >Учтите, что операции изменения размера на виртуальных дисках не могут быть выполнены с виртуальной машиной, на которой выполняется. Вам потребуется отменить выделение виртуальной машины для этого шага

7. При изменении размера дисков до нужного значения запустите виртуальную машину и проверьте новые размеры с помощью программы Fdisk.

    ``` bash
    for disk in `ls -l /dev/disk/azure/scsi1/* | awk -F/ '{print $NF}'` ; do echo "fdisk -l /dev/${disk} | grep ^Disk "; done | bash

    lsblk -o "NAME,SIZE"
    ```

    ![сценариок — Check-fdisk1](./media/disk-encryption/resize-lvm/021-resize-lvm-scenarioc-check-fdisk1.png)

    В этом конкретном случае/Dev/SDD был изменен с 5G на 20G

8. Проверить текущий размер PV

    ``` bash
    pvdisplay /dev/resizeddisk
    ```

    ![сценариок — Check-пвдисплай](./media/disk-encryption/resize-lvm/022-resize-lvm-scenarioc-check-pvdisplay.png)
    
    Даже если размер диска был изменен, ПС по-прежнему имеет предыдущий размер.

9. Изменение размера PV

    ``` bash
    pvresize /dev/resizeddisk
    ```

    ![сценариок — Check-пвресизе](./media/disk-encryption/resize-lvm/023-resize-lvm-scenarioc-check-pvresize.png)


10. Проверьте размер PV

    ``` bash
    pvdisplay /dev/resizeddisk
    ```

    ![сценариок — Check-pvdisplay1](./media/disk-encryption/resize-lvm/024-resize-lvm-scenarioc-check-pvdisplay1.png)

    Примените ту же процедуру ко всем дискам, размер которых нужно изменить.

11. Проверка сведений о VG

    ``` bash
    vgdisplay vgname
    ```

    ![сценариок — Check-vgdisplay1](./media/disk-encryption/resize-lvm/025-resize-lvm-scenarioc-check-vgdisplay1.png)

    В VG теперь есть место, выделяемое для LVs

12. Изменение размера для LV

    ``` bash
    lvresize -r -L +5G vgname/lvname
    lvresize -r -l +100%FREE /dev/datavg/datalv01
    ```

    ![сценариок — Check-lvresize1](./media/disk-encryption/resize-lvm/031-resize-lvm-scenarioc-check-lvresize1.png)

13. Проверить размер FS

    ``` bash
    df -h /datalvm2
    ```

    ![сценариок — Check-Df3](./media/disk-encryption/resize-lvm/032-resize-lvm-scenarioc-check-df3.png)

#### <a name="extending-an-lvm-on-crypt-volume-adding-a-new-pv"></a>Расширение LVMного тома, который добавляет новый ПС

Этот метод точно соответствует инструкциям из раздела [Настройка LVM при шифровании](how-to-configure-lvm-raid-on-crypt.md) для добавления нового диска и его настройки в конфигурации LVM-on-.

Этот метод можно использовать для добавления пространства к уже существующему LV или, вместо этого, можно создать новый VGs или LVs.

1. Проверка текущего размера VG

    ``` bash
    vgdisplay vgname
    ```

    ![сценарий-Check-vg01](./media/disk-encryption/resize-lvm/033-resize-lvm-scenarioe-check-vg01.png)

2. Проверьте размер FS и lv, которые нужно увеличить

    ``` bash
    lvdisplay /dev/vgname/lvname
    ```

    ![сценарий-Check-lv01](./media/disk-encryption/resize-lvm/034-resize-lvm-scenarioe-check-lv01.png)

    ``` bash
    df -h mountpoint
    ```

    ![сценарий-Check-FS01](./media/disk-encryption/resize-lvm/034-resize-lvm-scenarioe-check-fs01.png)

3. Добавьте новый диск данных в виртуальную машину и найдите его.

    Проверка дисков перед добавлением диска

    ``` bash
    fdisk -l | egrep ^"Disk /"
    ```

    ![сценарий-Check-newdisk01](./media/disk-encryption/resize-lvm/035-resize-lvm-scenarioe-check-newdisk01.png)

    Проверьте диски перед добавлением нового диска

    ``` bash
    lsblk
    ```

    ![сценарий-Check-newdisk002](./media/disk-encryption/resize-lvm/035-resize-lvm-scenarioe-check-newdisk02.png)

    Добавьте новый диск с помощью PowerShell, Azure CLI или портал Azure. Узнайте, как [подключить диск](attach-disk-portal.md) для справки по добавлению дисков в виртуальную машину.

    В соответствии со схемой имен ядер для устройств новый диск обычно получает следующую доступную букву. в этом конкретном случае новый добавленный диск — SSD.

4. Проверка дисков после добавления нового диска

    ``` bash
    fdisk -l | egrep ^"Disk /"
    ```

    ![сценарий-Check-newdisk02](./media/disk-encryption/resize-lvm/036-resize-lvm-scenarioe-check-newdisk02.png)-

    ``` bash
    lsblk
    ```

    ![сценарий-Check-newdisk003](./media/disk-encryption/resize-lvm/036-resize-lvm-scenarioe-check-newdisk03.png)

5. Создание файловой системы поверх недавно добавленного диска

    Сопоставление недавно добавленного диска со связанными устройствами в/dev/disk/Azure/SCSI1/

    ``` bash
    ls -la /dev/disk/azure/scsi1/
    ```

    ![сценарий-Check-newdisk03](./media/disk-encryption/resize-lvm/037-resize-lvm-scenarioe-check-newdisk03.png)

    ``` bash
    mkfs.ext4 /dev/disk/azure/scsi1/${disk}
    ```

    ![сценарий — mkfs01](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-mkfs01.png)

6. Создать временную точку подключения для нового добавленного диска

    ``` bash
    newmount=/data4
    mkdir ${newmount}
    ```

7. Добавление недавно созданной файловой системы в/etc/fstab

    ``` bash
    blkid /dev/disk/azure/scsi1/lun4| awk -F\" '{print "UUID="$2" '${newmount}' "$4" defaults,nofail 0 0"}' >> /etc/fstab
    ```

8. Подключите новый созданный объект FS с помощью подключения

    ``` bash
    mount -a
    ```

9. Проверьте, подключен ли новый добавленный объект FS.

    ``` bash
    df -h
    ```

    ![изменение размера-LVM-Scenario-DF](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-df.png)

    ``` bash
    lsblk
    ```

    ![Resize-LVM-Scenario-лсблк](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-lsblk.png)

10. Повторное запуск шифрования дисков данных, запущенных ранее

    Для LVM-on-Encryption рекомендуется использовать Енкриптформаталл. в противном случае при настройке дополнительных дисков может произойти двойное шифрование.

    Сведения об использовании см. в статье [Настройка LVM при шифровании](how-to-configure-lvm-raid-on-crypt.md).

    Пример.

    ``` bash
    az vm encryption enable \
    --resource-group ${RGNAME} \
    --name ${VMNAME} \
    --disk-encryption-keyvault ${KEYVAULTNAME} \
    --key-encryption-key ${KEYNAME} \
    --key-encryption-keyvault ${KEYVAULTNAME} \
    --volume-type "DATA" \
    --encrypt-format-all \
    -o table
    ```

    После завершения шифрования вы увидите слой шифрования на вновь добавленном диске.

    ``` bash
    lsblk
    ```

    ![сценарий — lsblk2](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-lsblk2.png)

11. Отключение зашифрованного слоя нового диска

    ``` bash
    umount ${newmount}
    ```

12. Проверка текущих сведений о постоянного хранилища версий

    ``` bash
    pvs
    ```

    ![сценарий — куррентпвс](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-currentpvs.png)

13. Создание PV на основе зашифрованного уровня диска

    Возьмите имя устройства из предыдущей команды лсблк и добавьте/Дев/маппер перед именем устройства, чтобы создать ПС.

    ``` bash
    pvcreate /dev/mapper/mapperdevicename
    ```

    ![сценарий — пвкреате](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-pvcreate.png)

    Вы увидите предупреждение об очистке текущей сигнатуры Ext4 FS, это ожидаемое значение, ответьте y на этот вопрос.

14. Убедитесь, что новый ПС добавлен в конфигурацию LVM.

    ``` bash
    pvs
    ```

    ![сценарий — невпв](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-newpv.png)

15. Добавьте новый ПС в VG, который необходимо увеличить.

    ``` bash
    vgextend vgname /dev/mapper/nameofhenewpv
    ```

    ![сценарий — вжекстент](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-vgextent.png)

16. Проверка нового размера и свободного пространства VG

    ``` bash
    vgdisplay vgname
    ```

    ![сценарий — вгдисплай](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-vgdisplay.png)

    Обратите внимание на увеличение общего числа PE и бесплатного PE и размера.

17. Увеличьте размер файла lv и файловой системы с помощью параметра-r на лвекстенд (в этом примере мы используем общее доступное пространство в VG и добавим его к заданному логическому тому).

    ``` bash
    lvextend -r -l +100%FREE /dev/vgname/lvname
    ```

    ![сценарий — лвекстенд](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-lvextend.png)

18. Проверка размера LV

    ``` bash
    lvdisplay /dev/vgname/lvname
    ```

    ![сценарий — лвдисплай](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-lvdisplay.png)

19. Проверка размера только что измененного размера файловой системы

    ``` bash
    df -h mountpoint
    ```

    ![сценарий — DF1](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-df1.png)

20. Убедитесь, что слой LVM создан поверх зашифрованного слоя.

    ``` bash
    lsblk
    ```

    ![сценарий — lsblk3](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-lsblk3.png)

    Если использовать лсблк без параметров, точки подключения будут отображаться несколько раз, так как они сортируются по устройствам и логическим томам, можно использовать лсблк-FS,-s изменяет сортировку так, чтобы точки подключения отображались один раз, диски будут показаны несколько раз.

    ``` bash
    lsblk -fs
    ```

    ![сценарий — lsblk4](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-lsblk4.png)

#### <a name="extending-an-lvm-on-crypt-volume-resizing-an-existing-pv"></a>Расширение LVM на незашифрованном томе изменение размера существующего

1. Поиск зашифрованных дисков

    ``` bash
    lsblk
    ```

    ![сценариоф — lsblk01](./media/disk-encryption/resize-lvm/039-resize-lvm-scenariof-lsblk01.png)

    ``` bash
    lsblk -s
    ```

    ![сценариоф — lsblk012](./media/disk-encryption/resize-lvm/040-resize-lvm-scenariof-lsblk012.png)

2. Проверка сведений о пс

    ``` bash
    pvs
    ```

    ![сценариоф — pvs1](./media/disk-encryption/resize-lvm/041-resize-lvm-scenariof-pvs.png)

3. Проверьте сведения о VG

    ``` bash
    vgs
    ```

    ![сценариоф — VGS](./media/disk-encryption/resize-lvm/042-resize-lvm-scenariof-vgs.png)

4. Проверка информации о LV

    ``` bash
    lvs
    ```

    ![сценариоф — LVS](./media/disk-encryption/resize-lvm/043-resize-lvm-scenariof-lvs.png)

5. Проверка использования файловой системы

    ``` bash
    df -h /mountpoint(s)
    ```

    ![LVM-сценариоф-FS](./media/disk-encryption/resize-lvm/044-resize-lvm-scenariof-fs.png)

6. Проверка размеров дисков

    ``` bash
    fdisk
    fdisk -l | egrep ^"Disk /"
    lsblk
    ```

    ![сценариоф — fdisk01](./media/disk-encryption/resize-lvm/045-resize-lvm-scenariof-fdisk01.png)

7. Изменение размера диска с данными

    Вы можете ссылаться на [диски Linux](expand-disks.md) (см. раздел только изменение размера диска). Этот шаг можно выполнить с помощью портала, интерфейса командной строки или PowerShell.

    >[!NOTE]
    >Учтите, что операции изменения размера на виртуальных дисках не могут быть выполнены с виртуальной машиной, на которой выполняется. Вам потребуется отменить выделение виртуальной машины для этого шага

8. Проверка размеров дисков

    ``` bash
    fdisk
    fdisk -l | egrep ^"Disk /"
    lsblk
    ```

    ![сценариоф — fdisk02](./media/disk-encryption/resize-lvm/046-resize-lvm-scenariof-fdisk02.png)

    Обратите внимание, что (в данном случае) размер обоих дисков был изменен с 2 до 4 ГБ, однако размер FS, LV и PV не изменился.

9. Проверить текущий размер PV

    Помните, что в LVM-on-шифрования ПС — это устройство/Дев/маппер/, а не устройство/Дев/СД *.

    ``` bash
    pvdisplay /dev/mapper/devicemappername
    ```

    ![сценариоф — постоянного хранилища версий](./media/disk-encryption/resize-lvm/047-resize-lvm-scenariof-pvs.png)

10. Изменение размера PV

    ``` bash
    pvresize /dev/mapper/devicemappername
    ```

    ![сценариоф-изменить размер — НЗ](./media/disk-encryption/resize-lvm/048-resize-lvm-scenariof-resize-pv.png)

11. Проверка размера ПС после изменения размера

    ``` bash
    pvdisplay /dev/mapper/devicemappername
    ```

    ![сценариоф-ПС](./media/disk-encryption/resize-lvm/049-resize-lvm-scenariof-pv.png)

12. Изменение размера зашифрованного слоя в PV

    ``` bash
    cryptsetup resize /dev/mapper/devicemappername
    ```

    Примените ту же процедуру ко всем дискам, размер которых нужно изменить.

13. Проверьте сведения о VG

    ``` bash
    vgdisplay vgname
    ```

    ![сценариоф — VG](./media/disk-encryption/resize-lvm/050-resize-lvm-scenariof-vg.png)

    В VG теперь есть место, выделяемое для LVs

14. Проверка сведений о LV

    ``` bash
    lvdisplay vgname/lvname
    ```

    ![сценариоф-Латвия](./media/disk-encryption/resize-lvm/051-resize-lvm-scenariof-lv.png)

15. Проверка использования FS

    ``` bash
    df -h /mountpoint
    ```

    ![сценариоф-FS](./media/disk-encryption/resize-lvm/052-resize-lvm-scenariof-fs.png)

16. Изменение размера для lv

    ``` bash
    lvresize -r -L +2G /dev/vgname/lvname
    ```

    ![сценариоф — лвресизе](./media/disk-encryption/resize-lvm/053-resize-lvm-scenariof-lvresize.png)

    Мы используем параметр-r, чтобы также изменить размер службы федерации

17. Проверка сведений о LV

    ``` bash
    lvdisplay vgname/lvname
    ```

    ![сценариоф — лвсизе](./media/disk-encryption/resize-lvm/054-resize-lvm-scenariof-lvsize.png)

18. Проверка использования файловой системы

    ``` bash
    df -h /mountpoint
    ```

    ![создать файловую систему](./media/disk-encryption/resize-lvm/055-resize-lvm-scenariof-fs.png)

    Примените ту же процедуру изменения размера к любому дополнительному параметру lv, который его требует.

## <a name="next-steps"></a>Дальнейшие действия

- [Устранение неполадок с шифрованием дисков Azure](disk-encryption-troubleshooting.md)
