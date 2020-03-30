---
title: 'Быстрый запуск: Потребляйте VMware VMs на Azure'
titleSuffix: Azure VMware Solution by CloudSimple
description: Узнайте, как настроить и использовать VMware с портала Azure с помощью решения Azure VMware от CloudSimple
author: sharaths-cs
ms.author: dikamath
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 4ab613c251bc43a025e0381046805ec998a04227
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77019559"
---
# <a name="quickstart---consume-vmware-vms-on-azure"></a>Быстрый старт - Потребляйте VMware VMs на Azure

Чтобы создать виртуальную машину на портале Azure, используйте шаблоны виртуальных машин, которые администратор CloudSimple включил для подписки. Шаблоны VM находятся в инфраструктуре VMware.

## <a name="cloudsimple-vm-creation-on-azure-requires-a-vm-template"></a>Создание CloudSimple VM на Azure требует шаблона VM

Создайте виртуальную машину на личном облаке из uI vCenter. Чтобы создать шаблон, следуйте инструкциям в [Клон виртуальная машина шаблон в vSphere веб-клиента](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.vm_admin.doc/GUID-FE6DE4DF-FAD0-4BB0-A1FD-AFE9A40F4BFE.html). Храните шаблон VM на вашем Private Cloud vCenter.

## <a name="create-a-virtual-machine-in-the-azure-portal"></a>Создание виртуальной машины на портале Azure

1. Выберите **все услуги**.

2. Выполните поиск **CloudSimple Virtual Machines**.

3. Нажмите кнопку **Добавить**.

    ![Создание виртуальной машины CloudSimple](media/create-cloudsimple-virtual-machine.png)

4. Введите основную информацию и нажмите **Далее:Размер**.

    ![Создание виртуальной машины CloudSimple - основы](media/create-cloudsimple-virtual-machine-basic-info.png)

    | Поле | Описание |
    | ------------ | ------------- |
    | Подписка | Подписка Azure, связанная с вашим частным облаком.  |
    | Группа ресурсов | Ресурсная группа, которой будет назначен VM. Можно выбрать имеющуюся группу или создать новую. |
    | name | Имя для идентификации VM.  |
    | Расположение | Azure области, в которой этот VM размещается.  |
    | Частное облако | CloudSimple Private Cloud, где вы хотите создать виртуальную машину. |
    | Пул ресурсов | Картированный ресурсный пул для ВМ. Выберите из доступных пулов ресурсов. |
    | vSphere Template (Шаблон vSphere) | vSphere шаблон для VM.  |
    | Имя пользователя | Имя пользователя администратора VM (для шаблонов Windows).|
    | Пароль |  Пароль для администратора VM (для шаблонов Windows). |
    | Подтверждение пароля | Подтвердите пароль. |

5. Выберите количество ядер и емкость памяти для VM и нажмите **Далее: Конфигурации.** Выберите флажок, если вы хотите предоставить полную виртуализацию процессора гостевой операционной системе. Приложения, требующие аппаратной виртуализации, смогут работать на виртуальных машинах без двоичной трансляции или паравиртуализации. Дополнительные сведения см. в статье VMware <a href="https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-2A98801C-68E8-47AF-99ED-00C63E4857F6.html" target="_blank">Expose VMware Hardware Assisted Virtualization</a> (Предоставление аппаратной виртуализации VMware).

    ![Создание виртуальной машины CloudSimple - размер](media/create-cloudsimple-virtual-machine-size.png)

6. Настройка сетевых интерфейсов и дисков, как описано в следующих таблицах и нажмите **Обзор и создать**.

    ![Создание виртуальной машины CloudSimple - конфигурации](media/create-cloudsimple-virtual-machine-configurations.png)

    Для сетевых интерфейсов нажмите **«Добавить сетевой интерфейс»** и настройте следующие настройки.

    | Control | Описание |
    | ------------ | ------------- |
    | name | Введите имя для идентификации интерфейса.  |
    | Сеть | Выберите из списка настроенной распределенной группы портов в вашем Private Cloud vSphere.  |
    | Адаптер | Выберите адаптер vSphere из списка доступных типов, настроенных для VM. Для получения дополнительной информации смотрите статью базы знаний VMware <a href="https://kb.vmware.com/s/article/1001805" target="_blank">Выбор сетевого адаптера для виртуальной машины.</a> |
    | Power on at Boot (Включать при загрузке) | Выберите, следует ли включить оборудование сетевого адаптера при загрузке виртуальной машины. По умолчанию этот параметр имеет значение **Enable** (Включить). |

    Для дисков нажмите **Добавить диск** и настроить следующие настройки.

    | Item | Описание |
    | ------------ | ------------- |
    | name | Введите имя для идентификации диска.  |
    | Размер | Выберите один из доступных размеров.  |
    | SCSI Controller (Контроллер SCSI) | Выберите контроллер SCSI для диска.  |
    | Режим | Определяет, как диск участвует в моментальных снимках. Выберите один из следующих параметров. <br> - Независимый настойчивый: Все данные, записанные на диск, пишутся постоянно.<br> - Независимые непостоянные: Изменения, написанные на диске, отбрасываются при отключении или сбросе виртуальной машины.  Режим независимого временного хранения позволяет восстановить состояние виртуальной машины, просто перезапустив ее. Дополнительные сведения см. в <a href="https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-8B6174E6-36A8-42DA-ACF7-0DA4D8C5B084.html" target="_blank">документации VMware</a>.

7. При завершении проверки просмотрите настройки и нажмите **«Создать».** Чтобы внести какие-либо изменения, нажмите на вкладки в верхней части или нажмите кнопку.

    ![Создание виртуальной машины CloudSimple - обзор](media/create-cloudsimple-virtual-machine-review.png)

## <a name="next-steps"></a>Дальнейшие действия

* [Просмотр списка виртуальных машин CloudSimple](azure-create-vm.md#view-list-of-cloudsimple-virtual-machines)
* [Управление виртуальной машиной CloudSimple от Azure](azure-manage-vm.md)
