---
title: Часто задаваемые вопросы о файлах NetApp Azure (ru) Документы Майкрософт
description: Ответы часто задаваемые вопросы о Файлах NetApp Azure.
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
ms.date: 04/01/2020
ms.author: b-juche
ms.openlocfilehash: 59453dbb9617f5a2ddbdca08709747b11a688560
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/02/2020
ms.locfileid: "80547088"
---
# <a name="faqs-about-azure-netapp-files"></a>Задаваемые вопросы о файлах NetApp Azure

В этой статье часто задаются вопросы (часто задаваемые вопросы) о файлах NetApp Azure. 

## <a name="networking-faqs"></a>Задаваемые вопросы о сети

### <a name="does-the-nfs-data-path-go-over-the-internet"></a>Проходит ли путь данных NFS через Интернет?  

Нет. Путь данных NFS не проходит через Интернет. Azure NetApp Files — это назаленная служба Azure, развернутая в виртуальной сети Azure (VNet), где эта служба доступна. Azure NetApp Files использует делегированную подсеть и предоставляет сетевой интерфейс непосредственно на VNet. 

Ознакомиться с инструкциями по [планированию сети Azure NetApp Files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-network-topologies) можно.  

### <a name="can-i-connect-a-vnet-that-i-already-created-to-the-azure-netapp-files-service"></a>Могу ли я подключить VNet, который я уже создал, к службе файлов NetApp Azure?

Да, вы можете подключить созданные vNets к службе. 

Ознакомиться с инструкциями по [планированию сети Azure NetApp Files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-network-topologies) можно.  

### <a name="can-i-mount-an-nfs-volume-of-azure-netapp-files-using-dns-fqdn-name"></a>Могу ли я установить объем NFS файлов NetApp Azure с использованием имени DNS F-DN?

Да, вы можете, если вы создаете необходимые записи DNS. Azure NetApp Files поставляет IP-адрес службы для подготовленного объема. 

> [!NOTE] 
> Файлы Azure NetApp могут развертывать дополнительные I-файлы для службы по мере необходимости.  Записи DNS, возможно, должны периодически обновляться.

## <a name="security-faqs"></a>Часто задаваемые вопросы о безопасности

### <a name="can-the-network-traffic-between-the-azure-vm-and-the-storage-be-encrypted"></a>Можно ли шифровать сетевой трафик между Azure VM и хранилищем?

Трафик данных (трафик от NFSv3, NFSv4.1 или SMBv3 клиента до объемов Файлов Сети) не зашифрован. Однако трафик с Azure VM (работает с nFS или SMB-клиентом) в Файлы NetApp Azure так же безопасен, как и любой другой трафик Azure-VM-to-VM. Этот трафик является локальным для сети центров обработки данных Azure. 

### <a name="can-the-storage-be-encrypted-at-rest"></a>Можно ли шифровать хранилище в состоянии покоя?

Все объемы файлов Azure NetApp шифруются по стандарту FIPS 140-2. Все ключи управляются службой Файлов NetApp Azure. 

### <a name="how-are-encryption-keys-managed"></a>Как управляется ключи шифрования? 

Служба обрабатывает ключевое управление файлами NetApp Azure. Для каждого тома создается уникальный ключ шифрования данных XTS-AES-256. Иерархия ключей шифрования используется для шифрования и защиты всех ключей громкости. Эти ключи шифрования никогда не отображаются и не сообщаются в незашифрованном формате. Ключи шифрования удаляются сразу же при удалении громкости.

Поддержка управляемых пользователем ключей (Bring Your Own Keys) с помощью Azure Dedicated HSM доступна на контролируемой основе в регионах Востоксша, Запад США 2 и США на юге.  Вы можете запросить доступ по адресу **anffeedback@microsoft.com**. По мере наличия емкости запросы будут утверждены.

### <a name="can-i-configure-the-nfs-export-policy-rules-to-control-access-to-the-azure-netapp-files-service-mount-target"></a>Могу ли я настроить правила экспортной политики NFS для контроля доступа к целевому показателю установки файлов NetApp Azure?


Да, вы можете настроить до пяти правил в одной экспортной политике NFS.

### <a name="does-azure-netapp-files-support-network-security-groups"></a>Поддерживают ли файлы Сети файлы Azure NetApp?

Нет, в настоящее время вы не можете применять группы сетевой безопасности к делегированной подсети файлов NetApp Azure или сетевым интерфейсам, созданным службой.

### <a name="can-i-use-azure-iam-with-azure-netapp-files"></a>Можно ли использовать Azure IAM с файлами NetApp Azure?

Да, Azure NetApp Files поддерживает функции RBAC с Azure IAM.

## <a name="performance-faqs"></a>Часто задаваемые вопросы производительности

### <a name="what-should-i-do-to-optimize-or-tune-azure-netapp-files-performance"></a>Что я должен сделать, чтобы оптимизировать или настроить производительность файлов NetApp Azure?

Вы можете предпринять следующие действия в соответствии с требованиями к производительности: 
- Убедитесь, что виртуальная машина имеет соответствующий размер.
- Включить ускоренную сеть для VM.
- Выберите желаемый уровень обслуживания и размер для пула емкости.
- Создайте объем с желаемым размером квоты для емкости и производительности.

### <a name="how-do-i-convert-throughput-based-service-levels-of-azure-netapp-files-to-iops"></a>Как преобразовать уровень пропускной связи файлов Azure NetApp в IOPS?

Вы можете преобразовать MB/s в IOPS, используя следующую формулу:  

`IOPS = (MBps Throughput / KB per IO) * 1024`

### <a name="how-do-i-change-the-service-level-of-a-volume"></a>Как изменить уровень обслуживания тома?

Изменение уровня обслуживания объема в настоящее время не поддерживается.

### <a name="how-do-i-monitor-azure-netapp-files-performance"></a>Как контролировать производительность файлов Сети Azure NetApp?

Файлы Azure NetApp предоставляют показатели производительности громкости. Вы также можете использовать Azure Monitor для мониторинга метрик использования файлов Azure NetApp.  Ознакомиться с материалами для файлов NetApp Azure можно по смотреть в [метрики](azure-netapp-files-metrics.md) производительности для файлов Azure NetApp.

## <a name="nfs-faqs"></a>Часто задаваемые вопросы NFS

### <a name="i-want-to-have-a-volume-mounted-automatically-when-an-azure-vm-is-started-or-rebooted--how-do-i-configure-my-host-for-persistent-nfs-volumes"></a>Я хочу автоматически установить громкость при начале или перезагрузке VM Azure.  Как настроить узла для постоянных объемов NFS?

Для автоматического монтажа тома NFS при запуске или `/etc/fstab` перезагрузке VM добавьте запись в файл на универсале. 

Смотрите [Маунт или открепить громкость для виртуальных машин Windows или Linux](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md) для получения подробной информации.  

### <a name="why-does-the-df-command-on-nfs-client-not-show-the-provisioned-volume-size"></a>Почему команда DF в клиенте NFS не показывает размер объема?

Размер объема, зарегистрированный в DF, — это максимальный размер, до которых может увеличиться объем файлов NetApp Azure. Размер объема файлов NetApp Azure в команде DF не отражает квоту или размер объема.  Вы можете получить объем или квоту файлов Azure NetApp через портал Azure или API.

### <a name="what-nfs-version-does-azure-netapp-files-support"></a>Какую версию NFS поддерживает Файлы NetApp Azure?

Файлы Azure NetApp поддерживают NFSv3 и NFSv4.1. Вы можете [создать том,](azure-netapp-files-create-volumes.md) используя обе версии NFS. 

### <a name="how-do-i-enable-root-squashing"></a>Как включить сквошинг корневой корня?

Корневая сквошинг в настоящее время не поддерживается.

## <a name="smb-faqs"></a>Часто задаваемые вопросы с мсм

### <a name="is-an-active-directory-connection-required-for-smb-access"></a>Требуется ли для доступа к SMB подключение active Directory? 

Да, перед развертыванием объема SMB необходимо создать соединение Active Directory. Указанные контроллеры доменов должны быть доступны в делегированной подсети файлов Azure NetApp для успешного подключения.  Подробнее о [создании объема SMB.](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes-smb) 

### <a name="how-many-active-directory-connections-are-supported"></a>Сколько подключений Active Directory поддерживается?

Файлы Azure NetApp не поддерживают несколько подключений Active Directory (AD) в одном *регионе,* даже если подключения AD находятся в разных учетных записях NetApp. Тем не менее, вы можете иметь несколько подключения AD в одной *подписке,* до тех пор, как соединения AD находятся в разных регионах. Если вам требуется несколько подключения AD в одном регионе, для этого можно использовать отдельные подписки. 

Подключение AD настроено на учетную запись NetApp; подключение AD отображается только через созданную учетную запись NetApp.

### <a name="does-azure-netapp-files-support-azure-active-directory"></a>Поддерживают ли файлы Azure NetApp Active Directory? 

Поддерживаются как [службы домена Azure Active Directory (AD),](https://docs.microsoft.com/azure/active-directory-domain-services/overview) так и [службы домена Active Directory Domain (AD DS).](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview) Вы можете использовать существующие контроллеры доменов Active Directory с помощью файлов Azure NetApp. Контроллеры домена могут распорядиться в Azure как виртуальные машины, так и в помещениях через ExpressRoute или S2S VPN. В настоящее время Файлы Azure NetApp не поддерживают присоединение AD к [active Directory Azure.](https://azure.microsoft.com/resources/videos/azure-active-directory-overview/)

Если вы используете файлы Azure NetApp с помощью служб `OU=AADDC Computers` домена Active Directory, путь организационного подразделения — при настройке Active Directory для учетной записи NetApp.

### <a name="what-versions-of-windows-server-active-directory-are-supported"></a>Какие версии активного каталога Windows Server поддерживаются?

Azure NetApp Files поддерживает Windows Server 2008r2SP1-2019 версии служб домена Active Directory.

### <a name="why-does-the-available-space-on-my-smb-client-not-show-the-provisioned-size"></a>Почему доступное пространство в клиенте SMB не показывает приготовивого размера?

Размер объема, о которых сообщает клиент SMB, — это максимальный размер, до которым может вырасти объем файлов NetApp Azure. Размер объема файлов Azure NetApp, показанный на клиенте SMB, не отражает квоту или размер объема. Вы можете получить объем или квоту файлов Azure NetApp через портал Azure или API.

### <a name="does-azure-netapp-files-support-kerberos-encryption"></a>Поддерживают ли файлы Сети Приложения Azure шифрование Kerberos?

Да, по умолчанию, Azure NetApp Files поддерживает шифрование AES-128 и AES-256 для трафика между службой и целевыми контроллерами доменов Active Directory. Смотрите [Создать объем SMB для файлов Сети Azure](azure-netapp-files-create-volumes-smb.md) для требований. 

<!--
### Does Azure NetApp Files support LDAP signing? 

Yes, Azure NetApp Files supports LDAP signing by default. This functionality enables secure LDAP lookups between the Azure NetApp Files service and the user-specified [Active Directory Domain Services domain controllers](https://docs.microsoft.com/windows/win32/ad/active-directory-domain-services). For more information, see [ADV190023 | Microsoft Guidance for Enabling LDAP Channel Binding and LDAP Signing](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/ADV190023).
--> 

## <a name="capacity-management-faqs"></a>Часто задаваемые вопросы управления потенциалом

### <a name="how-do-i-monitor-usage-for-capacity-pool-and-volume-of-azure-netapp-files"></a>Как контролировать использование пула емкости и объема файлов Azure NetApp? 

Файлы Azure NetApp предоставляют показатели пула емкости и объема. Вы также можете использовать Azure Monitor для мониторинга использования файлов NetApp Azure. Подробности смотрите в материалах для [файлов Azure NetApp.](azure-netapp-files-metrics.md) 

### <a name="can-i-manage-azure-netapp-files-through-azure-storage-explorer"></a>Можно ли управлять файлами NetApp Azure через Azure Storage Explorer?

Нет. Файлы Azure NetApp не поддерживаются Azure Storage Explorer.

### <a name="how-do-i-determine-if-a-directory-is-approaching-the-limit-size"></a>Как определить, приближается ли каталог к предельного размера?

Вы можете `stat` использовать команду клиента, чтобы увидеть, приближается ли каталог к пределу максимального размера (320 МБ).

Для каталога 320 МБ количество блоков составляет 655360, при этом размер каждого блока составляет 512 байтов.  (То есть, 320x1024x1024/512.)  

Примеры:

    [makam@cycrh6rtp07 ~]$ stat bin
    File: 'bin'
    Size: 4096            Blocks: 8          IO Block: 65536  directory

    [makam@cycrh6rtp07 ~]$ stat tmp
    File: 'tmp'
    Size: 12288           Blocks: 24         IO Block: 65536  directory
 
    [makam@cycrh6rtp07 ~]$ stat tmp1
    File: 'tmp1'
    Size: 4096            Blocks: 8          IO Block: 65536  directory

## <a name="data-migration-and-protection-faqs"></a>Миграция данных и защита часто задаваемых вопросов

### <a name="how-do-i-migrate-data-to-azure-netapp-files"></a>Как перенести данные в файлы NetApp Azure?
Файлы Azure NetApp предоставляют объемы NFS и SMB.  Для переноса данных в службу можно использовать любой файловый инструмент копирования. 

NetApp предлагает решение на основе SaaS, [NetApp Cloud Sync](https://cloud.netapp.com/cloud-sync-service).  Решение позволяет реплицировать данные NFS или SMB на экспорт NFS-файлов Azure NetApp или акции SMB. 

Вы также можете использовать широкий спектр бесплатных инструментов для копирования данных. Для NFS можно использовать инструменты рабочих нагрузок, такие как [rsync,](https://rsync.samba.org/examples.html) для копирования и синхронизации исходных данных в объем файлов NetApp Azure. Для малого и среднего бизнеса можно использовать [робокопию](https://docs.microsoft.com/windows-server/administration/windows-commands/robocopy) рабочих нагрузок таким же образом.  Эти инструменты также могут реплицировать разрешения файлов или папок. 

Требования к миграции данных из файлов Azure NetApp следующие: 

- Убедитесь, что файлы Azure NetApp доступны в целевом регионе Azure.
- Проверка подключения к сети между источником и ip-адресом целевого объема Файлов Сети NetApp. Передача данных между помещениями и сервисом Azure NetApp Files поддерживается через ExpressRoute.
- Создайте целевой объем файлов NetApp Azure.
- Перенесите исходные данные в целевой объем с помощью предпочтительного инструмента копирования файлов.

### <a name="how-do-i-create-a-copy-of-an-azure-netapp-files-volume-in-another-azure-region"></a>Как создать копию объема файлов NetApp Azure в другом регионе Azure?
    
Файлы Azure NetApp предоставляют объемы NFS и SMB.  Любой инструмент копирования файлов может использоваться для репликации данных между регионами Azure. 

NetApp предлагает решение на базе SaaS, [NetApp Cloud Sync](https://cloud.netapp.com/cloud-sync-service).  Решение позволяет реплицировать данные NFS или SMB на экспорт NFS-файлов Azure NetApp или акции SMB. 

Вы также можете использовать широкий спектр бесплатных инструментов для копирования данных. Для NFS можно использовать инструменты рабочих нагрузок, такие как [rsync,](https://rsync.samba.org/examples.html) для копирования и синхронизации исходных данных в объем файлов NetApp Azure. Для малого и среднего бизнеса можно использовать [робокопию](https://docs.microsoft.com/windows-server/administration/windows-commands/robocopy) рабочих нагрузок таким же образом.  Эти инструменты также могут реплицировать разрешения файлов или папок. 

Требования к репликации объема файлов NetApp Azure в другой регион Azure следующие: 
- Убедитесь, что файлы Azure NetApp доступны в целевом регионе Azure.
- Проверка подключения к сети между VNets в каждом регионе. В настоящее время глобальное пиринг между VNets не поддерживается.  Вы можете установить связь между VNets, связавшись с схемой ExpressRoute или используя соединение S2S VPN. 
- Создайте целевой объем файлов NetApp Azure.
- Перенесите исходные данные в целевой объем с помощью предпочтительного инструмента копирования файлов.

### <a name="is-migration-with-azure-data-box-supported"></a>Поддерживается ли миграция с помощью ящика данных Azure?

Нет. В настоящее время Azure Data Box не поддерживает файлы NetApp Azure. 

### <a name="is-migration-with-azure-importexport-service-supported"></a>Поддерживается ли миграция с помощью службы импорта/экспорта Azure?

Нет. Служба импорта/экспорта Azure в настоящее время не поддерживает файлы NetApp Azure.

## <a name="next-steps"></a>Дальнейшие действия  

- [Часто задаваемые вопросы Microsoft Azure ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-faqs)
- [Задаваемые вопросы виртуальной сети Microsoft Azure](https://docs.microsoft.com/azure/virtual-network/virtual-networks-faq)
- [Создание запроса на поддержку Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)
- [Azure Data Box](https://docs.microsoft.com/azure/databox-family/)
- [Часто задаваемые вопросы о производительности SMB для файлов Azure NetApp](azure-netapp-files-smb-performance.md)
