---
title: Решение Azure VMware с помощью Клаудсимпле. Создание виртуальной машины в Azure с помощью шаблонов виртуальных машин
description: В этой статье описывается создание виртуальных машин в Azure с помощью шаблонов виртуальных машин в инфраструктуре VMware для частного облака Клаудсимпле.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: ee3029de9826aee17dc76d0e69f08b3c1068423b
ms.sourcegitcommit: 5ded08785546f4a687c2f76b2b871bbe802e7dae
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/19/2019
ms.locfileid: "69576840"
---
# <a name="create-a-virtual-machine-in-azure-using-vm-templates-on-the-vmware-infrastructure"></a>Создание виртуальной машины в Azure с помощью шаблонов виртуальных машин в инфраструктуре VMware

Вы можете создать виртуальную машину на портал Azure с помощью шаблонов виртуальных машин в инфраструктуре VMware, которую администратор Клаудсимпле включил для вашей подписки.

## <a name="sign-in-to-azure"></a>Войдите в Azure

Войдите на [портале Azure](https://portal.azure.com).

## <a name="create-cloudsimple-virtual-machine"></a>Создание виртуальной машины Клаудсимпле

1. Выбор пункта **Все службы**.

2. Выполните поиск **CloudSimple Virtual Machines**.

3. Нажмите кнопку **Добавить**.

    ![Создание виртуальной машины Клаудсимпле](media/create-cloudsimple-virtual-machine.png)

4. Введите Основные сведения нажмите кнопку **Далее: размер**.

    > [!NOTE]
    > Для создания виртуальной машины Клаудсимпле в Azure требуется шаблон виртуальной машины.  Этот шаблон виртуальной машины должен существовать в частном облаке vCenter.  Создайте виртуальную машину в частном облаке из пользовательского интерфейса vCenter с требуемой операционной системой и конфигурациями.  С помощью инструкций в разделе [клонирование виртуальной машины в шаблон в веб-клиенте vSphere](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-FE6DE4DF-FAD0-4BB0-A1FD-AFE9A40F4BFE_copy.html)создайте шаблон.

    ![Создание виртуальной машины Клаудсимпле — основы](media/create-cloudsimple-virtual-machine-basic-info.png)

    | Поле | Описание |
    | ------------ | ------------- |
    | Подписка | Подписка Azure, связанная с частным облаком.  |
    | Группа ресурсов | Группа ресурсов, которой будет назначена виртуальная машина. Можно выбрать имеющуюся группу или создать новую. |
    | Название | Имя, идентифицирующее виртуальную машину.  |
    | Местоположение | Регион Azure, в котором размещена эта виртуальная машина.  |
    | Частное облако | Клаудсимпле частное облако, в котором вы хотите создать виртуальную машину. |
    | Пул ресурсов | Сопоставленный пул ресурсов для виртуальной машины. Выберите из доступных пулов ресурсов. |
    | Шаблон vSphere | шаблон vSphere для виртуальной машины.  |
    | Имя пользователя | Имя пользователя администратора виртуальной машины (для шаблонов Windows)|
    | Пароль <br>Подтвердите пароль | Пароль для администратора виртуальной машины (для шаблонов Windows).  |

5. Выберите количество ядер и объем памяти для виртуальной машины и нажмите кнопку **Далее: конфигурации**. Установите флажок, если требуется предоставить полную виртуализацию ЦП операционной системе на виртуальной машине, чтобы приложения, требующие виртуализации оборудования, могли работать на виртуальных машинах без двоичного перевода или виртуализации. Дополнительные сведения см. в статье VMware [Expose VMware Hardware Assisted Virtualization](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-2A98801C-68E8-47AF-99ED-00C63E4857F6.html) (Предоставление аппаратной виртуализации VMware).

    ![Создание виртуальной машины Клаудсимпле. размер](media/create-cloudsimple-virtual-machine-size.png)

6. Настройте сетевые интерфейсы и диски, как описано в следующих таблицах, и щелкните **проверить и создать**.

    ![Создание виртуальной машины Клаудсимпле. конфигурации](media/create-cloudsimple-virtual-machine-configurations.png)

    Для параметра сетевые интерфейсы щелкните **Добавить сетевой интерфейс** и настройте следующие параметры.

    | Элемент управления | Описание |
    | ------------ | ------------- |
    | Название | Введите имя для идентификации интерфейса.  |
    | Network | Выберите из списка настроенной распределенной группы портов в vSphere частного облака.  |
    | Адаптер | Выберите адаптер vSphere из списка доступных типов, настроенных для виртуальной машины. Дополнительные сведения см. в статье базы знаний VMware [Выбор сетевого адаптера для виртуальной машины](https://kb.vmware.com/s/article/1001805). |
    | Включение при загрузке | Выберите, следует ли включить оборудование сетевого адаптера при загрузке виртуальной машины. По умолчанию этот параметр имеет значение **Enable** (Включить). |

    Для параметра диски щелкните **Добавить диск** и настройте следующие параметры.

    | Элемент | Описание |
    | ------------ | ------------- |
    | Название | Введите имя для идентификации диска.  |
    | Size | Выберите один из доступных размеров.  |
    | Контроллер SCSI | Выберите контроллер SCSI для диска.  |
    | Mode | Определяет, как диск участвует в моментальных снимках. Выберите один из следующих параметров. <br> -Независимый постоянный: Все данные, записываемые на диск, записываются безвозвратно.<br> — Независимо от постоянного: Изменения, записываемые на диск, отменяются при выключении или сбросе виртуальной машины.  Режим независимого временного хранения позволяет восстановить состояние виртуальной машины, просто перезапустив ее. Дополнительные сведения см. в [документации VMware](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-8B6174E6-36A8-42DA-ACF7-0DA4D8C5B084.html).

7. После завершения проверки проверьте параметры и нажмите кнопку **создать**. Чтобы внести изменения, щелкните вкладки вверху или щелкните.

    ![Создание виртуальной машины Клаудсимпле — обзор](media/create-cloudsimple-virtual-machine-review.png)

## <a name="view-list-of-cloudsimple-virtual-machines"></a>Просмотр списка виртуальных машин Клаудсимпле

1. Выбор пункта **Все службы**.

2. Выполните поиск **CloudSimple Virtual Machines**.

3. Выберите, в котором было создано частное облако.

    ![Список виртуальных машин Клаудсимпле](media/list-cloudsimple-virtual-machines.png)

Список виртуальных машин Клаудсимпле включает виртуальные машины, созданные из портал Azure.  Виртуальные машины, созданные в частном облаке vCenter в сопоставленном пуле ресурсов vCenter, будут показаны в списке.  
