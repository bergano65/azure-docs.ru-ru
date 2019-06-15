---
title: Часто задаваемые вопросы о файлах Azure NetApp | Документация Майкрософт
description: Ответы на часто задаваемые вопросы о службе файлов Azure NetApp.
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
ms.date: 05/14/2019
ms.author: b-juche
ms.openlocfilehash: 6f1ca3398678b59a81e5c22b51b36a1f5505d4c2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65806391"
---
# <a name="faqs-about-azure-netapp-files"></a>Часто задаваемые вопросы о файлах Azure NetApp

В этой статье содержатся ответы на часто задаваемые вопросы (FAQ) о службе файлов Azure NetApp. 

## <a name="networking-faqs"></a>Часто задаваемые вопросы о сети

### <a name="does-the-nfs-data-path-go-over-the-internet"></a>Путь к данным NFS, проходит через Интернет?  

№ Путь к данным NFS не будет проходить через Интернет. Служба файлов Azure NetApp — это служба Azure собственный, которая развертывается в виртуальной сети Azure (VNet) где доступна эта служба. Служба файлов Azure NetApp используется делегированного подсеть и подготовка сетевой интерфейс непосредственно в виртуальной сети. 

См. в разделе [сети NetApp службы файлов Azure, касающиеся планирования](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-network-topologies) подробные сведения.  

### <a name="can-i-connect-a-vnet-that-i-already-created-to-the-azure-netapp-files-service"></a>Можно подключиться к виртуальной сети, уже создан для службы файлов Azure NetApp?

Да, можно подключить виртуальные сети, созданной в службу. 

См. в разделе [сети NetApp службы файлов Azure, касающиеся планирования](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-network-topologies) подробные сведения.  

### <a name="can-i-mount-an-nfs-volume-of-azure-netapp-files-using-dns-fqdn-name"></a>Можно ли подключить том NFS NetApp файлов Azure, используя полное доменное имя DNS-имя

Да, можно, если создать необходимые записи DNS. Служба файлов Azure NetApp предоставляет IP-адрес службы для подготовленного тома. 

> [!NOTE] 
> Служба файлов Azure NetApp можно развернуть дополнительные IP-адреса для службы, при необходимости.  Записи DNS может потребоваться периодически обновляются.

## <a name="security-faqs"></a>Часто задаваемые вопросы о безопасности

### <a name="can-the-network-traffic-between-the-azure-vm-and-the-storage-be-encrypted"></a>Могут быть зашифрованы сетевой трафик между виртуальной Машиной Azure и хранилище?

Трафик данных (тома файлов Azure NetApp трафик от клиента NFSv3 или SMBv3), не шифруются. Тем не менее трафик от виртуальной Машины Azure (под управлением клиентом SMB или NFS) к службе файлов Azure NetApp так безопасен, как любой другой входящий трафик виртуальной Машины Azure на Машинах. Этот трафик локальной сети центра обработки данных Azure. 

### <a name="can-the-storage-be-encrypted-at-rest"></a>Хранилища могут быть зашифрованы при хранении?

Все тома службы файлов Azure NetApp, шифруются с использованием FIPS 140-2 стандарта. Все ключи управляются службой файлов Azure NetApp. 

### <a name="how-are-encryption-keys-managed"></a>Об управлении ключами шифрования? 

Управление ключами для службы файлов Azure NetApp обрабатывается службой.  В настоящее время не поддерживаются управляемых пользователем ключей (перевести свой собственный ключи).

### <a name="can-i-configure-the-nfs-export-policy-rules-to-control-access-to-the-azure-netapp-files-service-mount-target"></a>Можно настроить правила политики экспорта NFS для управления доступом к целевому объекту подключения службы файлов Azure NetApp?


Да, можно настроить до пяти правил в одну политику экспорта NFS.

### <a name="does-azure-netapp-files-support-network-security-groups"></a>Группы безопасности сети поддерживаются NetApp службой файлов Azure?

Нет, сейчас вы не может применить группы безопасности сети для делегированного подсети Azure NetApp файлов или сетевые интерфейсы, созданные службой.

### <a name="can-i-use-azure-iam-with-azure-netapp-files"></a>Можно ли использовать Azure IAM с NetApp службы файлов Azure?

Да, служба файлов Azure NetApp поддерживает функции RBAC Azure IAM.

## <a name="performance-faqs"></a>Часто задаваемые вопросы о производительности

### <a name="what-should-i-do-to-optimize-or-tune-azure-netapp-files-performance"></a>Что следует сделать для оптимизации или настройки производительности службы файлов Azure NetApp?

Можно выполнить следующие действия в соответствии с требованиями производительности: 
- Убедитесь, что виртуальная машина имеет размер соответствующим образом.
- Включите функцию ускорения сети для виртуальной Машины.
- Выберите нужную службу уровень и размер емкости пула.
- Создайте том с размером требуемой квоты для емкости и производительности.

### <a name="how-do-i-convert-throughput-based-service-levels-of-azure-netapp-files-to-iops"></a>Как преобразовать уровни служб на основе пропускной способности Azure NetApp файлов операций ввода-ВЫВОДА?

МБ в секунду для операций ввода-ВЫВОДА можно преобразовать с помощью следующей формулы:  

`IOPS = (MBps Throughput / KB per IO) * 1024`

### <a name="how-do-i-change-the-service-level-of-a-volume"></a>Как изменить уровень службы тома?

Изменение уровня обслуживания тома не поддерживается.

### <a name="how-do-i-monitor-azure-netapp-files-performance"></a>Как отслеживать производительность NetApp файлов Azure?

Служба файлов Azure NetApp предоставляет метрики производительности тома. Также можно использовать Azure Monitor для мониторинга показателей использования для службы файлов Azure NetApp.  См. в разделе [метрики для службы файлов Azure NetApp](azure-netapp-files-metrics.md) список метрик производительности для службы файлов Azure NetApp.

## <a name="nfs-faqs"></a>Часто задаваемые вопросы о NFS

### <a name="i-want-to-have-a-volume-mounted-automatically-when-an-azure-vm-is-started-or-rebooted--how-do-i-configure-my-host-for-persistent-nfs-volumes"></a>Я хочу иметь том монтируется автоматически при запуске или перезагрузки виртуальной Машины Azure.  Как настроить компьютер для постоянных томах NFS?

Для тома NFS автоматически подключится во VM start или перезагрузки, добавьте запись для `/etc/fstab` файла на узле. 

Например: `$ANFIP:/$FILEPATH      /$MOUNTPOINT    nfs bg,rw,hard,noatime,nolock,rsize=65536,wsize=65536,vers=3,tcp,_netdev 0 0`

- $ANFIP  
    IP-адрес тома службы файлов Azure NetApp, найти в колонке свойств тома
- $FILEPATH  
    Путь экспорта тома службы файлов Azure NetApp
- $MOUNTPOINT  
    Папка, созданная на узле Linux, используемый для подключения экспорта NFS

### <a name="why-does-the-df-command-on-nfs-client-not-show-the-provisioned-volume-size"></a>Почему команда DF на NFS-клиента не содержит размера подготовленного тома?

Указанный размер тома в DF находится тома службы файлов Azure NetApp могут увеличиваться до размера. Размер тома службы файлов Azure NetApp в команде DF не отражают общие квоты или размер тома.  Размер тома службы файлов Azure NetApp или квоты можно получить через портал Azure или API.

### <a name="what-nfs-version-does-azure-netapp-files-support"></a>Какая версия NFS поддерживает NetApp службы файлов Azure?

Служба файлов Azure NetApp в настоящее время поддерживает NFSv3.

### <a name="how-do-i-enable-root-squashing"></a>Как включить сжимания корневого?

Корневой сжимания сейчас не поддерживается.

## <a name="smb-faqs"></a>Часто задаваемые вопросы о SMB

### <a name="does-azure-netapp-files-support-azure-active-directory"></a>Azure Active Directory поддерживаются службой файлов Azure NetApp?

Нет, он поддерживается в настоящее время не.  Служба файлов Azure NetApp поддерживает доменных служб Active Directory (принеси на своих AD), который можно использовать существующие контроллеры домена Active Directory с файлами Azure NetApp. Контроллеры домена могут находиться в Azure, как виртуальные машины или в локальной среде через ExpressRoute.

### <a name="is-an-active-directory-connection-required-for-smb-access"></a>Такое подключение к Active Directory, необходимые для доступа по SMB? 

Да, необходимо создать подключение к Active Directory перед развертыванием на диск SMB. Указанный контроллеры домена должны быть доступны делегированного подсети NetApp службы файлов Azure для успешного подключения.  См. в разделе [Создание тома SMB](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes#create-an-smb-volume) подробные сведения. 

### <a name="how-many-active-directory-connections-are-supported"></a>Количество подключений Active Directory поддерживаются?

Служба файлов Azure NetApp в настоящее время поддерживает одно подключение Active Directory для каждой подписки. Кроме того подключения Active Directory относится к одной учетной записи NetApp; он не является общим для учетных записей. 

### <a name="what-versions-of-windows-ad-are-supported"></a>Какие версии Windows AD поддерживаются?

Служба файлов Azure NetApp поддерживает версии Windows Server 2008r2SP1 2016 доменных служб Active Directory.

## <a name="capacity-management-faqs"></a>Управление емкостью часто задаваемые вопросы

### <a name="how-do-i-monitor-usage-for-capacity-pool-and-volume-of-azure-netapp-files"></a>Как отслеживать использование емкости пула и томов файлов NetApp Azure? 

Служба файлов Azure NetApp предоставляет метрики использования емкости пула и томов. Можно также использовать Azure Monitor для наблюдения за использованием файлов NetApp Azure. См. в разделе [метрики для службы файлов Azure NetApp](azure-netapp-files-metrics.md) сведения. 

### <a name="can-i-manage-azure-netapp-files-through-azure-storage-explorer"></a>Можно ли управлять Azure NetApp файлов с помощью обозревателя службы хранилища Azure?

№ Обозреватель службы хранилища Azure не поддерживается Azure файлы NetApp.

## <a name="data-migration-and-protection-faqs"></a>Часто задаваемые вопросы миграции и защиты данных

### <a name="how-do-i-migrate-data-to-azure-netapp-files"></a>Как перенести данные в службу файлов Azure NetApp?
Служба файлов Azure NetApp предоставляет тома NFS и SMB.  Для переноса данных в службу, можно использовать любое средство копирования на основе файла. 

NetApp предлагает решения на основе SaaS, [синхронизация облака NetApp](https://cloud.netapp.com/cloud-sync-service).  Решение позволяет реплицировать NFS или общих папок SMB данных SMB или NFS файлы NetApp Azure экспортов. 

Можно также использовать широкого спектра бесплатных средств для копирования данных. Для NFS, можно использовать средства рабочих нагрузок например [rsync](https://rsync.samba.org/examples.html) для копирования и синхронизации исходных данных в томе NetApp службы файлов Azure. Для SMB, можно использовать рабочие нагрузки [robocopy](https://docs.microsoft.com/windows-server/administration/windows-commands/robocopy) таким же образом.  Эти средства также можно реплицировать разрешения для файла или папки. 

Далее приведены требования для переноса данных из локальной среды в службе файлов Azure NetApp. 

- Убедитесь, что файлы NetApp Azure доступен в целевом регионе Azure.
- Проверьте сетевое подключение между источником и целевой том NetApp службы файлов Azure IP-адрес. Передача данных между локально, а также службы файлов Azure NetApp поддерживается через ExpressRoute.
- Создайте целевую тома службы файлов Azure NetApp.
- Перенесите данные источника в целевой том с помощью инструмента копирования файла.

### <a name="how-do-i-create-a-copy-of-an-azure-netapp-files-volume-in-another-azure-region"></a>Как создать копию тома службы файлов Azure NetApp в другом регионе Azure?
    
Служба файлов Azure NetApp предоставляет тома NFS и SMB.  Любой инструмент на основе копии файла можно использовать для репликации данных между регионами Azure. 

NetApp предлагает решение на основе SaaS, [синхронизация облака NetApp](https://cloud.netapp.com/cloud-sync-service).  Решение позволяет реплицировать NFS или общих папок SMB данных SMB или NFS файлы NetApp Azure экспортов. 

Можно также использовать широкого спектра бесплатных средств для копирования данных. Для NFS, можно использовать средства рабочих нагрузок например [rsync](https://rsync.samba.org/examples.html) для копирования и синхронизации исходных данных в томе NetApp службы файлов Azure. Для SMB, можно использовать рабочие нагрузки [robocopy](https://docs.microsoft.com/windows-server/administration/windows-commands/robocopy) таким же образом.  Эти средства также можно реплицировать разрешения для файла или папки. 

Далее приведены требования для репликации тома NetApp службы файлов Azure в другой регион Azure. 
- Убедитесь, что файлы NetApp Azure доступен в целевом регионе Azure.
- Проверьте сетевое подключение между виртуальными сетями в каждом регионе. В настоящее время глобальный пиринг между виртуальными сетями не поддерживается.  Можно установить соединение между виртуальными сетями, связывание с каналом ExpressRoute и используя это VPN-подключения S2S. 
- Создайте целевую тома службы файлов Azure NetApp.
- Перенесите данные источника в целевой том с помощью инструмента копирования файла.

### <a name="is-migration-with-azure-data-box-supported"></a>С помощью Azure Data Box поддерживается, является перенос?

№ Azure Data Box поддерживается NetApp службы файлов Azure. 

### <a name="is-migration-with-azure-importexport-service-supported"></a>С помощью службы импорта и экспорта Azure поддерживается, является перенос?

№ Служба импорта и экспорта Azure не поддерживает файлы NetApp Azure в настоящее время.

## <a name="next-steps"></a>Дальнейшие действия  

- [Часто задаваемые вопросы о Microsoft Azure ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-faqs)
- [Часто задаваемые вопросы о виртуальной сети Microsoft Azure](https://docs.microsoft.com/azure/virtual-network/virtual-networks-faq)
- [Создание запроса на поддержку Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)
- [Azure Data Box](https://docs.microsoft.com/azure/databox-family/)