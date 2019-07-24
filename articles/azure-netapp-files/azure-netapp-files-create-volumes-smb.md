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
ms.date: 7/9/2019
ms.author: b-juche
ms.openlocfilehash: 6e425eba3159f8840e1a7960f6a6c3171b1ba163
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/12/2019
ms.locfileid: "67850422"
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

    |     Service           |     Порт     |     Протокол     |
    |-----------------------|--------------|------------------|
    |    Веб-службы Active Directory    |    9389      |    TCP           |
    |    DNS                |    53        |    TCP           |
    |    DNS                |    53        |    UDP           |
    |    ICMPv4             |    Н/Д       |    Эхо-ответ    |
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

## <a name="create-an-active-directory-connection"></a>Создание подключения Active Directory

1. В учетной записи NetApp щелкните **Active Directory подключения**, а затем нажмите кнопку присоединиться.  

    ![Подключения Active Directory](../media/azure-netapp-files/azure-netapp-files-active-directory-connections.png)

2. В окне Присоединение Active Directory укажите следующие сведения.

    * **Основной DNS-сервер**  
        Это DNS-служба, необходимая для Active Directory присоединение к домену и операции проверки подлинности SMB. 
    * **Вторичный DNS-сервер**   
        Это дополнительный DNS-сервер для обеспечения избыточности служб имен. 
    * **Домен**  
        Это доменное имя служб домен Active Directory, которые необходимо присоединить.
    * **Префикс сервера SMB (учетная запись компьютера)**  
        Это префикс именования для учетной записи компьютера в Active Directory, который Azure NetApp Files будет использовать для создания новых учетных записей.

        Например, если стандарт именования, используемый Организацией для файловых серверов, — NAS-01, NAS-02..., NAS-045, то для префикса нужно ввести "NAS". 

        Служба будет создавать дополнительные учетные записи компьютеров в Active Directory по мере необходимости.

    * **Путь к подразделению**  
        Это путь LDAP для подразделения, в котором будут созданы учетные записи компьютера сервера SMB. То есть OU = второго уровня, OU = First. 
    * Учетные данные, включая **имя пользователя** и **пароль**

    ![Присоединение Active Directory](../media/azure-netapp-files/azure-netapp-files-join-active-directory.png)

3. Щелкните **Соединить**.  

    Откроется созданное Active Directory подключение.

    ![Подключения Active Directory](../media/azure-netapp-files/azure-netapp-files-active-directory-connections-created.png)

## <a name="add-an-smb-volume"></a>Добавление тома SMB

1. Щелкните колонку **тома** в колонке пулы ресурсов. 

    ![Переход к томам](../media/azure-netapp-files/azure-netapp-files-navigate-to-volumes.png)

2. Чтобы создать том щелкните **+ Добавить том**.  
    Откроется окно Создание тома.

3. В окне Создание тома щелкните **создать** и укажите сведения для следующих полей:   
    * **Имя тома**      
        Укажите имя создаваемого тома.   

        Имя тома должно быть уникальным в пределах каждого пула емкости. Длина имени должна быть не менее трех знаков. Можно использовать любой алфавитно-цифровой символ.

    * **Пул ресурсов**  
        Укажите пул ресурсов, в котором нужно создать том.

    * **Квота**  
        Укажите объем логического хранилища, выделенный для тома.  

        В поле **Доступная квота** отображается объем неиспользуемого пространства выбранного пула емкости, которое можно использовать для создания нового тома. Размер нового тома не должен превышать доступную квоту.  

    * **Виртуальная сеть**  
        Укажите Виртуальную сеть Azure, из которой будет осуществляться доступ к тому.  

        В указанной виртуальной сети должна быть подсеть, делегированная службе Azure NetApp Files. Доступ к службе Azure NetApp Files можно получить только из той же виртуальной сети либо из виртуальной сети, которая находится в том же расположении, что и том, через пиринговую связь. Вы также можете получить доступ к тому из локальной сети через Express Route.   

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

## <a name="next-steps"></a>Следующие шаги  

* [Подключение или отключение тома для виртуальных машин Windows или Linux](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)
* [Ограничения ресурсов для службы Azure NetApp Files](azure-netapp-files-resource-limits.md)
* [Узнайте об интеграции виртуальной сети для служб Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-for-azure-services)
