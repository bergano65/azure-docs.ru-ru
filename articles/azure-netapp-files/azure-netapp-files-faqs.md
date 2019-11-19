---
title: Часто задаваемые вопросы о Azure NetApp Files | Документация Майкрософт
description: Ответы на часто задаваемые вопросы о Azure NetApp Files.
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
ms.openlocfilehash: 815ac261a29f710914347443f7882b9fe682229f
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/19/2019
ms.locfileid: "74173603"
---
# <a name="faqs-about-azure-netapp-files"></a>Часто задаваемые вопросы о Azure NetApp Files

В этой статье содержатся ответы на часто задаваемые вопросы о Azure NetApp Files. 

## <a name="networking-faqs"></a>Вопросы и ответы о сети

### <a name="does-the-nfs-data-path-go-over-the-internet"></a>Выполняет ли путь к данным NFS через Интернет?  

Нет Путь к данным NFS не переходит через Интернет. Azure NetApp Files — это собственная служба Azure, которая развертывается в виртуальной сети Azure (VNet), где доступна эта служба. Azure NetApp Files использует делегированную подсеть и подготавливает сетевой интерфейс непосредственно в виртуальной сети. 

Дополнительные сведения см. [в разделе рекомендации по Azure NetApp Files планировании сети](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-network-topologies) .  

### <a name="can-i-connect-a-vnet-that-i-already-created-to-the-azure-netapp-files-service"></a>Можно ли подключить уже созданную виртуальную сеть к службе Azure NetApp Files?

Да, можно подключить виртуальных сетей, созданный вами к службе. 

Дополнительные сведения см. [в разделе рекомендации по Azure NetApp Files планировании сети](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-network-topologies) .  

### <a name="can-i-mount-an-nfs-volume-of-azure-netapp-files-using-dns-fqdn-name"></a>Можно ли подключить том NFS Azure NetApp Files, используя полное доменное имя DNS?

Да, можно, если создать необходимые записи DNS. Azure NetApp Files предоставляет IP-адрес службы для подготовленного тома. 

> [!NOTE] 
> При необходимости Azure NetApp Files может развернуть дополнительные IP-адреса для службы.  Может потребоваться периодически обновлять записи DNS.

## <a name="security-faqs"></a>Часто задаваемые вопросы о безопасности

### <a name="can-the-network-traffic-between-the-azure-vm-and-the-storage-be-encrypted"></a>Может ли шифроваться сетевой трафик между виртуальной машиной Azure и хранилищем?

Трафик данных (трафик от клиента NFSv3, Нфсв 4.1 или SMBv3 к Azure NetApp Filesным томам) не шифруется. Тем не менее трафик от виртуальной машины Azure (с клиентом NFS или SMB) на Azure NetApp Files является безопасным, как и любой другой трафик Azure-ВМ-VM. Этот трафик является локальным для сети центра обработки данных Azure. 

### <a name="can-the-storage-be-encrypted-at-rest"></a>Может ли хранилище быть зашифровано неактивных данных?

Все Azure NetApp Filesные тома шифруются с использованием стандарта FIPS 140-2. Все ключи управляются службой Azure NetApp Files. 

### <a name="how-are-encryption-keys-managed"></a>Как осуществляется управление ключами шифрования? 

Управление ключами для Azure NetApp Files обрабатывается службой. Для каждого тома создается уникальный ключ шифрования данных XTS-AES-256. Иерархия ключей шифрования используется для шифрования и защиты всех ключей томов. Эти ключи шифрования никогда не отображаются или не передаются в виде незашифрованного формата. Ключи шифрования немедленно удаляются при удалении тома.

Сейчас управляемые пользователем ключи (с собственными ключами) не поддерживаются.

### <a name="can-i-configure-the-nfs-export-policy-rules-to-control-access-to-the-azure-netapp-files-service-mount-target"></a>Можно ли настроить правила политики экспорта NFS для контроля доступа к целевому объекту подключения службы Azure NetApp Files?


Да, можно настроить до пяти правил в одной политике экспорта NFS.

### <a name="does-azure-netapp-files-support-network-security-groups"></a>Поддерживает ли Azure NetApp Files группы безопасности сети?

Нет, в настоящее время нельзя применять группы безопасности сети к делегированной подсети Azure NetApp Files или к сетевым интерфейсам, созданным службой.

### <a name="can-i-use-azure-iam-with-azure-netapp-files"></a>Можно ли использовать Azure IAM с Azure NetApp Files?

Да, Azure NetApp Files поддерживает функции RBAC с помощью Azure IAM.

## <a name="performance-faqs"></a>Вопросы и ответы по производительности

### <a name="what-should-i-do-to-optimize-or-tune-azure-netapp-files-performance"></a>Что делать, чтобы оптимизировать или настроить производительность Azure NetApp Files?

В соответствии с требованиями к производительности можно выполнять следующие действия. 
- Убедитесь, что размер виртуальной машины соответствует требованиям.
- Включите ускоренную сеть для виртуальной машины.
- Выберите требуемый уровень обслуживания и размер для пула емкости.
- Создайте том с требуемым размером квоты для емкости и производительности.

### <a name="how-do-i-convert-throughput-based-service-levels-of-azure-netapp-files-to-iops"></a>Разделы справки преобразовывать уровни обслуживания на основе пропускной способности Azure NetApp Files в операции ввода-вывода?

Можно преобразовать МБ/с в операции ввода-вывода с помощью следующей формулы:  

`IOPS = (MBps Throughput / KB per IO) * 1024`

### <a name="how-do-i-change-the-service-level-of-a-volume"></a>Разделы справки изменить уровень обслуживания тома?

Изменение уровня обслуживания тома в настоящее время не поддерживается.

### <a name="how-do-i-monitor-azure-netapp-files-performance"></a>Разделы справки монитор Azure NetApp Files производительность?

Azure NetApp Files предоставляет метрики производительности тома. Можно также использовать Azure Monitor для мониторинга метрик использования для Azure NetApp Files.  Список метрик производительности для Azure NetApp Files см. в разделе [метрики для Azure NetApp Files](azure-netapp-files-metrics.md) .

## <a name="nfs-faqs"></a>Вопросы и ответы по NFS

### <a name="i-want-to-have-a-volume-mounted-automatically-when-an-azure-vm-is-started-or-rebooted--how-do-i-configure-my-host-for-persistent-nfs-volumes"></a>Я хочу автоматически подключить том при запуске или перезагрузке виртуальной машины Azure.  Разделы справки настроить узел для постоянных томов NFS?

Чтобы том NFS автоматически подключаться при запуске или перезагрузке виртуальной машины, добавьте запись в файл `/etc/fstab` на узле. 

Дополнительные сведения см. [в статье подключение или отключение тома для виртуальных машин Windows или Linux](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md) .  

### <a name="why-does-the-df-command-on-nfs-client-not-show-the-provisioned-volume-size"></a>Почему команда DF на клиенте NFS не показывает подготовленный размер тома?

Размер тома, сообщаемый в DF, — это максимальный размер, который может увеличить Azure NetApp Files том. Размер Azure NetApp Filesого тома в команде DF не является отражающим квоту или размер тома.  Вы можете получить Azure NetApp Files размер или квоту тома с помощью портал Azure или API.

### <a name="what-nfs-version-does-azure-netapp-files-support"></a>Какая версия NFS поддерживает Azure NetApp Files?

Azure NetApp Files поддерживает NFSv3 и Нфсв 4.1. Вы можете создать том с помощью версии NFS. 

> [!IMPORTANT] 
> Для доступа к функции NFS версии 4.1 требуется внесение в список разрешений.  Чтобы запросить внесение в список разрешений, отправьте запрос по адресу <anffeedback@microsoft.com>. 


### <a name="how-do-i-enable-root-squashing"></a>Разделы справки включить корневую использование параметра Squash?

Корневой использование параметра squash в настоящее время не поддерживается.

## <a name="smb-faqs"></a>Часто задаваемые вопросы по SMB

### <a name="is-an-active-directory-connection-required-for-smb-access"></a>Требуется Active Directoryое подключение для доступа по протоколу SMB? 

Да, перед развертыванием тома SMB необходимо создать подключение Active Directory. Для успешного подключения указанные контроллеры домена должны быть доступны для делегированной подсети Azure NetApp Files.  Дополнительные сведения см. [в разделе Создание тома SMB](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes-smb) . 

### <a name="how-many-active-directory-connections-are-supported"></a>Сколько Active Directory подключений поддерживается?

В настоящее время Azure NetApp Files поддерживает одно Active Directoryное подключение на подписку. Кроме того, Active Directory подключение относится только к одной учетной записи NetApp; Он не используется совместно несколькими учетными записями. 

### <a name="does-azure-netapp-files-support-azure-active-directory"></a>Поддерживает ли Azure NetApp Files Azure Active Directory? 

Поддерживаются [службы доменов Azure Active Directory (AD)](https://docs.microsoft.com/azure/active-directory-domain-services/overview) и [домен Active Directory Services (AD DS)](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview) . С Azure NetApp Files можно использовать существующие Active Directory контроллеры домена. Контроллеры домена могут размещаться в Azure как виртуальные машины или локально с помощью ExpressRoute или S2S VPN. В настоящее время Azure NetApp Files не поддерживает присоединение к AD для [Azure Active Directory](https://azure.microsoft.com/resources/videos/azure-active-directory-overview/) .

При использовании Azure NetApp Files с доменными службами Azure Active Directory путь подразделения `OU=AADDC Computers` при настройке Active Directory для учетной записи NetApp.

### <a name="what-versions-of-windows-server-active-directory-are-supported"></a>Какие версии Windows Server Active Directory поддерживаются?

Azure NetApp Files поддерживает версии домен Active Directory служб Windows Server 2008r2SP1-2019.

## <a name="capacity-management-faqs"></a>Вопросы и ответы по управлению емкостью

### <a name="how-do-i-monitor-usage-for-capacity-pool-and-volume-of-azure-netapp-files"></a>Разделы справки отслеживать использование пула емкости и объема Azure NetApp Files? 

Azure NetApp Files предоставляет метрики емкости пула и использования томов. Можно также использовать Azure Monitor для мониторинга использования Azure NetApp Files. Дополнительные сведения см. в разделе [метрики для Azure NetApp Files](azure-netapp-files-metrics.md) . 

### <a name="can-i-manage-azure-netapp-files-through-azure-storage-explorer"></a>Можно ли управлять Azure NetApp Files с помощью Обозреватель службы хранилища Azure?

Нет Azure NetApp Files не поддерживается Обозреватель службы хранилища Azure.

## <a name="data-migration-and-protection-faqs"></a>Часто задаваемые вопросы по переносу и защите данных

### <a name="how-do-i-migrate-data-to-azure-netapp-files"></a>Разделы справки перенести данные в Azure NetApp Files?
Azure NetApp Files предоставляет тома NFS и SMB.  Для переноса данных в службу можно использовать любое средство копирования на основе файлов. 

NetApp предлагает решение на основе SaaS, [NetApp облачную синхронизацию](https://cloud.netapp.com/cloud-sync-service).  Решение позволяет реплицировать данные NFS или SMB в Azure NetApp Files экспорта NFS или общих ресурсах SMB. 

Можно также использовать широкий набор бесплатных средств для копирования данных. Для NFS можно использовать средства рабочих нагрузок, такие как [rsync](https://rsync.samba.org/examples.html) , для копирования и синхронизации исходных данных в том Azure NetApp Files. Для SMB можно использовать рабочие нагрузки с помощью средства [Robocopy](https://docs.microsoft.com/windows-server/administration/windows-commands/robocopy) аналогичным образом.  Эти средства также могут выполнять репликацию разрешений для файлов и папок. 

Ниже приведены требования к переносу данных из локальной среды в Azure NetApp Files. 

- Убедитесь, что Azure NetApp Files доступен в целевом регионе Azure.
- Проверьте сетевое подключение между исходным и Azure NetApp Files целевым IP-адресом тома. Обмен данными между локальным и Azure NetApp Files службой поддерживается через ExpressRoute.
- Создайте целевой Azure NetApp Files том.
- Перенесите исходные данные на целевой том с помощью предпочтительного средства копирования файлов.

### <a name="how-do-i-create-a-copy-of-an-azure-netapp-files-volume-in-another-azure-region"></a>Разделы справки создать копию тома Azure NetApp Files в другом регионе Azure?
    
Azure NetApp Files предоставляет тома NFS и SMB.  Любое средство копирования на основе файлов можно использовать для репликации данных между регионами Azure. 

NetApp предлагает решение на основе SaaS, [NetApp облачную синхронизацию](https://cloud.netapp.com/cloud-sync-service).  Решение позволяет реплицировать данные NFS или SMB в Azure NetApp Files экспорта NFS или общих ресурсах SMB. 

Можно также использовать широкий набор бесплатных средств для копирования данных. Для NFS можно использовать средства рабочих нагрузок, такие как [rsync](https://rsync.samba.org/examples.html) , для копирования и синхронизации исходных данных в том Azure NetApp Files. Для SMB можно использовать рабочие нагрузки с помощью средства [Robocopy](https://docs.microsoft.com/windows-server/administration/windows-commands/robocopy) аналогичным образом.  Эти средства также могут выполнять репликацию разрешений для файлов и папок. 

Ниже приведены требования к репликации Azure NetApp Files тома в другой регион Azure. 
- Убедитесь, что Azure NetApp Files доступен в целевом регионе Azure.
- Проверьте сетевое подключение между виртуальных сетей в каждом регионе. В настоящее время глобальный пиринг между виртуальных сетей не поддерживается.  Вы можете установить подключение между виртуальных сетей, связав его с каналом ExpressRoute или с помощью VPN-подключения S2S. 
- Создайте целевой Azure NetApp Files том.
- Перенесите исходные данные на целевой том с помощью предпочтительного средства копирования файлов.

### <a name="is-migration-with-azure-data-box-supported"></a>Поддерживается ли миграция с Azure Data Box?

Нет Azure Data Box не поддерживает Azure NetApp Files в данный момент. 

### <a name="is-migration-with-azure-importexport-service-supported"></a>Поддерживается ли миграция с поддержкой службы импорта и экспорта Azure?

Нет Служба импорта и экспорта Azure не поддерживает Azure NetApp Files в настоящее время.

## <a name="next-steps"></a>Дополнительная информация  

- [Microsoft Azure ExpressRoute часто задаваемые вопросы](https://docs.microsoft.com/azure/expressroute/expressroute-faqs)
- [Вопросы и ответы по виртуальная сеть Microsoft Azure](https://docs.microsoft.com/azure/virtual-network/virtual-networks-faq)
- [Создание запроса на поддержку Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)
- [Azure Data Box](https://docs.microsoft.com/azure/databox-family/)
