---
title: Файлы NetApp для решения VMware для Azure
description: Используйте Azure NetApp Files с виртуальными машинами VMware в Azure для переноса и синхронизации данных между локальными серверами, виртуальными машинами Azure VMware и облачными инфраструктурами.
ms.topic: how-to
ms.date: 09/17/2020
ms.openlocfilehash: 993a67f82d5af971a4c4a0010bd9d19e2a3113b2
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/30/2020
ms.locfileid: "91573928"
---
# <a name="netapp-files-for-azure-vmware-solution"></a>Файлы NetApp для решения VMware для Azure

В этой статье мы рассмотрим шаги интеграции Azure NetApp Files с рабочими нагрузками на основе решений VMware для Azure. [Azure NetApp Files](../azure-netapp-files/azure-netapp-files-introduction.md) — это служба Azure для миграции и выполнения рабочих нагрузок корпоративных файлов в облаке без изменений кода. Она обеспечивает простую миграцию данных в локальных и облачных инфраструктурах. Быстрая синхронизация данных с повышенным уровнем безопасности упрощает миграцию и DevOps сценарии с такими возможностями, как мгновенный моментальный снимок, восстановление и Active Directoryная интеграция.

## <a name="topology"></a>Топология

В этом сценарии для проверки и проверки общего ресурса пула Azure NetApp Files Azure NetApp Files настроен в Azure с пулом емкости, пулом томов и подсетью. Мы использовали протокол NFS. Как Azure NetApp Files, так и решение Azure VMware создаются в одном регионе Azure (Восточная часть США). Подключение к решению VMware для Azure осуществляется через Azure ExpressRoute.

![Файлы NetApp для топологии решения VMware для Azure.](media/net-app-files/net-app-files-topology.png)

## <a name="prerequisites"></a>Предварительные требования 

> [!div class="checklist"]
> * Подписка Azure с Azure NetApp Files
> * Подсеть для файла NetApp для Azure
> * Виртуальная машина Linux в решении VMware для Azure
> * Виртуальная машина Windows в решении VMware для Azure

## <a name="verify-configuration-of-azure-netapp-files"></a>Проверка конфигурации Azure NetApp Files 

Сначала проверьте конфигурацию Azure NetApp Files, созданную в Azure на диске класса Premium.

1. В портал Azure в разделе **хранилище**выберите **Azure NetApp Files**. Отобразится список настроенных Azure NetApp Files.

    :::image type="content" source="media/net-app-files/azure-net-app-files-list.png" alt-text="Список Azure NetApp Files."::: 

2. При выборе учетной записи NetApp можно просматривать различные параметры. Например, если выбрать **contoso-anf2**, вы увидите его параметры. 

    :::image type="content" source="media/net-app-files/net-app-settings.png" alt-text="Список Azure NetApp Files."::: 

3. Выберите **Пулы ресурсов** , чтобы проверить настроенный пул. 

    :::image type="content" source="media/net-app-files/capacity-pools.png" alt-text="Список Azure NetApp Files.":::

    Вы видите, что пул носителей настроен как 4 Тиб с диском класса Premium.

4. Выберите **тома** (см. снимок экрана из шага 2), чтобы просмотреть тома, созданные в пуле ресурсов.  

    :::image type="content" source="media/net-app-files/azure-net-app-volumes.png" alt-text="Список Azure NetApp Files.":::

5. Выберите том, чтобы просмотреть его конфигурацию.  

    :::image type="content" source="media/net-app-files/configuration-of-volume.png" alt-text="Список Azure NetApp Files.":::

    Вы видите, что пул томов анфпул с 200 гиб был создан как общая папка файлов NetApp. Виртуальная сеть с частным IP-адресом была создана для Azure NetApp Files, которая предоставляет путь NFS для подключения к виртуальным машинам Azure VMware. 

## <a name="protocols-supported-by-azure-netapp-files"></a>Протоколы, поддерживаемые Azure NetApp Files  

Azure NetApp Files поддерживает сопоставление общего ресурса сервера (SMB) и сетевой файловой системы (NFS). 

- **Общий ресурс SMB**. для развертывания тома SMB необходимо сначала создать подключение Active Directory. Для успешного подключения указанные контроллеры домена должны быть доступны для делегированной подсети Azure NetApp Files. После настройки Active Directory в учетной записи Azure NetApp Files она будет отображаться как выбираемый элемент при создании томов SMB. 

- **Общая папка NFS**. Azure NetApp Files участвует в создании томов с помощью NFS (NFSv3 и нфсв 4.1), SMBv3 или двойного протокола (NFSV3 и SMB). Потребление емкости тома зависит от подготовленной емкости пула. NFS можно подключить к серверу Linux с помощью командной строки.

## <a name="create-azure-netapp-files"></a>Создание Azure NetApp Files 

1. Войдите на [портал Azure](https://rc.portal.azure.com/#home). В разделе службы Azure выберите **Azure NetApp Files**. 

2. Выберите **+ Добавить** , чтобы создать новый Azure NetApp Files том. 

3. Введите необходимые поля (имя, подписка, Группа ресурсов и расположение) и нажмите кнопку **создать**. 

## <a name="add-capacity-pools-into-azure-netapp-files"></a>Добавление пулов мощностей в Azure NetApp Files 

1. В портал Azure выберите **Azure NetApp Files**, выберите **Пулы ресурсов** и **+ Добавить пул**. 

2. Введите необходимые сведения для имени пула томов, уровня обслуживания и размера диска (FQDN) или IP-адреса и веса. Нажмите **Добавить**.

    :::image type="content" source="media/net-app-files/add-capacity-pool.png" alt-text="Список Azure NetApp Files.":::

3. Чтобы создать тома в пуле ресурсов, выберите **тома** на панели поиска и щелкните **+ Добавить том**. 
 
4. Заполните обязательные поля, как показано на снимке экрана ниже.

    :::image type="content" source="media/net-app-files/create-a-volume.png" alt-text="Список Azure NetApp Files.":::

5. На следующей странице выберите общий ресурс типа протокола. Выберите версии, если это общий ресурс NFS, и выберите домен Active Directory, если это общий ресурс SMB.  

6. Если это общий ресурс NFS, добавьте исходный IP-адрес, из которого будет осуществляться доступ к общему ресурсу типа протокола. Выберите **Review + create** (Просмотреть и создать). 

    :::image type="content" source="media/net-app-files/select-volume-details.png" alt-text="Список Azure NetApp Files.":::
 
    В разделе Azure NetApp Files в портал Azure общий ресурс NFS готов к использованию.

    :::image type="content" source="media/net-app-files/share-ready.png" alt-text="Список Azure NetApp Files.":::

## <a name="mount-an-nfs-file-share-on-your-azure-vmware-solution-vms"></a>Подключение файлового ресурса NFS на виртуальных машинах решения Azure VMware

### <a name="mount-an-nfs-file-share-on-an-azure-vmware-solution-windows-vm"></a>Подключение файлового ресурса NFS на виртуальной машине Azure VMware с Windows

- Откройте командную строку и выполните команду, чтобы отобразить файловый ресурс NFS. На следующих снимках экрана показан общий диск, сопоставленный на виртуальной машине Windows в решении VMware Azure.  

    :::image type="content" source="media/net-app-files/share-mapped-to-windows-vm.png" alt-text="Список Azure NetApp Files.":::
 
    :::image type="content" source="media/net-app-files/mapped-to-windows-drive.png" alt-text="Список Azure NetApp Files.":::

### <a name="mount-an-nfs-file-share-on-an-azure-vmware-solution-linux-vm"></a>Подключение файлового ресурса NFS на виртуальной машине Linux решения Azure VMware

#### <a name="setting-up-your-azure-instance"></a>Настройка экземпляра Azure

1. В портал Azure свяжите экземпляр Azure с подсетью, определенной в той же виртуальной сети, что и ваш том.

    > [!NOTE]
    > Подсети требуется правило группы безопасности сети, разрешающее трафик на портах NFS (2049, 111), UDP и TCP.

2. Откройте SSH-клиент и подключитесь к экземпляру Azure. Дополнительные сведения см. в статье [Использование ключей SSH с Windows в Azure](../virtual-machines/linux/ssh-from-windows.md).

3. Установите клиент NFS в экземпляре Azure:
   - На экземпляре Red Hat Enterprise Linux или SUSE Linux: `sudo yum install -y nfs-utils`
   - На экземпляре Ubuntu или Debian: `sudo apt-get install nfs-common` 

#### <a name="mounting-your-file-system"></a>Подключение файловой системы

1. Создайте новый каталог в экземпляре Azure: `sudo mkdir ANFPOOL`

2. Выберите целевой IP-адрес подключения.

3. Подключите файловую систему: `sudo mount -t nfs -o rw,hard,rsize=1048576,wsize=1048576,vers=3,tcp 10.22.3.4:/ANFPOOL ANFPOOL`

    :::image type="content" source="media/net-app-files/root-test.png" alt-text="Список Azure NetApp Files.":::

## <a name="create-an-active-directory-connection-for-an-smb-share"></a>Создание подключения Active Directory для общего ресурса SMB

1. Выберите учетную запись NetApp в портал Azure.

2. В разделе Azure NetApp Files выберите **Active Directory подключения**.

    :::image type="content" source="media/net-app-files/active-directory-connections.png" alt-text="Список Azure NetApp Files."::: 

3. Выберите **присоединиться** к общему ресурсу SMB для Active Directory. На следующем снимке экрана показаны сведения о домене общего ресурса SMB.

    :::image type="content" source="media/net-app-files/active-directory-connect-details.png" alt-text="Список Azure NetApp Files."::: 

    Файловый ресурс SMB Azure готов к использованию.  

    :::image type="content" source="media/net-app-files/smb-file-share-details.png" alt-text="Список Azure NetApp Files."::: 

4. Сопоставьте общий ресурс SMB с виртуальной машиной решения VMware для Windows Azure. Используйте путь подключения SMB, как показано на предыдущем снимке экрана. Дополнительные сведения см. [в статье Подключение сетевого диска в Windows 10](https://support.microsoft.com/help/4026635/windows-10-map-a-network-drive) .

## <a name="next-steps"></a>Дальнейшие действия
- Дополнительные сведения об [иерархии хранения Azure NetApp Files](../azure-netapp-files/azure-netapp-files-understand-storage-hierarchy.md).
- См. статью [Управление жизненным циклом виртуальных машин Azure VMware](lifecycle-management-of-azure-vmware-solution-vms.md) .
- См. статью [интеграция решения VMware с Azure в центральную и периферийную архитектуру](concepts-hub-and-spoke.md) .
