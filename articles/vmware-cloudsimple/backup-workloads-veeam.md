---
title: Решение Azure VMware от CloudSimple - Резервное копирование виртуальных компьютеров рабочей нагрузки на private Cloud с помощью Veeam
description: Описывает, как можно создать резервную связь с виртуальными машинами, работающими в облачном облачном облаке CloudSimple Private Cloud с помощью Veeam B&R 9.5
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 3262841efb9109b1de24fe501ea0a7bea0dd612d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77025135"
---
# <a name="back-up-workload-vms-on-cloudsimple-private-cloud-using-veeam-br"></a>Резервное копирование данных VMs-технологий на облачном частном облаке с помощью Veeam B&R

В этом руководстве описывается, как можно создать резервную связь с виртуальными машинами, работающими в облачном частном облаке CloudSimple, основанном на Azure, с помощью Veeam B&R 9.5.

## <a name="about-the-veeam-back-up-and-recovery-solution"></a>О решении veeam резервного копирования и восстановления

Решение Veeam включает в себя следующие компоненты.

**Сервер резервного копирования**

Сервер резервного копирования — это сервер Windows (VM), который служит центром управления Veeam и выполняет следующие функции: 

* Координирует резервное копирование, репликацию, проверку восстановления и восстановление задач
* Контролирует планирование заданий и распределение ресурсов
* Позволяет настроить и управлять компонентами инфраструктуры резервного копирования и указать глобальные параметры для инфраструктуры резервного копирования

**Прокси-серверы**

Прокси-серверы устанавливаются между сервером резервного копирования и другими компонентами инфраструктуры резервного копирования. Они управляют следующими функциями:

* Извлечение данных VM из производственного хранилища
* Сжатие
* Дедупликация
* Шифрование
* Передача данных в резервный репозиторий

**Репозиторий резервного копирования**

Репозиторий резервного копирования — это место хранения, где Veeam хранит резервные файлы, копии VM и метаданные для реплицированных VMs.  Репозиторием может быть сервер Windows или Linux с локальными дисками (или установленным NFS/SMB) или аппаратным прибором для хранения данных.

### <a name="veeam-deployment-scenarios"></a>Сценарии развертывания Veeam
Можно использовать Azure для предоставления репозитория резервного копирования и цели хранения для долгосрочного резервного копирования и архивирования. Весь резервный сетевой трафик между вс-мистами в приватном облаке и резервным репозиторием в Azure перемещается по высокой пропускной способности, низкой задержке. Репликация трафика по регионам перемещается по внутренней сети задней плоскости Azure, что снижает затраты на пропускную способность пользователей.

**Базовое развертывание**

Для создания сред с менее чем 30 ТБ рекомендует следующую конфигурацию:

* Сервер резервного копирования Veeam и прокси-сервер, установленный на том же VM в частном облаке.
* Основной репозиторий резервного копирования на основе Linux в Azure, настроенный в качестве мишени для заданий резервного копирования.
* `azcopy`используется для копирования данных из основного репозитория резервного копирования в контейнер Azure blob, репационный в другой регион.

![Основные сценарии развертывания](media/veeam-basicdeployment.png)

**Расширенное развертывание**

Для создания сред с более чем 30 ТБ рекомендует следующую конфигурацию:

* Один прокси-сервер на узла в кластере vSAN, как это рекомендовано Veeam.
* Основной репозиторий резервного копирования Windows в частном облаке для кэширования пяти дней данных для быстрого восстановления.
* Репозиторий резервного копирования Linux в Azure в качестве мишени для резервного копирования заданий для более длительного удержания. Этот репозиторий должен быть настроен как репозиторий резервного копирования.
* `azcopy`используется для копирования данных из основного репозитория резервного копирования в контейнер Azure blob, репационный в другой регион.

![Основные сценарии развертывания](media/veeam-advanceddeployment.png)

На предыдущем рисунке обратите внимание, что прокси-сервер резервного копирования является VM с доступом Hot Add к рабочим дискам VM на хранилище данных vSAN. Veeam использует режим резервного копирования виртуального устройства прокси для vSAN.

## <a name="requirements-for-veeam-solution-on-cloudsimple"></a>Требования к решению Veeam в CloudSimple

Решение Veeam требует от вас следующего:

* Предоставьте свои собственные лицензии Veeam.
* Развертывание и управление Veeam для резервного копирования рабочих нагрузок, работающих в облачном частном облаке.

Это решение предоставляет вам полный контроль над инструментом резервного копирования Veeam и предлагает выбор в пользу родного интерфейса Veeam или плагина Veeam vCenter для управления работами резервного копирования VM.

Если вы являетесь существующим пользователем Veeam, вы можете пропустить раздел о компонентах решения Veeam и непосредственно перейти к [сценариям развертывания Veeam.](#veeam-deployment-scenarios)

## <a name="install-and-configure-veeam-backups-in-your-cloudsimple-private-cloud"></a>Установка и настройка резервных данных Veeam в облачном частном облаке

В следующих разделах описывается, как установить и настроить резервное решение Veeam для вашего облачного частного облака.

Процесс развертывания состоит из следующих этапов:

1. [vCenter UI: Настройка инфраструктурных служб в частном облаке](#vcenter-ui-set-up-infrastructure-services-in-your-private-cloud)
2. [CloudSimple портал: Настройка сети Private Cloud для Veeam](#cloudsimple-private-cloud-set-up-private-cloud-networking-for-veeam)
3. [CloudSimple портал: Эскалация привилегий](#cloudsimple-private-cloud-escalate-privileges-for-cloudowner)
4. [Портал Azure: Подключите виртуальную сеть к частному облаку](#azure-portal-connect-your-virtual-network-to-the-private-cloud)
5. [Портал Azure: Создание резервного репозитория в Azure](#azure-portal-connect-your-virtual-network-to-the-private-cloud)
6. [Портал Azure: Настройка хранения кабр Azure для долгосрочного хранения данных](#configure-azure-blob-storage-for-long-term-data-retention)
7. [vCenter UI частного облака: Установка Veeam B&R](#vcenter-console-of-private-cloud-install-veeam-br)
8. [Veeam Console: Настройка veeam Резервного копирования & программного обеспечения восстановления](#veeam-console-install-veeam-backup-and-recovery-software)
9. [Портал CloudSimple: Настройка доступа Veeam и деэскалация привилегий](#cloudsimple-portal-set-up-veeam-access-and-de-escalate-privileges)

### <a name="before-you-begin"></a>Перед началом

Для начала развертывания Veeam требуется следующее:

* Подписка На Azure, принадлежащая вам
* Предварительно созданная группа ресурсов Azure
* Виртуальная сеть Azure в подписке
* Учетная запись хранения Azure.
* [Частное облако,](create-private-cloud.md) созданное с помощью портала CloudSimple.  

На этапе реализации необходимы следующие элементы:

* Шаблоны VMware для Windows для установки Veeam (например, Windows Server 2012 R2 - 64 битизображения изображения)
* Один из доступных VLAN идентифицирован для резервной сети
* CIDR подсети, которая будет назначена в сеть резервного копирования
* Veeam 9.5 u3 устанавливаемые носители (ISO) загружены в хранилище данных vSAN частного облака

### <a name="vcenter-ui-set-up-infrastructure-services-in-your-private-cloud"></a>vCenter UI: Настройка инфраструктурных служб в частном облаке

Налаживайте инфраструктурные службы в приватном облаке, чтобы упростить управление рабочими нагрузками и инструментами.

* Вы можете добавить внешнего поставщика идентификационных данных, как описано в [Set up vCenter, для использования Active Directory,](set-vcenter-identity.md) если какой-либо из следующих применений:

  * Необходимо идентифицировать пользователей из вашего предварительного Active Directory (AD) в личном облаке.
  * Требуется настроить AD в приватном облаке для всех пользователей.
  * Вы хотите использовать Azure AD.
* Чтобы обеспечить поиск IP-адресов, управление IP-адресами и службы разрешения имен для ваших рабочих нагрузок в приватном облаке, навязайте сервер DHCP и DNS, как описано в [приложениях Setup DNS и DHCP, а также рабочие нагрузки в облачном частном облаке.](dns-dhcp-setup.md)

### <a name="cloudsimple-private-cloud-set-up-private-cloud-networking-for-veeam"></a>Облачное частное облако: настройка сети Private Cloud для Veeam

Доступ к порталу CloudSimple для создания сети Private Cloud для решения Veeam.

Создайте VLAN для резервной сети и назначайте ей подсеть CIDR. Для получения инструкций смотрите [Создание и управление VLANs/Subnets.](create-vlan-subnet.md)

Создание правил брандмауэра между подсетью управления и сетью резервного копирования, чтобы обеспечить сетевой трафик в портах, используемых Veeam. Смотрите тему Veeam [Подержанные порты](https://helpcenter.veeam.com/docs/backup/vsphere/used_ports.html?ver=95). Для инструкций по созданию [Set up firewall tables and rules](firewall.md)правил брандмауэра см.

В следующей таблице приводится список портов.

| Значок | Описание | Значок | Описание |
| ------------ | ------------- | ------------ | ------------- |
| Сервер резервного копирования  | vCenter  | HTTPS / TCP  | 443 |
| Сервер резервного копирования <br> *Требуется для развертывания компонентов резервного копирования Veeam & репликации* | Прокси резервного копирования  | TCP/UDP  | 135, 137 до 139 и 445 |
    | Сервер резервного копирования   | DNS  | UDP  | 53  | 
    | Сервер резервного копирования   | Сервер уведомлений об обновлении Veeam  | TCP  | 80  | 
    | Сервер резервного копирования   | Сервер обновления лицензии Veeam  | TCP  | 443  | 
    | Прокси резервного копирования   | vCenter |   |   | 
    | Прокси резервного копирования  | Репозиторий резервного копирования Linux   | TCP  | 22  | 
    | Прокси резервного копирования  | Репозиторий резервного копирования Windows  | TCP  | 49152 - 65535   | 
    | Репозиторий резервного копирования  | Прокси резервного копирования  | TCP  | 2500 -5000  | 
    | Репозиторий резервного копирования источника<br> *Используется для создания заданий для копирования резервного копирования*  | Целевое репозиторий резервного копирования  | TCP  | 2500 - 5000  | 

Создание правил брандмауэра между подсетью рабочей нагрузки и сетью резервного копирования, как описано в [таблицах и правилах настройки брандмауэра.](firewall.md)  Для резервного копирования и восстановления приложений необходимо открыть [дополнительные порты](https://helpcenter.veeam.com/docs/backup/vsphere/used_ports.html?ver=95) на всподанных снастями, вмещающие определенные приложения.

По умолчанию CloudSimple предоставляет ссылку на ExpressRoute с 1 Гб. Для больших размеров среды может потребоваться более высокая пропускная способность. Для получения дополнительной информации о более высокой пропускной способности обратитесь в службу поддержки Azure.

Для продолжения настройки требуется ключ авторизации и одноранговая схема URI и доступ к подписке Azure.  Эта информация доступна на странице виртуального подключения к сети на портале CloudSimple. Для инструкций [см. Получение внимательной информации для виртуальной сети Azure в связи CloudSimple.](virtual-network-connection.md) Если у вас возникли проблемы с получением информации, [обратитесь в службу поддержки.](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)

### <a name="cloudsimple-private-cloud-escalate-privileges-for-cloudowner"></a>Облачное частное облако: эскалация привилегий для владельца облачных технологий

Пользователь "облачного владельца" по умолчанию не имеет достаточных привилегий в Private Cloud vCenter для установки VEEAM, поэтому привилегии vCenter пользователя должны быть повышены. Для получения дополнительной [информации см.](escalate-private-cloud-privileges.md)

### <a name="azure-portal-connect-your-virtual-network-to-the-private-cloud"></a>Портал Azure: Подключите виртуальную сеть к частному облаку

Подключите виртуальную сеть к частному облаку, следуя инструкциям в [виртуальном сетевом подключении Azure с помощью ExpressRoute.](azure-expressroute-connection.md)

### <a name="azure-portal-create-a-backup-repository-vm"></a>Портал Azure: Создание резервного репозитория VM

1. Создайте стандартный D2 v3 VM с (2 vCPUs и 8 ГБ памяти).
2. Выберите изображение на основе CentOS 7.4.
3. Назначь группу сетевой безопасности (NSG) для VM. Убедитесь, что VM не имеет публичного IP-адреса и не доступен из публичного интернета.
4. Создайте учетную запись пользователя с имени пользователя и пароля для нового VM. Для получения инструкций смотрите [создать виртуальную машину Linux на портале Azure](../virtual-machines/linux/quick-create-portal.md).
5. Создайте стандарт 1x512 GiB HDD и прикрепите его к репозиторию VM.  Для получения инструкций см. [Как прикрепить управляемый диск данных к Windows VM на портале Azure.](../virtual-machines/windows/attach-managed-disk-portal.md)
6. [Создайте громкость XFS на управляемом диске.](https://www.digitalocean.com/docs/volumes/how-to/) Войдите в VM, используя ранее упомянутые учетные данные. Выполните следующий сценарий для создания логического объема, добавьте диск к нему, создайте [раздел](https://www.digitalocean.com/docs/volumes/how-to/partition/) файловой системы XFS и [установите](https://www.digitalocean.com/docs/volumes/how-to/mount/) раздел под пути /backup1.

    Пример скрипта

    ```
    sudo pvcreate /dev/sdc
    sudo vgcreate backup1 /dev/sdc
    sudo lvcreate -n backup1 -l 100%FREE backup1
    sudo mkdir -p /backup1
    sudo chown veeamadmin /backup1
    sudo chmod 775 /backup1
    sudo mkfs.xfs -d su=64k -d sw=1 -f /dev/mapper/backup1-backup1
    sudo mount -t xfs /dev/mapper/backup1-backup1 /backup1
    ```

7. Экспозиция /backup1 в качестве точки крепления NFS на сервер резервного копирования Veeam, который работает в приватном облаке. Для получения инструкций смотрите статью Digital Ocean [Как настроить NFS Маунт на CentOS 6](https://www.digitalocean.com/community/tutorials/how-to-set-up-an-nfs-mount-on-centos-6). Используйте это имя общего использования NFS при настройке репозитория резервного копирования на сервере резервного копирования Veeam.

8. Назначай правила фильтрации в NSG для резервного репозитория VM, чтобы явно разрешить весь сетевой трафик в и из VM.

> [!NOTE]
> Veeam Backup & Replication использует протокол SSH для связи с репозиториями резервного копирования Linux и требует утилиты SCP в репозиториях Linux. Убедитесь, что Daemon SSH настроен правильно и что SCP доступен на хосте Linux.

### <a name="configure-azure-blob-storage-for-long-term-data-retention"></a>Настройка хранения каплей Azure для долгосрочного хранения данных

1. Создание учетной записи хранения общего назначения (GPv2) стандартного типа и контейнера с каплей, как описано в видео [Microsoft, начинающемся с Azure Storage.](https://azure.microsoft.com/resources/videos/get-started-with-azure-storage)
2. Создайте контейнер для хранения Azure, как описано в ссылке [Create Container.](https://docs.microsoft.com/rest/api/storageservices/create-container)
2. Загрузите утилиту командной `azcopy` строки для Linux от Microsoft. Вы можете использовать следующие команды в оболочке Bash в CentOS 7.5.

    ```
    wget -O azcopy.tar.gz https://aka.ms/downloadazcopylinux64
    tar -xf azcopy.tar.gz
    sudo ./install.sh
    sudo yum -y install libunwind.x86_64
    sudo yum -y install icu
    ```

3. Используйте `azcopy` команду для копирования резервных файлов в контейнер blob и из нее.  Подробную [информацию о передаче данных с помощью AzCopy можно](../storage/common/storage-use-azcopy-linux.md) узнать на Linux.

### <a name="vcenter-console-of-private-cloud-install-veeam-br"></a>vCenter консоль частного облака: Установить Veeam B&R

Доступ к vCenter из вашего частного облака для создания учетной записи службы Veeam, установка Veeam B&R 9.5 и настройка Veeam с помощью учетной записи службы.

1. Создайте новую роль под названием «Роль резервного копирования Veeam» и назначайте ей необходимые разрешения в соответствии с рекомендациями Veeam. Для получения подробной информации см. тему Veeam [Необходимые разрешения](https://helpcenter.veeam.com/docs/backup/vsphere/required_permissions.html?ver=95).
2. Создайте новую группу пользователей Veeam в vCenter и присвоите ей «Роль резервного копирования Veeam».
3. Создайте новый пользователь "Veeam Service Account" и добавьте его в "Группу пользователей Veeam".

    ![Создание учетной записи службы Veeam](media/veeam-vcenter01.png)

4. Создайте распределенную группу портов в vCenter с помощью резервной сети VLAN. Для получения подробной информации просмотрите видео VMware [Создание группы распределенных портов в веб-клиенте vSphere.](https://www.youtube.com/watch?v=wpCd5ZbPOpA)
5. Создавайте vMs для резервного копирования Veeam и прокси-серверов в vCenter в [соответствии с требованиями системы Veeam.](https://helpcenter.veeam.com/docs/backup/vsphere/system_requirements.html?ver=95) Вы можете использовать Windows 2012 R2 или Linux. Для получения дополнительной [информации см.](https://www.veeam.com/kb2216)
6. Установите установку Veeam ISO в качестве устройства CDROM в резервном сервере Veeam VM.
7. Используя сеанс RDP для машины Windows 2012 R2 (цель для установки Veeam), [установите Veeam B&R 9.5u3](https://helpcenter.veeam.com/docs/backup/vsphere/install_vbr.html?ver=95) в Windows 2012 R2 VM.
8. Найдите внутренний IP-адрес резервного сервера Veeam VM и направите IP-адрес на статический на сервере DHCP. Точные шаги, необходимые для этого, зависят от сервера DHCP. Например, <a href="https://www.netgate.com/docs/pfsense/dhcp/dhcp-server.html" target="_blank">статическое отображение статей</a> Netgate в DHCP объясняет, как настроить сервер DHCP с помощью маршрутизатора pfSense.

### <a name="veeam-console-install-veeam-backup-and-recovery-software"></a>Консоль Veeam: Установка резервного копирования veeam и программного обеспечения для восстановления

Используя консоль Veeam, наверсможно нанастройку резервного копирования veeam и программное обеспечение для восстановления. Для получения подробной информации см. [Резервное копирование Veeam & репликации v9 - Установка и развертывание.](https://www.youtube.com/watch?v=b4BqC_WXARk)

1. Добавление VMware vSphere в качестве управляемой серверной среды. По запросу предоставьте учетные данные учетной записи veeam Service, созданной в начале [vCenter Console of Private Cloud: Install Veeam B&R.](#vcenter-console-of-private-cloud-install-veeam-br)

    * Используйте настройки по умолчанию для управления нагрузкой и расширенных параметров по умолчанию.
    * Установите месторасположение монтажного сервера в резервную часть.
    * Измените место резервного копирования конфигурации для сервера Veeam в удаленное репозиторий.

2. Добавьте сервер Linux в Azure в качестве резервного репозитория.

    * Используйте настройки по умолчанию для управления нагрузкой и для расширенных настроек. 
    * Установите месторасположение монтажного сервера в резервную часть.
    * Измените место резервного копирования конфигурации для сервера Veeam в удаленное репозиторий.

3. Включить шифрование резервного копирования конфигурации с помощью **настройки резервного копирования Home> Configuration.**

4. Добавьте сервер Windows VM в качестве прокси-сервера для среды VMware. Используя "Правила движения" для прокси-сервера, шифруйте резервные данные по проводу.

5. Настройка заданий резервного копирования.
    * Для настройки заданий резервного копирования следуйте инструкциям по [созданию задания резервного копирования.](https://www.youtube.com/watch?v=YHxcUFEss4M)
    * Включить шифрование резервных файлов в **рамках Расширенных Настроек > хранения.**

6. Настройка заданий копирования резервного копирования.

    * Чтобы настроить задания копирования резервного копирования, следуйте инструкциям в видео [Создание задания копирования резервного](https://www.youtube.com/watch?v=LvEHV0_WDWI&t=2s)копирования.
    * Включить шифрование резервных файлов в **рамках Расширенных Настроек > хранения.**

### <a name="cloudsimple-portal-set-up-veeam-access-and-de-escalate-privileges"></a>Портал CloudSimple: Настройка доступа Veeam и деэскалация привилегий
Создайте общедоступный IP-адрес для резервного и восстановительного сервера Veeam. Для получения инструкций [см.](public-ips.md)

Создайте правило брандмауэра, использующего, чтобы позволить серверу резервного копирования Veeam создать исходящий соединение с веб-сайтом Veeam для загрузки обновлений/патчей на порте TCP 80. Для инструкций смотрите [настройку таблиц и правил брандмауэра.](firewall.md)

Для деэскалации привилегий [см.](escalate-private-cloud-privileges.md#de-escalate-privileges)

## <a name="references"></a>Ссылки

### <a name="cloudsimple-references"></a>Ссылки CloudSimple

* [Создание частного облака](create-private-cloud.md)
* [Создание и управление VLAN/Подсетями](create-vlan-subnet.md)
* [vCenter Источники идентификации](set-vcenter-identity.md)
* [Настройка рабочей нагрузки DNS и DHCP](dns-dhcp-setup.md)
* [Эскалация привилегий](escalate-privileges.md)
* [Настройка таблиц и правил брандмауэра](firewall.md)
* [Разрешения частного облака](learn-private-cloud-permissions.md)
* [Выделение общедоступных IP-адресов](public-ips.md)

### <a name="veeam-references"></a>Ссылки на Veeam

* [Подержанные порты](https://helpcenter.veeam.com/docs/backup/vsphere/used_ports.html?ver=95)
* [Необходимые разрешения](https://helpcenter.veeam.com/docs/backup/vsphere/required_permissions.html?ver=95)
* [Требования к системе](https://helpcenter.veeam.com/docs/backup/vsphere/system_requirements.html?ver=95)
* [Установка резервного копирования Veeam & репликации](https://helpcenter.veeam.com/docs/backup/vsphere/install_vbr.html?ver=95)
* [Необходимые модули и разрешения для поддержки Multi-OS FLR и репозитория для Linux](https://www.veeam.com/kb2216)
* [Veeam Резервное копирование & репликации v9 - Установка и развертывание - Видео](https://www.youtube.com/watch?v=b4BqC_WXARk)
* [Veeam v9 Создание резервного копирования - видео](https://www.youtube.com/watch?v=YHxcUFEss4M)
* [Veeam v9 Создание резервного копирования Работа - Видео](https://www.youtube.com/watch?v=LvEHV0_WDWI&t=2s)

### <a name="azure-references"></a>Ссылки Azure

* [Настройка шлюза виртуальной сети для ExpressRoute с помощью портала Azure](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md)
* [Подключение виртуальной сети к каналу в разных подписках](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md#connect-a-vnet-to-a-circuit---different-subscription)
* [Создание виртуальной машины Linux на портале Azure](../virtual-machines/linux/quick-create-portal.md)
* [Как подключить управляемый диск данных к виртуальной машине Windows на портале Azure](../virtual-machines/windows/attach-managed-disk-portal.md)
* [Начало работы с хранилищем Azure - видео](https://azure.microsoft.com/resources/videos/get-started-with-azure-storage)
* [Create Container](https://docs.microsoft.com/rest/api/storageservices/create-container)
* [Перенос данных с помощью AzCopy для Linux](../storage/common/storage-use-azcopy-linux.md)

### <a name="vmware-references"></a>Ссылки VMware

* [Создание группы распределенных портов в веб-клиенте vSphere - видео](https://www.youtube.com/watch?v=wpCd5ZbPOpA)

### <a name="other-references"></a>Прочие ссылки

* [Создание громкости XFS на управляемом диске - RedHat](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/storage_administration_guide/ch-xfs)
* [Как настроить NFS горе на CentOS 7 - HowToForge](https://www.howtoforge.com/nfs-server-and-client-on-centos-7)
* [Настройка сервера DHCP - Netgate](https://www.netgate.com/docs/pfsense/dhcp/dhcp-server.html)
