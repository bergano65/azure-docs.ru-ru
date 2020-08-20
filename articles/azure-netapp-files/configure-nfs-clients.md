---
title: Настройка клиента NFS для Azure NetApp Files | Документация Майкрософт
description: Описание настройки клиентов NFS для использования с Azure NetApp Files.
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
ms.date: 08/19/2020
ms.author: b-juche
ms.openlocfilehash: 4a54858f4a09cd62bb555a6df2c12efa2290dd69
ms.sourcegitcommit: 271601d3eeeb9422e36353d32d57bd6e331f4d7b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/20/2020
ms.locfileid: "88653177"
---
# <a name="configure-an-nfs-client-for-azure-netapp-files"></a>Настройка клиента NFS для Azure NetApp Files

Для использования с Azure NetApp Files доступен широкий спектр дистрибутивов Linux. В этой статье описываются конфигурации для двух наиболее часто используемых сред: RHEL 8 и Ubuntu 18,04. Конфигурация клиента NFS, описанная в этой статье, является частью программы установки при [настройке шифрования нфсв 4.1 Kerberos](configure-kerberos-encryption.md) или [создании тома с двумя протоколами](create-volumes-dual-protocol.md).  

Независимо от используемой версии Linux требуются следующие конфигурации:
* Настройте NTP-клиент, чтобы избежать проблем с отклонением во времени.
* Настройка записей DNS клиента Linux для разрешения имен.  
    Эта конфигурация включает запись "A" (вперед) и запись PTR (обратная). 
* Для присоединение к домену создайте учетную запись компьютера в целевом Active Directory (которая создается во время выполнения команды JOIN в сфере). 
    > [!NOTE] 
    > `$SERVICEACCOUNT`Переменная, используемая в приведенных ниже командах, должна быть учетной записью пользователя с разрешениями или делегированием для создания учетной записи компьютера в целевом подразделении.
* Разрешить клиенту подключать тома NFS и другие соответствующие средства мониторинга.

## <a name="rhel-8-configuration"></a>Конфигурация RHEL 8 

1. Установить пакеты:   
    `sudo yum -y install realmd sssd adcli samba-common krb5-workstation chrony`

2. Настройте NTP-клиент:  
    `chrony`По умолчанию используется RHEL 8.  Следуя рекомендациям по настройке в [чрони Suite, настройте NTP](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html/configuring_basic_system_settings/using-chrony-to-configure-ntp).

3. Присоединиться к домену Active Directory:  
    `sudo realm join $DOMAIN.NAME -U $SERVICEACCOUNT --computer-ou= OU=$YOUROU,DC=$DOMAIN,DC=TLD`

## <a name="ubuntu-configuration"></a>Конфигурация Ubuntu 

1. Установить пакеты:  
    `sudo yum -y install realmd packagekit sssd adcli samba-common krb5-workstation chrony`

2. Настройте NTP-клиент.  
    Ubuntu 18,04 использует по `chrony` умолчанию.  Следуя рекомендациям по настройке в [Ubuntu Бионик: использование чрони для настройки NTP](https://ubuntu.com/blog/ubuntu-bionic-using-chrony-to-configure-ntp).

3. Присоединитесь к домен Active Directory:  
    `sudo realm join $DOMAIN.NAME -U $SERVICEACCOUNT --computer-ou= OU=$YOUROU,DC=$DOMAIN,DC=TLD`

## <a name="next-steps"></a>Дальнейшие действия  

* [Создание тома NFS для Azure NetApp Files](azure-netapp-files-create-volumes.md)
* [Создание тома с двумя протоколами для Azure NetApp Files](create-volumes-dual-protocol.md)

