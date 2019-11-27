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
ms.date: 10/12/2019
ms.author: b-juche
ms.openlocfilehash: 94fc4906478e44365d03e9c8eeadd7cb1946a43a
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/13/2019
ms.locfileid: "72300527"
---
# <a name="create-an-smb-volume-for-azure-netapp-files"></a>Создание тома SMB для Azure NetApp Files

Azure NetApp Files поддерживает тома NFS и SMBv3. Потребление емкости тома зависит от подготовленной емкости пула. В этой статье показано, как создать том SMBv3. Если вы хотите создать том NFS, см. раздел [Создание тома NFS для Azure NetApp Files](azure-netapp-files-create-volumes.md). 

## <a name="before-you-begin"></a>Перед началом работы 
Перед началом необходимо настроить пул емкости.   
[Настройка пула емкости](azure-netapp-files-set-up-capacity-pool.md)   
Подсеть должна быть делегирована службе Azure NetApp Files.  
[Делегирование подсети службе Azure NetApp Files](azure-netapp-files-delegate-subnet.md)

## <a name="requirements-for-active-directory-connections"></a>Требования к Active Directoryным подключениям

 Перед созданием тома SMB необходимо создать Active Directory подключения. Ниже приведены требования к Active Directoryным подключениям. 

* Используемая учетная запись администратора должна иметь возможность создавать учетные записи компьютеров в указанном пути подразделений (OU).  

* На соответствующем сервере Windows Active Directory (AD) должны быть открыты правильные порты.  
    Ниже приведены необходимые порты. 

    |     Служба           |     Порт     |     Протокол     |
    |-----------------------|--------------|------------------|
    |    Веб-службы Active Directory    |    9389      |    TCP           |
    |    DNS                |    53        |    TCP           |
    |    DNS                |    53        |    UDP           |
    |    ICMPv4             |    Недоступно       |    Эхо-ответ    |
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
    |    Защищенный протокол LDAP        |    636       |    TCP           |
    |    Защищенный протокол LDAP        |    3269      |    TCP           |
    |    раз            |    123       |    UDP           |

* Топология сайта для целевых служб домен Active Directory должна соответствовать рекомендациям, в частности в виртуальной сети Azure, в которой развернута Azure NetApp Files.  

    Адресное пространство для виртуальной сети, в которой развертывается Azure NetApp Files, должно быть Добавлено на новый или существующий сайт Active Directory (где находится контроллер домена, доступный Azure NetApp Files). 

* Указанные DNS-серверы должны быть доступны из [делегированной подсети](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-delegate-subnet) Azure NetApp Files.  

    См. [рекомендации по Azure NetApp Files планировании сети](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-network-topologies) для поддерживаемых сетевых топологий.

    Группы безопасности сети (группы безопасности сети) и брандмауэры должны иметь соответствующим образом настроенные правила, чтобы разрешить Active Directory и запросы на трафик DNS. 

* Azure NetApp Filesая Делегированная подсеть должна иметь доступ ко всем контроллерам домена домен Active Directory служб (ADDS) в домене, включая все локальные и удаленные контроллеры домена. В противном случае может произойти прерывание работы службы.  

    Если у вас есть контроллеры домена, недоступные через делегированную подсеть Azure NetApp Files, можно отправить запрос на поддержку Azure, чтобы изменить область с **глобального** (по умолчанию) на **сайт**.  Azure NetApp Files должны обмениваться данными только с контроллерами домена на сайте, где находится Azure NetApp Files адресное пространство делегированной подсети.

    См. раздел [проектирование топологии сайтов](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/designing-the-site-topology) о сайтах и СЛУЖБАХ Active Directory. 

## <a name="create-an-active-directory-connection"></a>Создание подключения Active Directory

1. В учетной записи NetApp щелкните **Active Directory подключения**, а затем нажмите кнопку **присоединиться**.  

    ![Подключения Active Directory](../media/azure-netapp-files/azure-netapp-files-active-directory-connections.png)

2. В окне Присоединение Active Directory укажите следующие сведения.

    * **Основной DNS-сервер**  
        Это DNS-служба, необходимая для Active Directory присоединение к домену и операции проверки подлинности SMB. 
    * **Дополнительный DNS-**    
        Это дополнительный DNS-сервер для обеспечения избыточности служб имен. 
    * **Домен**  
        Это доменное имя служб домен Active Directory, которые необходимо присоединить.
    * **Префикс сервера SMB (учетная запись компьютера)**  
        Это префикс именования для учетной записи компьютера в Active Directory, который Azure NetApp Files будет использовать для создания новых учетных записей.

        Например, если стандарт именования, используемый Организацией для файловых серверов, — NAS-01, NAS-02..., NAS-045, то для префикса нужно ввести "NAS". 

        Служба будет создавать дополнительные учетные записи компьютеров в Active Directory по мере необходимости.

    * **Путь к подразделению**  
        Это путь LDAP для подразделения, в котором будут созданы учетные записи компьютера сервера SMB. То есть OU = второго уровня, OU = First. 

        При использовании Azure NetApp Files с доменными службами Azure Active Directory путь подразделения `OU=AADDC Computers` при настройке Active Directory для учетной записи NetApp.
        
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

        В качестве имени тома нельзя использовать `default`.

    * **Пул ресурсов**  
        Укажите пул ресурсов, в котором нужно создать том.

    * **Квота**  
        Укажите объем логического хранилища, выделенный для тома.  

        В поле **Доступная квота** отображается объем неиспользуемого пространства выбранного пула емкости, которое можно использовать для создания нового тома. Размер нового тома не должен превышать доступную квоту.  

    * **Виртуальная сеть**  
        Укажите виртуальную сеть Azure, из которой вы хотите получить доступ к тому.  

        У указанной виртуальной сети должна быть Делегированная подсеть для Azure NetApp Files. Доступ к Azure NetApp Filesной службе можно получить только из той же виртуальной сети или из виртуальной сети, расположенной в том же регионе, что и том, с помощью пиринга виртуальных сетей. Вы также можете получить доступ к тому из локальной сети через Express Route.   

    * **Подсеть**  
        Укажите подсеть, которую нужно использовать для тома.  
        Указанная подсеть должна быть делегирована службе Azure NetApp Files. 
        
        Если вы не делегировали подсеть, вы можете щелкнуть **Создать** на странице "Создать том". Затем на странице "Создание подсети" укажите сведения о подсети и выберите **Microsoft.NetApp/volumes**, чтобы делегировать подсеть службе Azure NetApp Files. В каждой виртуальной сети можно делегировать только одну подсеть в Azure NetApp Files.   
 
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

## <a name="next-steps"></a>Дополнительная информация  

* [Подключение или отключение тома для виртуальных машин Windows или Linux](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)
* [Ограничения ресурсов для службы Azure NetApp Files](azure-netapp-files-resource-limits.md)
* [Часто задаваемые вопросы по SMB](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-faqs#smb-faqs)
* [Узнайте об интеграции виртуальной сети для служб Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-for-azure-services)
* [Установка нового Active Directory леса с помощью Azure CLI](https://docs.microsoft.com/windows-server/identity/ad-ds/deploy/virtual-dc/adds-on-azure-vm)
