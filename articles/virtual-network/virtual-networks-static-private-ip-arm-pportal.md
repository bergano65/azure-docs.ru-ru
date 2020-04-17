---
title: Настройка частных IP-адресов для ВМ - портал Azure
description: Узнайте, как настроить частные IP-адреса для виртуальных машин с помощью портала Azure.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
tags: azure-resource-manager
ms.assetid: 11245645-357d-4358-9a14-dd78e367b494
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/07/2020
ms.author: kumud
ms.openlocfilehash: 946926a8a805ec3c53ea3c57dc3eded2462f7673
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81461554"
---
# <a name="configure-a-private-ip-address-for-a-vm-using-the-azure-portal"></a>Налажить частный IP-адрес для VM с помощью портала Azure

[!INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[!INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

Следующие шаги выборки ожидают, что простая среда уже будет создана. Если вы хотите запустить шаги по мере отображения в этом документе, сначала [создайте виртуальную сеть.](quick-create-portal.md#create-a-virtual-network) Однако в шаге 3 используйте эти значения вместо этого:

| Параметр | Значение |
| ------- | ----- |
| Имя | *TestVNet* |
| Пространство адресов | *192.168.0.0/16* |
| Группа ресурсов | **TestRG** (при необходимости, выберите **Создать новый** для его создания) |
| Имя подсети | *Frontend* |
| Диапазон адреса подсети | *192.168.1.0/24* |

## <a name="create-a-vm-for-testing-static-private-ip-addresses"></a>Создание VM для тестирования статических частных IP-адресов
При создании VM в режиме развертывания диспетчера ресурсов нельзя установить статический частный IP-адрес с помощью портала Azure. Вместо этого сначала вы создаете VM. После этого вы можете установить свой приватный IP для того чтобы быть статичным.

Чтобы создать VM *подсети* *FrontEnd* виртуальной сети *TestVNet,* выполните следующие действия:

1. Из меню [портала Azure](https://portal.azure.com) выберите **Создать ресурс.**

    ![Создание ресурса, портал Azure](./media/virtual-networks-static-ip-arm-pportal/create-a-resource.png)
2. Выберите **компьютерную** > **виртуальную машину.**

    ![Создание VM, портал Azure](./media/virtual-networks-static-ip-arm-pportal/compute-virtual-machine.png)
3. В **основах**— указать значения для элементов, описанных в следующей таблице. Затем выберите **Следующий&nbsp;:&nbsp;Диски,** а затем **Далее&nbsp;:&nbsp;Сеть**.

    | Item | Значение |
    | --- | --- |
    | **Подписка** | Текущая подписка |
    | **Группа ресурсов** | **TestRG** (выберите из списка выпадающих) |
    | **Имя виртуальной машины** | *DNS01* |
    | **Регион** | **Восточная часть США (США)** |
    | **ОС контейнера** | **Windows Server 2019 Datacenter** |
    | **Размер** | **VM Размер** **B1ls**, **Предложение** **стандартных** |
    | **Имя пользователя** | Имя пользователя учетной записи администратора |
    | **Пароль** | Пароль для имени пользователя учетной записи администратора |
    | **Подтверждение пароля** | Пароль снова |

    ![Вкладка Основы, Создайте виртуальную машину, портал Azure](./media/virtual-networks-static-ip-arm-pportal/create-a-virtual-machine-basics.png)
4. В **сети**укажите значения для элементов, описанные в следующей таблице, а затем выберите **Следующий**.

    | Item | Значение |
    | --- | --- |
    | **Виртуальная сеть** | **TestVNet** |
    | **Подсеть** | **Frontend** |

    ![Сетевая вкладка, Создание виртуальной машины, портал Azure](./media/virtual-networks-static-ip-arm-pportal/create-a-virtual-machine-networking.png)
5. В **управлении**, под **диагностической учетной записи хранения,** выбрать **vnetstorage**. Если эта учетная запись хранилища не отображается в списке, выберите **Создать новый,** указать **имя** *vnetstorage*и выбрать **OK.** Наконец, выберите **Обзор&nbsp;+&nbsp;создать**.

    ![Вкладка управления, Создание виртуальной машины, портал Azure](./media/virtual-networks-static-ip-arm-pportal/create-a-virtual-machine-management.png)
6. В **обзоре и создать**, просмотрите обзор информации, а затем выберите **Создать**.

    ![Обзор : создайте вкладку, создайте виртуальную машину, портал Azure](./media/virtual-networks-static-ip-arm-pportal/create-a-virtual-machine-review-create.png)

Следующее сообщение появляется после создания VM.

![Сообщение о завершении развертывания, Создание виртуальной машины, портал Azure](./media/virtual-networks-static-ip-arm-pportal/deployment-is-complete.png)

## <a name="retrieve-private-ip-address-information-for-a-vm"></a>Извлекать информацию о частных IP-адресах для VM
Для просмотра информации о частном IP-адресе для вашего нового VM:

1. Перейдите на [портал Azure,](https://portal.azure.com) чтобы найти ваш VM. Найдите и щелкните **Виртуальные машины**.

    ![Виртуальные машины, Поисковая коробка, портал Azure](./media/virtual-networks-static-ip-arm-pportal/search-box-virtual-machines.png)

2. Выберите имя нового VM **(DNS01**).

    ![Виртуальный список машин, портал Azure](./media/virtual-networks-static-ip-arm-pportal/virtual-machine-list.png)

3. Выберите **Сеть**и выберите единственный в списке сетевой интерфейс.

    ![Сетевой интерфейс, сеть, виртуальная машина, портал Azure](./media/virtual-networks-static-ip-arm-pportal/networking-network-interface.png)

4. Выберите **конфигурации IP**и выберите конфигурацию IP, указанную в таблице.

    ![Конфигурация IP, сетевой интерфейс, сеть, виртуальная машина, портал Azure](./media/virtual-networks-static-ip-arm-pportal/network-interface-ip-configurations.png)

5. В **настройках частных IP-адресов,** под виртуальной сетью/подсетью **TestVNet/FrontEnd** обратите внимание на значение **назначения** **(dynamic** or **Static)** и **IP-адрес.**

    ![Динамическое или статическое назначение, старые настройки приватных IP-адресов, конфигурация IP, сетевой интерфейс, сеть, виртуальная машина, портал Azure](./media/virtual-networks-static-ip-arm-pportal/private-ip-address-settings-old.png)

## <a name="add-a-static-private-ip-address-to-an-existing-vm"></a>Добавление статического частного IP-адреса для существующей виртуальной машины
Чтобы добавить статический частный IP-адрес в ваш новый VM:

1. На странице конфигурации IP установите назначение для вашего личного IP-адреса на **Static.**
2. Измените свой личный **IP-адрес** на *192.168.1.101*, а затем выберите **Сохранить**.
   
    ![Динамическое или статическое назначение, новые настройки частных IP-адресов, конфигурация IP, сетевой интерфейс, сеть, виртуальная машина, портал Azure](./media/virtual-networks-static-ip-arm-pportal/private-ip-address-settings-new.png)

> [!NOTE]
> Если вы заметили, что после выбора **Сохранить,** что назначение по-прежнему настроено на **динамический,** IP-адрес, который вы набрали уже используется. Попробуйте другой IP-адрес.

## <a name="remove-a-static-private-ip-address-from-a-vm"></a>Удаление статического частного IP-адреса виртуальной машины
Чтобы удалить статический частный IP-адрес из вашего VM:

На странице конфигурации IP установите назначение для вашего личного IP-адреса на **Dynamic,** а затем выберите **Сохранить.**

## <a name="set-ip-addresses-within-the-operating-system"></a>Настройка IP-адресов в операционной системе

В операционной системе VM не следует статически назначать *частный* IP-адрес, назначенный VM Azure. Только статическое назначение частного IP, когда это необходимо, например, при [назначении многих IP-адресов для VMs.](virtual-network-multiple-ip-addresses-portal.md) Если вы вручную установите личный IP-адрес в операционной системе, убедитесь, что он соответствует частному IP-адресу, назначенному [сетевому интерфейсу](virtual-network-network-interface-addresses.md#change-ip-address-settings)Azure. В противном случае вы можете потерять подключение к VM. Ознакомьтесь с дополнительными сведениями о параметрах [частных IP-адресов](virtual-network-network-interface-addresses.md#private).

Кроме того, вы никогда не должны вручную присваивать *общедоступный* IP-адрес, назначенный виртуальной машине Azure в операционной системе виртуальной машины.

## <a name="next-steps"></a>Следующие шаги

Ознакомьтесь с дополнительными сведениями об управлении [параметрами IP-адресов](virtual-network-network-interface-addresses.md).
