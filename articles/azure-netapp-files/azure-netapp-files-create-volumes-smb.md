---
title: Создание тома SMB для файлов NetApp Azure (ru) Документы Майкрософт
description: Описывает, как создать том SMB для файлов NetApp Azure.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/03/2020
ms.author: b-juche
ms.openlocfilehash: c4e7566eeb28bc5709acd60ced9fcdffb7e8a725
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/05/2020
ms.locfileid: "80668000"
---
# <a name="create-an-smb-volume-for-azure-netapp-files"></a>Создание тома SMB для Azure NetApp Files

Azure NetApp Files поддерживает объемы NFS и SMBv3. Потребление емкости тома зависит от подготовленной емкости пула. В этой статье показано, как создать том SMBv3. Если вы хотите создать том NFS, [см.](azure-netapp-files-create-volumes.md) 

## <a name="before-you-begin"></a>Подготовка к работе 
Перед началом необходимо настроить пул емкости.   
[Настройка пула емкости](azure-netapp-files-set-up-capacity-pool.md)   
Подсеть должна быть делегирована службе Azure NetApp Files.  
[Делегирование подсети службе Azure NetApp Files](azure-netapp-files-delegate-subnet.md)

## <a name="requirements-for-active-directory-connections"></a>Требования к соединениям Active Directory

 Перед созданием объема SMB необходимо создать соединения Active Directory. Требования к соединениям Active Directory следующие: 

* Используемая учетная запись админа должна иметь возможность создавать учетные записи машин в пути организационного блока (OU), который вы укажете.  

* Правильные порты должны быть открыты на соответствующем сервере Windows Active Directory (AD).  
    Необходимые порты являются следующими: 

    |     Служба           |     Порт     |     Протокол     |
    |-----------------------|--------------|------------------|
    |    AD веб-службы    |    9389      |    TCP           |
    |    DNS                |    53        |    TCP           |
    |    DNS                |    53        |    UDP           |
    |    ICMPv4             |    Недоступно       |    Эхо Ответ    |
    |    Kerberos           |    464       |    TCP           |
    |    Kerberos           |    464       |    UDP           |
    |    Kerberos           |    88        |    TCP           |
    |    Kerberos           |    88        |    UDP           |
    |    LDAP               |    389       |    TCP           |
    |    LDAP               |    389       |    UDP           |
    |    LDAP               |    3268      |    TCP           |
    |    имя NetBIOS;       |    138       |    UDP           |
    |    SAM/LSA            |    445       |    TCP           |
    |    SAM/LSA            |    445       |    UDP           |
    |    w32time            |    123       |    UDP           |

* Топология сайта для целевых служб домена Active Directory должна соответствовать передовым практикам, в частности, Azure VNet, где развернуты файлы NetApp Azure.  

    Адресное пространство для виртуальной сети, в которой развернуты файлы Azure NetApp, должно быть добавлено к новому или существующему сайту Active Directory (где доименуется контроллер домена, доступный azure NetApp Files). 

* Указанные DNS-серверы должны быть доступны из [делегированной подсети](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-delegate-subnet) файлов Azure NetApp.  

    [См. Рекомендации по планированию сети Azure NetApp Files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-network-topologies) для поддерживаемых сетевых топологий.

    Группы сетевой безопасности (НСГ) и брандмауэры должны иметь соответствующие правила, позволяющие запрашивать трафик Active Directory и DNS. 

* Делегированная подсеть Azure NetApp Должна быть в состоянии охватить все доменные контроллеры Active Directory Services (ADDS) в домене, включая все локальные и удаленные контроллеры домена. В противном случае может произойти прерывание обслуживания.  

    Если у вас есть контроллеры доменов, которые недоступны делегированной подсети Azure NetApp Files, вы можете указать сайт Active Directory во время создания подключения Active Directory.  Azure NetApp Files должен общаться только с контроллерами доменов на сайте, где делегировано пространство адреса подсети Azure NetApp.

    Смотрите [Проектирование топологии сайта](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/designing-the-site-topology) о сайтах и услугах AD. 
    
<!--
* Azure NetApp Files supports DES, Kerberos AES 128, and Kerberos AES 256 encryption types (from the least secure to the most secure). The user credentials used to join Active Directory must have the highest corresponding account option enabled that matches the capabilities enabled for your Active Directory.   

    For example, if your Active Directory has only the AES-128 capability, you must enable the AES-128 account option for the user credentials. If your Active Directory has the AES-256 capability, you must enable the AES-256 account option (which also supports AES-128). If your Active Directory does not have any Kerberos encryption capability, Azure NetApp Files uses DES by default.  

    You can enable the account options in the properties of the Active Directory Users and Computers MMC console:   

    ![Active Directory Users and Computers MMC](../media/azure-netapp-files/ad-users-computers-mmc.png)
-->

О дополнительной информации о Файлах SMB Azure NetApp [Файлы SMB](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-faqs#smb-faqs) о дополнительной информации aD. 

## <a name="decide-which-domain-services-to-use"></a>Решите, какие службы домена использовать 

Файлы Azure NetApp поддерживают как [службы active Directory Domain Services](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/understanding-active-directory-site-topology) (ADDS), так и службы активных доменов каталогов Azure (AADDS) для подключения к AD.  Прежде чем создать подключение AD, необходимо решить, следует ли использовать ADDS или AADDS.  

Для получения дополнительной информации [см. Сравните самоуправляемые службы домена Active Directory, Активный каталог Azure и управляемые службы домена Active Directory Azure.](https://docs.microsoft.com/azure/active-directory-domain-services/compare-identity-solutions) 

### <a name="active-directory-domain-services"></a>Доменные службы Active Directory

Вы можете использовать предпочтительные [сайты и службы Active Directory](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/understanding-active-directory-site-topology) для файлов NetApp Azure. Эта опция позволяет читать и писать в контроллеры доменов Active Directory Domain Services (ADDS), доступные [файлами Azure NetApp Files.](azure-netapp-files-network-topologies.md) Это также не позволяет службе общаться с контроллерами доменов, которые не указаны в указанном сайте Active Directory Sites and Services. 

Чтобы найти имя сайта при использовании ADDS, вы можете связаться с административной группой в вашей организации, которая отвечает за службы домена Active Directory. В приведенном ниже примере показан плагин Active Directory Sites and Services, в котором отображается название сайта: 

![Active Directory — сайты и службы.](../media/azure-netapp-files/azure-netapp-files-active-directory-sites-and-services.png)

При настройке подключения AD для файлов Azure NetApp вы указываете имя сайта в области для поля **имени сайта AD.**

### <a name="azure-active-directory-domain-services"></a>Доменные службы Azure Active Directory 

Для настройки и руководства [Azure](https://docs.microsoft.com/azure/active-directory-domain-services/)Active Directory Domain Services (AADDS) см.

Дополнительные соображения AADDS применяются для файлов Сети Azure: 

* Убедитесь, что VNet или подсеть, в которой развернутся AADDS, находится в том же регионе Azure, что и развертывание файлов NetApp Azure.
* Если вы используете другой VNet в регионе, где развернуты файлы NetApp Azure, следует создать пиринг между двумя VNets.
* Файлы Azure `user` NetApp поддерживают и `resource forest` типы.
* Для типа синхронизации можно выбрать `All` `Scoped`или .   
    Если вы `Scoped`выберете, убедитесь, что для доступа к акциям SMB выбрана правильная группа Azure AD.  Если вы не уверены, `All` вы можете использовать тип синхронизации.
* Требуется использование Корпоративного или Премиум SKU. Стандартный SKU не поддерживается.

При создании подключения Active Directory обратите внимание на следующие особенности для AADDS:

* Вы можете найти информацию для **первичного DNS,** **Вторичный DNS**, и **AD DNS доменное имя** в меню AADDS.  
Для DNS-серверов для настройки соединения Active Directory будут использоваться два IP-адреса. 
* **Путь организационного** `OU=AADDC Computers`блока .  
Эта настройка настроена в **Active Directory Connections** под **учетную запись NetApp:**

  ![Путь организационного блока](../media/azure-netapp-files/azure-netapp-files-org-unit-path.png)

* Учетными данными **имени пользователя** может быть любой пользователь, который является участником группы Azure AD **Azure AD.**


## <a name="create-an-active-directory-connection"></a>Создание подключения Active Directory

1. С вашей учетной записи NetApp щелкните **соединения Active Directory,** затем нажмите **Join**.  

    ![Активные подключения к каталогам](../media/azure-netapp-files/azure-netapp-files-active-directory-connections.png)

2. В окне активного каталога Join укажите следующую информацию, основанную на службах домена, которые вы хотите использовать:  

    Для получения информации, конкретной для служб доменов, которые вы используете, [см.](#decide-which-domain-services-to-use) 

    * **Первичный DNS**  
        Это DNS, который необходим для присоединения домена Active Directory и операций проверки подлинности SMB. 
    * **Вторичный DNS**   
        Это вторичный DNS-сервер для обеспечения избыточных служб имен. 
    * **AD DNS доменное имя**  
        Это доменное имя ваших служб домена Active Directory, к которым вы хотите присоединиться.
    * **Название сайта AD**  
        Это имя сайта, которым будет ограничено открытие контроллера домена.
    * **Префикс сервера СМБ (компьютерная учетная запись)**  
        Это приставка именования для учетной записи машины в Active Directory, которую Файлы Azure NetApp будут использоваться для создания новых учетных записей.

        Например, если стандартименов, который ваша организация использует для файловых серверов, — ЭТО NAS-01, NAS-02..., NAS-045, то для префикса вы введете «NAS». 

        Служба создаст дополнительные учетные записи машин в Active Directory по мере необходимости.

    * **Путь организационного блока**  
        Это путь LDAP для организационной единицы (OU), где будут созданы учетные записи серверной машины SMB. То есть, OU-second уровень, OU-первый уровень. 

        Если вы используете файлы Azure NetApp с помощью служб `OU=AADDC Computers` домена Active Directory, путь организационного подразделения — при настройке Active Directory для учетной записи NetApp.
        
    * Учетные данные, включая **имя пользователя** и **пароль**

    ![Присоединиться к Active Directory](../media/azure-netapp-files/azure-netapp-files-join-active-directory.png)

3. Щелкните **Соединить**.  

    Отображается созданное вами соединение Active Directory.

    ![Активные подключения к каталогам](../media/azure-netapp-files/azure-netapp-files-active-directory-connections-created.png)

> [!NOTE] 
> После сохранения подключения Active Directory можно отсеять поля имени пользователя и паролей. Никакие другие значения не могут быть отредактированы после сохранения соединения. Если вам необходимо изменить любые другие значения, необходимо сначала удалить все развернутые объемы SMB, а затем удалить и воссоздать соединение Active Directory.

## <a name="add-an-smb-volume"></a>Добавление объема малого и среднего бизнеса

1. Нажмите на лезвие **объемов** из лезвия емкости бассейнов. 

    ![Перейдите к объемам](../media/azure-netapp-files/azure-netapp-files-navigate-to-volumes.png)

2. Чтобы создать том щелкните **+ Добавить том**.  
    Отображается окно «Создание объема».

3. В окне создания тома нажмите **«Создайте** и предоставьте информацию для следующих полей:   
    * **Имя громкости**      
        Укажите имя создаваемого тома.   

        Имя громкости должно быть уникальным в каждом пуле емкости. Длина имени должна быть не менее трех знаков. Вы можете использовать любые буквенно-цифровые символы.   

        Вы не можете `default` использовать в качестве имени громкости.

    * **Бассейн емкости**  
        Укажите пул емкости, где требуется создать том.

    * **Квоты**  
        Укажите объем логического хранилища, выделенный для тома.  

        В поле **Доступная квота** отображается объем неиспользуемого пространства выбранного пула емкости, которое можно использовать для создания нового тома. Размер нового тома не должен превышать доступную квоту.  

    * **Виртуальная сеть**  
        Укажите виртуальную сеть Azure (VNet), из которой требуется доступ к объему.  

        В указанном VNet должна быть субнет, делегированный файлам NetApp Azure. Доступ к сервису Azure NetApp Files можно получить только из того же VNet или из VNet, который находится в том же регионе, что и объем через вглядываение VNet. Вы также можете получить доступ к громкости из вашей сети через Express Route.   

    * **Подсети**  
        Укажите подсеть, которую нужно использовать для тома.  
        Указанная подсеть должна быть делегирована службе Azure NetApp Files. 
        
        Если вы не делегировали подсеть, вы можете нажать **кнопку Создать новую** на странице Создать объем. Затем на странице "Создание подсети" укажите сведения о подсети и выберите **Microsoft.NetApp/volumes**, чтобы делегировать подсеть службе Azure NetApp Files. В каждом VNet только одна подсеть может быть делегирована файлам NetApp Azure.   
 
        ![Создание тома](../media/azure-netapp-files/azure-netapp-files-new-volume.png)
    
        ![Создание подсети](../media/azure-netapp-files/azure-netapp-files-create-subnet.png)

4. Нажмите **Протокол** и заполните следующую информацию:  
    * Выберите **SMB** в качестве типа протокола для объема. 
    * Выберите соединение **Active Directory** из списка выпадающих.
    * Укажите имя общего объема в **имени Share.**

    ![Указать протокол малого и среднего бизнеса](../media/azure-netapp-files/azure-netapp-files-protocol-smb.png)

5. Нажмите **Обзор и Создать** для просмотра объем деталей.  Затем нажмите **Создать** для создания объема SMB.

    Созданный объем отображается на странице Томов. 
 
    От пула емкости том наследует атрибуты подписки, группы ресурсов и расположения. Состояние развертывания можно отслеживать на вкладке уведомлений.

## <a name="next-steps"></a>Следующие шаги  

* [Подключение или отключение тома для виртуальных машин Windows или Linux](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)
* [Ограничения ресурсов для службы Azure NetApp Files](azure-netapp-files-resource-limits.md)
* [Часто задаваемые вопросы с мсм](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-faqs#smb-faqs)
* [Узнайте о виртуальной сетевой интеграции для служб Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-for-azure-services)
* [Установка нового active Directory forest с помощью Azure CLI](https://docs.microsoft.com/windows-server/identity/ad-ds/deploy/virtual-dc/adds-on-azure-vm)
