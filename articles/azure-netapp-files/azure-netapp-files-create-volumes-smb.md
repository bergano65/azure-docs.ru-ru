---
title: Создание тома SMB для Azure NetApp Files | Документация Майкрософт
description: Описание создания тома SMB для Azure NetApp Files.
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
ms.date: 04/30/2020
ms.author: b-juche
ms.openlocfilehash: 7dfc17825fab6c9a5f0d832318cb1d57271c56da
ms.sourcegitcommit: 1895459d1c8a592f03326fcb037007b86e2fd22f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/01/2020
ms.locfileid: "82625557"
---
# <a name="create-an-smb-volume-for-azure-netapp-files"></a>Создание тома SMB для Azure NetApp Files

Azure NetApp Files поддерживает тома NFS и SMBv3. Потребление емкости тома зависит от подготовленной емкости пула. В этой статье показано, как создать том SMBv3. Если вы хотите создать том NFS, см. раздел [Создание тома NFS для Azure NetApp Files](azure-netapp-files-create-volumes.md). 

## <a name="before-you-begin"></a>Перед началом 
Перед началом необходимо настроить пул емкости.   
[Настройка пула емкости](azure-netapp-files-set-up-capacity-pool.md)   
Подсеть должна быть делегирована службе Azure NetApp Files.  
[Делегирование подсети в Azure NetApp Files](azure-netapp-files-delegate-subnet.md)

## <a name="requirements-for-active-directory-connections"></a>Требования к Active Directoryным подключениям

 Перед созданием тома SMB необходимо создать Active Directory подключения. Ниже приведены требования к Active Directoryным подключениям. 

* Используемая учетная запись администратора должна иметь возможность создавать учетные записи компьютеров в указанном пути подразделений (OU).  

* На соответствующем сервере Windows Active Directory (AD) должны быть открыты правильные порты.  
    Ниже приведены необходимые порты. 

    |     Служба           |     Порт     |     Протокол     |
    |-----------------------|--------------|------------------|
    |    Веб-службы Active Directory    |    9389      |    TCP           |
    |    DNS                |    53        |    TCP           |
    |    DNS                |    53        |    Протокол UDP           |
    |    ICMPv4             |    Н/Д       |    Эхо-ответ    |
    |    Kerberos           |    464       |    TCP           |
    |    Kerberos           |    464       |    Протокол UDP           |
    |    Kerberos           |    88        |    TCP           |
    |    Kerberos           |    88        |    Протокол UDP           |
    |    LDAP               |    389       |    TCP           |
    |    LDAP               |    389       |    Протокол UDP           |
    |    LDAP               |    3268      |    TCP           |
    |    имя NetBIOS;       |    138       |    Протокол UDP           |
    |    SAM/LSA            |    445       |    TCP           |
    |    SAM/LSA            |    445       |    Протокол UDP           |
    |    w32time            |    123       |    Протокол UDP           |

* Топология сайта для целевых служб домен Active Directory должна соответствовать рекомендациям, в частности в виртуальной сети Azure, в которой развернута Azure NetApp Files.  

    Адресное пространство виртуальной сети, в которой развертывается Azure NetApp Files, должно быть Добавлено на новый или существующий сайт Active Directory (где доступен контроллер домена, доступный по Azure NetApp Files). 

* Указанные DNS-серверы должны быть доступны из [делегированной подсети](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-delegate-subnet) Azure NetApp Files.  

    См. [рекомендации по Azure NetApp Files планировании сети](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-network-topologies) для поддерживаемых сетевых топологий.

    Группы безопасности сети (группы безопасности сети) и брандмауэры должны иметь соответствующим образом настроенные правила, чтобы разрешить Active Directory и запросы на трафик DNS. 

* Azure NetApp Filesая Делегированная подсеть должна иметь доступ ко всем контроллерам домена домен Active Directory служб (ADDS) в домене, включая все локальные и удаленные контроллеры домена. В противном случае может произойти прерывание работы службы.  

    Если у вас есть контроллеры домена, недоступные Azure NetApp Files делегированной подсети, можно указать Active Directoryный сайт во время создания подключения Active Directory.  Azure NetApp Files должны взаимодействовать только с контроллерами домена, на которых Azure NetApp Files адресное пространство делегированной подсети.

    См. раздел [проектирование топологии сайтов](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/designing-the-site-topology) о сайтах и СЛУЖБАХ Active Directory. 
    
<!--
* Azure NetApp Files supports DES, Kerberos AES 128, and Kerberos AES 256 encryption types (from the least secure to the most secure). The user credentials used to join Active Directory must have the highest corresponding account option enabled that matches the capabilities enabled for your Active Directory.   

    For example, if your Active Directory has only the AES-128 capability, you must enable the AES-128 account option for the user credentials. If your Active Directory has the AES-256 capability, you must enable the AES-256 account option (which also supports AES-128). If your Active Directory does not have any Kerberos encryption capability, Azure NetApp Files uses DES by default.  

    You can enable the account options in the properties of the Active Directory Users and Computers MMC console:   

    ![Active Directory Users and Computers MMC](../media/azure-netapp-files/ad-users-computers-mmc.png)
-->

Дополнительные сведения об Active Directory см. в разделе [вопросы и ответы по SMB](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-faqs#smb-faqs) Azure NetApp Files. 

## <a name="decide-which-domain-services-to-use"></a>Выбор служб домена для использования 

Azure NetApp Files поддерживает [службы домен Active Directory](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/understanding-active-directory-site-topology) (ADDS) и Azure Active Directory доменные службы (AADDS) для подключений AD.  Перед созданием подключения к Active Directory необходимо решить, следует ли использовать ADDS или AADDS.  

Дополнительные сведения см. в разделе [Сравнение самостоятельно управляемых служб домен Active Directory, Azure Active Directory и управляемых Azure Active Directory доменных служб](https://docs.microsoft.com/azure/active-directory-domain-services/compare-identity-solutions). 

### <a name="active-directory-domain-services"></a>Доменные службы Active Directory

Для Azure NetApp Files можно использовать предпочитаемые [сайты Active Directory и области служб](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/understanding-active-directory-site-topology) . Этот параметр позволяет выполнять операции чтения и записи в контроллеры домена домен Active Directory Services (ADDS), [Доступные для Azure NetApp Files](azure-netapp-files-network-topologies.md). Это также предотвращает взаимодействие службы с контроллерами домена, которые не находятся на указанном Active Directory сайтах и службах. 

Чтобы найти имя сайта при использовании добавления, можно связаться с административной группой в Организации, которая отвечает за домен Active Directory Services. В приведенном ниже примере показан подключаемый модуль сайтов и служб Active Directory, в котором отображается имя сайта. 

![Active Directory — сайты и службы.](../media/azure-netapp-files/azure-netapp-files-active-directory-sites-and-services.png)

При настройке подключения AD для Azure NetApp Files необходимо указать имя сайта в поле область для **имени сайта Active Directory** .

### <a name="azure-active-directory-domain-services"></a>Доменные службы Azure Active Directory 

Сведения о конфигурации доменных служб Azure Active Directory (AADDS) и инструкции см. в [документации по доменным службам Azure AD](https://docs.microsoft.com/azure/active-directory-domain-services/).

Дополнительные соображения AADDS относятся к Azure NetApp Files: 

* Убедитесь, что VNet или подсеть, в которой развернут AADDS, находится в том же регионе Azure, что и развертывание Azure NetApp Files.
* Если вы используете другую виртуальную сеть в регионе, где развернут Azure NetApp Files, следует создать пиринг между двумя виртуальных сетей.
* Azure NetApp Files поддерживает `user` типы `resource forest` и.
* Для типа синхронизации можно выбрать `All` или. `Scoped`   
    Если выбрано `Scoped`, убедитесь, что выбрана правильная группа Azure AD для доступа к общим папкам SMB.  Если вы не уверены, можно использовать тип `All` синхронизации.
* Необходимо использовать SKU Enterprise или Premium. Номер SKU "Стандартный" не поддерживается.

При создании подключения Active Directory обратите внимание на следующие особенности для AADDS:

* Сведения о **первичном DNS**, **дополнительном DNS-** домене и **доменном имени AD** DNS можно найти в меню AADDS.  
Для DNS-серверов будут использоваться два IP-адреса для настройки подключения Active Directory. 
* **Путь к подразделению** — `OU=AADDC Computers`.  
Этот параметр настраивается в **Active Directory подключениях** в разделе **учетная запись NetApp**:

  ![Путь к подразделению](../media/azure-netapp-files/azure-netapp-files-org-unit-path.png)

* Учетные данные **пользователя** могут быть любым пользователем, который является членом группы Azure AD **DC Administrators**.


## <a name="create-an-active-directory-connection"></a>Создание подключения Active Directory

1. В учетной записи NetApp щелкните **Active Directory подключения**, а затем нажмите кнопку **присоединиться**.  

    ![Подключения Active Directory](../media/azure-netapp-files/azure-netapp-files-active-directory-connections.png)

2. В окне Присоединение Active Directory укажите следующие сведения в зависимости от служб домена, которые вы хотите использовать.  

    Сведения, относящиеся к используемым службам домена, см. в разделе [Выбор служб домена для использования](#decide-which-domain-services-to-use). 

    * **Основной DNS-сервер**  
        Это DNS-служба, необходимая для Active Directory присоединение к домену и операции проверки подлинности SMB. 
    * **Вторичный DNS-сервер**   
        Это дополнительный DNS-сервер для обеспечения избыточности служб имен. 
    * **Доменное имя DNS AD**  
        Это доменное имя служб домен Active Directory, которые необходимо присоединить.
    * **Имя сайта Active Directory**  
        Это имя сайта, которое будет ограничено для обнаружения контроллера домена.
    * **Префикс сервера SMB (учетная запись компьютера)**  
        Это префикс именования для учетной записи компьютера в Active Directory, который Azure NetApp Files будет использовать для создания новых учетных записей.

        Например, если стандарт именования, используемый Организацией для файловых серверов, — NAS-01, NAS-02..., NAS-045, то для префикса нужно ввести "NAS". 

        Служба будет создавать дополнительные учетные записи компьютеров в Active Directory по мере необходимости.

    * **Путь к подразделению**  
        Это путь LDAP для подразделения, в котором будут созданы учетные записи компьютера сервера SMB. То есть OU = второго уровня, OU = First. 

        Если вы используете Azure NetApp Files с доменными службами Azure Active Directory, путь подразделения задается `OU=AADDC Computers` при настройке Active Directory для учетной записи NetApp.
        
    * Учетные данные, включая **имя пользователя** и **пароль**

    ![Присоединение Active Directory](../media/azure-netapp-files/azure-netapp-files-join-active-directory.png)

3. Щелкните **Соединить**.  

    Откроется созданное Active Directory подключение.

    ![Подключения Active Directory](../media/azure-netapp-files/azure-netapp-files-active-directory-connections-created.png)

> [!NOTE] 
> После сохранения подключения Active Directory можно изменить поля имя пользователя и пароль. После сохранения соединения другие значения не могут быть изменены. Если необходимо изменить любые другие значения, необходимо сначала удалить все развернутые тома SMB, а затем удалить и повторно создать подключение Active Directory.

## <a name="add-an-smb-volume"></a>Добавление тома SMB

1. Щелкните колонку **тома** в колонке пулы ресурсов. 

    ![Переход к томам](../media/azure-netapp-files/azure-netapp-files-navigate-to-volumes.png)

2. Чтобы создать том щелкните **+ Добавить том**.  
    Откроется окно Создание тома.

3. В окне Создание тома щелкните **создать** и укажите сведения для следующих полей:   
    * **Имя тома**      
        Укажите имя создаваемого тома.   

        Имя тома должно быть уникальным в пределах каждого пула емкости. Длина имени должна быть не менее трех знаков. Можно использовать любой алфавитно-цифровой символ.   

        Нельзя использовать `default` в качестве имени тома.

    * **Пул ресурсов**  
        Укажите пул ресурсов, в котором нужно создать том.

    * **Предел**  
        Укажите объем логического хранилища, выделенный для тома.  

        В поле **Доступная квота** отображается объем неиспользуемого пространства выбранного пула емкости, которое можно использовать для создания нового тома. Размер нового тома не должен превышать доступную квоту.  

    * **Виртуальная сеть**  
        Укажите виртуальную сеть Azure, из которой вы хотите получить доступ к тому.  

        У указанной виртуальной сети должна быть Делегированная подсеть для Azure NetApp Files. Доступ к Azure NetApp Filesной службе можно получить только из той же виртуальной сети или из виртуальной сети, расположенной в том же регионе, что и том, с помощью пиринга виртуальных сетей. Вы также можете получить доступ к тому из локальной сети через Express Route.   

    * **Подсеть**  
        Укажите подсеть, которую нужно использовать для тома.  
        Указанная подсеть должна быть делегирована службе Azure NetApp Files. 
        
        Если вы еще не делегированы подсеть, на странице Создание тома можно нажать кнопку **создать** . Затем на странице "Создание подсети" укажите сведения о подсети и выберите **Microsoft.NetApp/volumes**, чтобы делегировать подсеть службе Azure NetApp Files. В каждой виртуальной сети можно делегировать только одну подсеть в Azure NetApp Files.   
 
        ![Создание тома](../media/azure-netapp-files/azure-netapp-files-new-volume.png)
    
        ![Создание подсети](../media/azure-netapp-files/azure-netapp-files-create-subnet.png)

4. Щелкните **протокол** и укажите следующие сведения:  
    * Выберите **SMB** в качестве типа протокола для тома. 
    * Выберите подключение **Active Directory** из раскрывающегося списка.
    * Укажите имя общего тома в поле **имя общего ресурса**.

    ![Укажите протокол SMB](../media/azure-netapp-files/azure-netapp-files-protocol-smb.png)

5. Щелкните **проверить и создать** , чтобы проверить сведения о томе.  Затем щелкните **создать** , чтобы создать том SMB.

    Созданный том появится на странице тома. 
 
    От пула емкости том наследует атрибуты подписки, группы ресурсов и расположения. Состояние развертывания можно отслеживать на вкладке уведомлений.

## <a name="control-access-to-an-smb-volume"></a>Управление доступом к тому SMB  

Управление доступом к тому SMB осуществляется с помощью разрешений.  

### <a name="share-permissions"></a>Разрешения для общего ресурса  

По умолчанию новый том имеет разрешения « **все» и «полный** доступ». Члены группы "Администраторы домена" могут изменять разрешения на общий доступ, используя "Управление компьютером" для учетной записи компьютера, используемой для тома Azure NetApp Files.

![Путь](../media/azure-netapp-files/smb-mount-path.png) 
![подключения SMB. Задание разрешений для общего ресурса](../media/azure-netapp-files/set-share-permissions.png) 

### <a name="ntfs-file-and-folder-permissions"></a>Разрешения для файлов и папок NTFS  

Разрешения для файла или папки можно задать с помощью вкладки **Безопасность** свойств объекта в клиенте SMB Windows.
 
![Установка разрешений для файлов и папок](../media/azure-netapp-files/set-file-folder-permissions.png) 

## <a name="next-steps"></a>Дальнейшие действия  

* [Подключение или отключение тома для виртуальных машин Windows или Linux](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)
* [Ограничения ресурсов для службы Azure NetApp Files](azure-netapp-files-resource-limits.md)
* [Часто задаваемые вопросы по SMB](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-faqs#smb-faqs)
* [Сведения об интеграции виртуальной сети для служб Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-for-azure-services)
* [Установка нового Active Directory леса с помощью Azure CLI](https://docs.microsoft.com/windows-server/identity/ad-ds/deploy/virtual-dc/adds-on-azure-vm)
