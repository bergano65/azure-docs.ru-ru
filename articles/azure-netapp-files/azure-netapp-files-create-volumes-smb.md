---
title: Создание тома SMB для Azure NetApp Files | Документация Майкрософт
description: В этой статье показано, как создать том SMBv3 в Azure NetApp Files. Узнайте о требованиях к Active Directoryным подключениям и доменным службам.
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
ms.topic: how-to
ms.date: 09/16/2020
ms.author: b-juche
ms.openlocfilehash: 6a90a4ad44bff392b5fe6cd0af13313bd98ce2a6
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/22/2020
ms.locfileid: "90988369"
---
# <a name="create-an-smb-volume-for-azure-netapp-files"></a>Создание тома SMB для Azure NetApp Files

Azure NetApp Files поддерживает создание томов с помощью NFS (NFSv3 и Нфсв 4.1), SMBv3 или двойного протокола (NFSv3 и SMB). Потребление емкости тома зависит от подготовленной емкости пула. В этой статье показано, как создать том SMBv3.

## <a name="before-you-begin"></a>Перед началом 
Перед началом необходимо настроить пул емкости.   
[Настройка пула емкости](azure-netapp-files-set-up-capacity-pool.md)   
Подсеть должна быть делегирована службе Azure NetApp Files.  
[Делегирование подсети службе Azure NetApp Files](azure-netapp-files-delegate-subnet.md)

## <a name="requirements-for-active-directory-connections"></a>Требования к подключениям Active Directory

 Перед созданием тома SMB необходимо создать подключения Active Directory. Ниже перечислены требования к подключениям Active Directory. 

* Используемая учетная запись администратора должна иметь возможность создавать учетные записи компьютеров в указанном пути подразделения (OU).  

* На соответствующем сервере Windows Active Directory (AD) должны быть открыты правильные порты.  
    Ниже перечислены необходимые порты. 

    |     Служба           |     Порт     |     Протокол     |
    |-----------------------|--------------|------------------|
    |    Веб-службы AD    |    9389      |    TCP           |
    |    DNS                |    53        |    TCP           |
    |    DNS                |    53        |    UDP           |
    |    ICMPv4             |    Недоступно       |    Ответ на запрос проверки связи    |
    |    Kerberos           |    464       |    TCP           |
    |    Kerberos           |    464       |    UDP           |
    |    Kerberos           |    88        |    TCP           |
    |    Kerberos           |    88        |    UDP           |
    |    LDAP               |    389       |    TCP           |
    |    LDAP               |    389       |    UDP           |
    |    LDAP               |    3268      |    TCP           |
    |    NetBIOS-имя       |    138       |    UDP           |
    |    SAM/LSA            |    445       |    TCP           |
    |    SAM/LSA            |    445       |    UDP           |
    |    w32time            |    123       |    UDP           |

* Топология сайта для целевых доменных служб Active Directory должна соответствовать рекомендациям, в частности в виртуальной сети Azure, в которой развернута Azure NetApp Files.  

    Адресное пространство виртуальной сети, в которой развертывается Azure NetApp Files, должно быть добавлено на новый или существующий сайт Active Directory (где размещен контроллер домена, доступный Azure NetApp Files). 

* Указанные DNS-серверы должны быть доступны из [делегированной подсети](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-delegate-subnet) Azure NetApp Files.  

    См. [Рекомендации по планированию сети Azure NetApp Files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-network-topologies), чтобы ознакомиться с поддерживаемыми сетевыми топологиями.

    Группы безопасности сети (NSG) и брандмауэры должны иметь соответствующим образом настроенные правила, чтобы разрешить запросы к Active Directory и запросы трафика DNS. 

* Делегированная подсеть Azure NetApp Files должна иметь доступ ко всем контроллерам домена доменных служб Active Directory служб (ADDS) в домене, включая все локальные и удаленные контроллеры домена. В противном случае работа службы может быть прервана.  

    Если у вас есть контроллеры домена, недоступные делегированной подсети Azure NetApp Files, можно указать сайт Active Directory во время создания подключения Active Directory.  Служба Azure NetApp Files должна взаимодействовать только с контроллерами домена на сайте, на которых находится адресное пространство делегированной подсети Azure NetApp Files.

    Сведения сайтах и службах Active Directory см. в разделе [Проектирование топологии сайта](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/designing-the-site-topology). 
    
* Чтобы включить шифрование AES для тома SMB, установите флажок **шифрование AES** в окне [Присоединение Active Directory](#create-an-active-directory-connection) . Azure NetApp Files поддерживает типы шифрования DES, Kerberos AES 128 и Kerberos AES 256 (от минимального уровня безопасности до наиболее безопасного). При включении шифрования AES учетные данные пользователя, используемые для приподключения Active Directory, должны иметь включенный параметр с наивысшей соответствующей учетной записью, соответствующий возможностям, включенным для Active Directory.    

    Например, если Active Directory имеет только возможность AES-128, необходимо включить параметр учетной записи AES-128 для учетных данных пользователя. Если Active Directory имеет возможность AES-256, необходимо включить параметр учетной записи AES-256 (который также поддерживает AES-128). Если у Active Directory нет функции шифрования Kerberos, Azure NetApp Files по умолчанию использует DES.  

    Параметры учетной записи можно включить в свойствах Active Directory "пользователи и компьютеры" консоли управления (MMC):   

    ![Active Directory MMC "пользователи и компьютеры"](../media/azure-netapp-files/ad-users-computers-mmc.png)

* Azure NetApp Files поддерживает [Подписывание LDAP](https://docs.microsoft.com/troubleshoot/windows-server/identity/enable-ldap-signing-in-windows-server), что обеспечивает безопасную передачу трафика LDAP между службой Azure NetApp Files и целевыми [контроллерами домена Active Directory](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview). Если вы используете рекомендации Microsoft консультационных [ADV190023](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/ADV190023) для подписывания LDAP, то следует включить функцию подписывания ldap в Azure NetApp Files, установив флажок **Подписывание LDAP** в окне [присоединиться Active Directory](#create-an-active-directory-connection) . 

    Конфигурация [привязки канала LDAP](https://support.microsoft.com/help/4034879/how-to-add-the-ldapenforcechannelbinding-registry-entry) не влияет на службу Azure NetApp Files. 

Дополнительные сведения об Active Directory см. в [часто задаваемых вопросах об SMB](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-faqs#smb-faqs) Azure NetApp Files. 

## <a name="decide-which-domain-services-to-use"></a>Выбор доменных служб для использования 

Для подключений AD Azure NetApp Files поддерживает и [доменные службы Active Directory](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/understanding-active-directory-site-topology) (ADDS), и доменные службы Azure Active Directory (AADDS).  Перед созданием подключения к Active Directory необходимо решить, следует ли использовать ADDS или AADDS.  

Дополнительные сведения см. в статье [Сравнение самостоятельно управляемых доменных служб Active Directory, Azure Active Directory и управляемых доменных служб Azure Active Directory](https://docs.microsoft.com/azure/active-directory-domain-services/compare-identity-solutions). 

### <a name="active-directory-domain-services"></a>Доменные службы Active Directory

Для Azure NetApp Files можно использовать предпочтительную область [сайтов и служб Active Directory](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/understanding-active-directory-site-topology). Этот параметр позволяет выполнять операции чтения и записи в контроллеры домена доменных служб Active Directory Services (ADDS), которые [доступны службе Azure NetApp Files](azure-netapp-files-network-topologies.md). Это также предотвращает взаимодействие службы с контроллерами домена, которые не находятся на указанном сайте модуля "Active Directory — сайты и службы". 

Чтобы найти имя сайта при использовании ADDS, можно обратиться к администраторам организации, которые отвечают за работу доменных служб Active Directory. В приведенном ниже примере показан подключаемый модуль "Active Directory — сайты и службы", в котором отображается имя сайта. 

![Active Directory — сайты и службы](../media/azure-netapp-files/azure-netapp-files-active-directory-sites-services.png)

При настройке подключения AD для Azure NetApp Files необходимо указать имя сайта в области в поле **Имя сайта AD**.

### <a name="azure-active-directory-domain-services"></a>Доменные службы Azure Active Directory 

Сведения о конфигурации доменных служб Azure Active Directory (AADDS) и соответствующие инструкции см. в статье [Документация по доменным службам Azure AD](https://docs.microsoft.com/azure/active-directory-domain-services/).

Дополнительные моменты относительно AADDS, которые следует учитывать при использовании Azure NetApp Files: 

* Убедитесь, что виртуальная сеть или подсеть, в которой развернуты AADDS, находится в том же регионе Azure, что и развертывание Azure NetApp Files.
* Если вы используете другую виртуальную сеть в регионе, где развернуты Azure NetApp Files, следует создать пиринг между двумя виртуальными сетями.
* Azure NetApp Files поддерживает типы `user` и `resource forest`.
* Для типа синхронизации можно выбрать `All` или `Scoped`.   
    При выборе `Scoped` убедитесь, что выбрана правильная группа Azure AD для доступа к общим папкам SMB.  Если вы не уверены, можно использовать тип синхронизации `All`.
* Необходимо использовать номер SKU "Корпоративный" или "Премиум". Номер SKU "Стандартный" не поддерживается.

При создании подключения Active Directory обратите внимание на следующие особенности для AADDS.

* В меню AADDS представлены сведения об **основном DNS**, **дополнительном DNS** и **имени DNS-домена AD**.  
Для DNS-серверов будут использоваться два IP-адреса для настройки подключения Active Directory. 
* **Путь подразделения** — `OU=AADDC Computers`.  
Этот параметр настраивается в подразделе **Подключения Active Directory** раздела **Учетная запись NetApp**.

  ![Путь подразделения](../media/azure-netapp-files/azure-netapp-files-org-unit-path.png)

* В поле **Имя пользователя** можно указать любого пользователя, который является членом группы Azure AD **Администраторы Azure AD DC**.


## <a name="create-an-active-directory-connection"></a>Создание подключения Active Directory

1. В учетной записи NetApp нажмите **Присоединение каталога Active Directory**, а затем нажмите **Присоединиться**.  

    ![Подключения Active Directory](../media/azure-netapp-files/azure-netapp-files-active-directory-connections.png)

2. В окне "Присоединение каталога Active Directory" укажите следующие сведения в зависимости от доменных служб, которые вы хотите использовать.  

    Сведения, относящиеся к используемым доменным службам, см. в разделе [Выбор доменных служб для использования](#decide-which-domain-services-to-use). 

    * **Основной DNS**  
        Требуется для присоединения к домену Active Directory и прохождения проверки подлинности SMB. 
    * **Дополнительный DNS**   
        Это дополнительный DNS-сервер для обеспечения избыточности служб имен. 
    * **Имя DNS-домена AD**  
        Это доменное имя доменных служб Active Directory, которые необходимо присоединить.
    * **Имя сайта AD**  
        Это имя сайта, которое будет ограничено для обнаружения контроллера домена.
    * **Префикс SMB-сервера (учетная запись компьютера)**  
        Это префикс именования для учетной записи компьютера в Active Directory, который Azure NetApp Files будет использовать для создания новых учетных записей.

        Например, если стандарт именования, используемый организацией для файловых серверов, — NAS-01, NAS-02..., NAS-045, то для префикса нужно ввести "NAS". 

        Служба будет создавать дополнительные учетные записи компьютеров в Active Directory по мере необходимости.

        > [!IMPORTANT] 
        > Переименование префикса сервера SMB после создания подключения Active Directory нарушается. После переименования префикса сервера SMB потребуется повторно подключить существующие общие ресурсы SMB.

    * **Путь подразделения**  
        Это путь LDAP к подразделению, в котором будут созданы учетные записи компьютера SMB-сервера (OU = SecondLevel, OU = FirstLevel). 

        При использовании Azure NetApp Files с доменными службами Azure Active Directory путь подразделения при настройке Active Directory для учетной записи NetApp будет `OU=AADDC Computers`.

    ![Присоединение каталога Active Directory](../media/azure-netapp-files/azure-netapp-files-join-active-directory.png)

    * **Шифрование AES**   
        Установите этот флажок, чтобы включить шифрование AES для тома SMB. Требования см. [в разделе Требования для Active Directory подключений](#requirements-for-active-directory-connections) . 

        ![Active Directory шифрование AES](../media/azure-netapp-files/active-directory-aes-encryption.png)

        Функция **шифрования AES** в настоящее время доступна в предварительной версии. Если вы впервые используете эту функцию, зарегистрируйте эту функцию, прежде чем использовать ее: 

        ```azurepowershell-interactive
        Register-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFAesEncryption
        ```

        Проверьте состояние регистрации компонента: 

        > [!NOTE]
        > **RegistrationState** может находиться в состоянии до `Registering` 60 минут до перехода на `Registered` . Прежде чем продолжить, подождите, пока состояние не будет **зарегистрировано** .

        ```azurepowershell-interactive
        Get-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFAesEncryption
        ```
        
        Вы также можете использовать [Azure CLI команды](https://docs.microsoft.com/cli/azure/feature?view=azure-cli-latest&preserve-view=true) `az feature register` , `az feature show` чтобы зарегистрировать эту функцию и отобразить состояние регистрации. 

    * **Подписывание LDAP**   
        Установите этот флажок, чтобы включить подписывание LDAP. Эта функция обеспечивает безопасный поиск LDAP между службой Azure NetApp Files и указанными пользователем [контроллерами домена служб домен Active Directory Services](https://docs.microsoft.com/windows/win32/ad/active-directory-domain-services). Дополнительные сведения см. в разделе [ADV190023 | Руководство Майкрософт по включению привязки каналов LDAP и подписывания LDAP](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/ADV190023).  

        ![Active Directory подписывания LDAP](../media/azure-netapp-files/active-directory-ldap-signing.png) 

        Функция **подписывания LDAP** сейчас доступна в предварительной версии. Если вы впервые используете эту функцию, зарегистрируйте эту функцию, прежде чем использовать ее: 

        ```azurepowershell-interactive
        Register-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFLdapSigning
        ```

        Проверьте состояние регистрации компонента: 

        > [!NOTE]
        > **RegistrationState** может находиться в состоянии до `Registering` 60 минут до перехода на `Registered` . Прежде чем продолжить, подождите, пока состояние не будет **зарегистрировано** .

        ```azurepowershell-interactive
        Get-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFLdapSigning
        ```
        
        Вы также можете использовать [Azure CLI команды](https://docs.microsoft.com/cli/azure/feature?view=azure-cli-latest&preserve-view=true) `az feature register` , `az feature show` чтобы зарегистрировать эту функцию и отобразить состояние регистрации. 

     * **Пользователи политики резервного копирования**  
        Можно включить дополнительные учетные записи, требующие повышенных прав доступа к учетной записи компьютера, созданной для использования с Azure NetApp Files. Указанным учетным записям можно будет изменять разрешения NTFS на уровне файлов и папок. Например, можно указать непривилегированную учетную запись службы, используемую для переноса данных в общую папку SMB в Azure NetApp Files.  

        ![Пользователи политики резервного копирования Active Directory](../media/azure-netapp-files/active-directory-backup-policy-users.png)

        Функция " **Пользователи политики резервного копирования** " сейчас доступна в предварительной версии. Если вы впервые используете эту функцию, зарегистрируйте эту функцию, прежде чем использовать ее: 

        ```azurepowershell-interactive
        Register-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFBackupOperator
        ```

        Проверьте состояние регистрации компонента: 

        > [!NOTE]
        > **RegistrationState** может находиться в состоянии до `Registering` 60 минут до перехода на `Registered` . Прежде чем продолжить, подождите, пока состояние не будет **зарегистрировано** .

        ```azurepowershell-interactive
        Get-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFBackupOperator
        ```
        
        Вы также можете использовать [Azure CLI команды](https://docs.microsoft.com/cli/azure/feature?view=azure-cli-latest&preserve-view=true) `az feature register` , `az feature show` чтобы зарегистрировать эту функцию и отобразить состояние регистрации. 

    * Учетные данные, включая **имя пользователя** и **пароль**.

        ![Учетные данные Active Directory](../media/azure-netapp-files/active-directory-credentials.png)

3. Щелкните **Соединить**.  

    Откроется созданное подключение Active Directory.

    ![Создано Active Directory подключений](../media/azure-netapp-files/azure-netapp-files-active-directory-connections-created.png)

## <a name="add-an-smb-volume"></a>Добавление тома SMB

1. В колонке управления пулами емкости щелкните колонку **Тома**. 

    ![Переход к томам](../media/azure-netapp-files/azure-netapp-files-navigate-to-volumes.png)

2. Чтобы создать том щелкните **+ Добавить том**.  
    Откроется окно создания тома.

3. В окне Создание тома щелкните **создать** и укажите сведения для следующих полей на вкладке "основы":   
    * **Имя тома**      
        Укажите имя создаваемого тома.   

        Имя тома должно быть уникальным в пределах каждого пула емкости. Длина имени должна быть не менее трех знаков. Имя может состоять из любых буквенно-цифровых символов.   

        В качестве имени тома нельзя использовать `default`.

    * **Пул емкости**  
        Укажите пул емкости, в котором нужно создать том.

    * **Квота**  
        Укажите объем логического хранилища, выделенный для тома.  

        В поле **Доступная квота** отображается объем неиспользуемого пространства выбранного пула емкости, которое можно использовать для создания нового тома. Размер нового тома не должен превышать доступную квоту.  

    * **Виртуальная сеть**  
        Укажите виртуальную сеть Azure, из которой будет осуществляться доступ к тому.  

        В указанной виртуальной сети должна быть подсеть, делегированная службе Azure NetApp Files. Доступ к службе Azure NetApp Files можно получить только из той же виртуальной сети либо из виртуальной сети, которая находится в том же расположении, что и том, через пиринговую связь. Доступ к тому также можно получить из локальной сети через Express Route.   

    * **Подсеть**  
        Укажите подсеть, которую нужно использовать для тома.  
        Указанная подсеть должна быть делегирована службе Azure NetApp Files. 
        
        Если вы не делегировали подсеть, можно нажать **Создать** на странице "Создать том". Затем на странице "Создание подсети" укажите сведения о подсети и выберите **Microsoft.NetApp/volumes**, чтобы делегировать подсеть службе Azure NetApp Files. В каждой виртуальной сети можно делегировать только одну подсеть для Azure NetApp Files.   
 
        ![Создание тома](../media/azure-netapp-files/azure-netapp-files-new-volume.png)
    
        ![Создание подсети](../media/azure-netapp-files/azure-netapp-files-create-subnet.png)

    * Если вы хотите применить к тому существующую политику моментальных снимков, щелкните **Показать дополнительные** , чтобы развернуть его, укажите, нужно ли скрыть путь к моментальному снимку, а затем выберите политику моментальных снимков в раскрывающемся меню. 

        Дополнительные сведения о создании политики моментальных снимков см. в разделе [Управление политиками моментальных снимков](azure-netapp-files-manage-snapshots.md#manage-snapshot-policies).

        ![Отобразить расширенный выбор](../media/azure-netapp-files/volume-create-advanced-selection.png)

4. Нажмите **Протокол** и введите следующие сведения.  
    * Выберите **SMB** в качестве типа протокола тома. 
    * В раскрывающемся списке выберите подключение **Active Directory**.
    * Укажите имя общего тома в поле **Имя общего ресурса**.

    ![Выбор протокола SMB](../media/azure-netapp-files/azure-netapp-files-protocol-smb.png)

5. Нажмите **Проверка и создание**, чтобы ознакомиться с сведениями о томе.  Затем нажмите кнопку **Создать**, чтобы создать том SMB.

    Созданный том появится на странице "Тома". 
 
    От пула емкости том наследует атрибуты подписки, группы ресурсов и расположения. Состояние развертывания можно отслеживать на вкладке уведомлений.

## <a name="control-access-to-an-smb-volume"></a>Управление доступом к тому SMB  

Управление доступом к тому SMB осуществляется с помощью разрешений.  

### <a name="share-permissions"></a>Разрешения для общего ресурса  

По умолчанию новый том имеет разрешения **"Все" и "Полный доступ"** для общего доступа. Члены группы "Администраторы домена" могут изменять разрешения на общий доступ, используя компонент "Управление компьютером" в учетной записи компьютера, используемой для тома Azure NetApp Files.

![Путь подключения SMB](../media/azure-netapp-files/smb-mount-path.png) 
![Задание разрешений для общего ресурса](../media/azure-netapp-files/set-share-permissions.png) 

### <a name="ntfs-file-and-folder-permissions"></a>Разрешения для файлов и папок NTFS  

Разрешения для файла или папки можно задать на вкладке **Безопасность** свойств объекта в клиенте SMB Windows.
 
![Задание разрешений для файлов и папок](../media/azure-netapp-files/set-file-folder-permissions.png) 

## <a name="next-steps"></a>Дальнейшие действия  

* [Подключение или отключение тома для виртуальных машин Windows или Linux](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)
* [Ограничения ресурсов для службы Azure NetApp Files](azure-netapp-files-resource-limits.md)
* [Часто задаваемые вопросы о SMB](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-faqs#smb-faqs)
* [Узнайте об интеграции виртуальной сети для служб Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-for-azure-services)
* [Установка нового леса Active Directory с помощью Azure CLI](https://docs.microsoft.com/windows-server/identity/ad-ds/deploy/virtual-dc/adds-on-azure-vm)
