---
title: Развертывание IBM Db2 pureScale на Azure
description: Узнайте, как развернуть [пример архитектуры](ibm-db2-purescale-azure.md), который недавно использовался для переноса данных предприятия из среды IBM Db2 на z/OS в IBM Db2 pureScale на Azure.
services: virtual-machines-linux
documentationcenter: ''
author: njray
manager: edprice
editor: edprice
tags: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 11/09/2018
ms.author: njray
ms.openlocfilehash: 24bdc9867af869437cc0e440a80e5bf4813abbae
ms.sourcegitcommit: 8314421d78cd83b2e7d86f128bde94857134d8e1
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/19/2018
ms.locfileid: "51978182"
---
# <a name="deploy-ibm-db2-purescale-on-azure"></a>Развертывание IBM Db2 pureScale на Azure

В этой статье описано развертывание [примера архитектуры](ibm-db2-purescale-azure.md), который недавно использовался для переноса данных предприятия из среды IBM Db2 на z/OS в IBM Db2 pureScale на Azure.

Чтобы выполнить шаги, используемые во время миграции, воспользуйтесь скриптами установки в репозитории [Db2onAzure](http://aka.ms/db2onazure) на сайте GitHub. Эти скрипты основаны на архитектуре, используемой для обычной рабочей нагрузки OLTP среднего размера.

## <a name="get-started"></a>Начало работы

Чтобы развернуть эту архитектуру, скачайте и запустите скрипт deploy.sh, доступный в репозитории [Db2onAzure](http://aka.ms/db2onazure) на сайте GitHub.

Репозиторий также включает скрипты для настройки панели мониторинга Grafana, которую можно использовать для отправки запросов в Prometheus, систему мониторинга и оповещения с открытым исходным кодом, входящую в состав Db2.

> [!NOTE]
> Скрипт deploy.sh в клиенте создает закрытые ключи SSH и передает их в шаблон развертывания через HTTPS. Для безопасного хранения секретов, ключей и паролей рекомендуем использовать [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview).

## <a name="how-the-deployment-script-works"></a>Как работает скрипт развертывания

Скрипт Deploy.sh создает и настраивает ресурсы Azure, которые используются в этой архитектуре. Скрипт запрашивает подписку Azure и виртуальные машины, используемые в целевой среде, а затем выполняет следующие операции:

-   настройка группы ресурсов, виртуальной сети и подсети в Azure для установки;

-   настройка групп безопасности сети и SSH для среды;

-   настройка нескольких сетевых адаптеров на виртуальных машинах GlusterFS и Db2 pureScale;

-   создание виртуальных машин в хранилище GlusterFS;

-   создание виртуальной машины jumpbox;

-   создание виртуальной машины Db2 pureScale;

-   создание следящей виртуальной машины, связь с которой проверят Db2 pureScale.

-   создание виртуальной машины Windows для тестирования (без установки чего-либо).

После этого скрипты развертывания создают виртуальную сеть хранения данных iSCSI (vSAN) для подключения к общему хранилищу в Azure. В этом примере iSCSI подключается к GlusterFS. Это решение также дает возможность установить цели iSCSI как один узел Windows. (iSCSI предоставляет интерфейс общего блочного хранилища по TCP/IP, что позволяет процедуре установки Db2 pureScale использовать интерфейс устройства для подключения к общему хранилищу.) Основы GlusterFS см. в разделе [Архитектура: типы томов](https://docs.gluster.org/en/latest/Quick-Start-Guide/Architecture/) руководства по началу работы с GlusterFS.

Скрипты развертывания выполняют следующие общие действия:

1.  Настройка кластера общего хранилища в Azure. GlusterFS используется для настройки кластера общего хранилища. При этом используется по крайней мере два узла Linux. Сведения о настройке см. в разделе [Настройка хранилища Red Hat Gluster в Microsoft Azure](https://access.redhat.com/documentation/en-us/red_hat_gluster_storage/3.1/html/deployment_guide_for_public_cloud/chap-documentation-deployment_guide_for_public_cloud-azure-setting_up_rhgs_azure) в документации по Red Hat Gluster.

2.  Настройка интерфейса iSCSI Direct на целевых серверах Linux для GlusterFS. Сведения о настройке см. в разделе [iSCSI GlusterFS](https://docs.gluster.org/en/latest/Administrator%20Guide/GlusterFS%20iSCSI/) в руководстве администратора GlusterFS.

3.  Настройка инициатора iSCSI на виртуальных машинах Linux, которые будут получать доступ к кластеру GlusterFS, с помощью цели iSCSI. Сведения о настройке см. в разделе [Настройка цели и инициатора iSCSI в Linux](https://www.rootusers.com/how-to-configure-an-iscsi-target-and-initiator-in-linux/) документации по RootUsers.

4.  Установка GlusterFS в качестве уровня хранилища для интерфейса iSCSI.

После создания устройства iSCSI остается установить Db2 pureScale. Когда устанавливается Db2 pureScale, [IBM Spectrum Scale](https://www.ibm.com/support/knowledgecenter/SSEPGG_11.1.0/com.ibm.db2.luw.qb.server.doc/doc/t0057167.html) (прежнее название — GPFS) компилируется и устанавливается в кластере GlusterFS. Эта кластерная файловая система позволяет Db2 pureScale обеспечить совместное использование данных на нескольких виртуальных машинах с подсистемой Db2 pureScale. Дополнительные сведения см. в документации по [IBM Spectrum Scale](https://www.ibm.com/support/knowledgecenter/en/STXKQY_4.2.0/ibmspectrumscale42_welcome.html) на сайте IBM.

## <a name="db2-purescale-response-file"></a>Файл ответов Db2 pureScale

Репозиторий GitHub включает Db2server.rsp, файл ответов, который позволяет создать автоматизированный скрипт для установки Db2 pureScale. В приведенной ниже таблице указаны параметры Db2 pureScale, которые файл ответов использует для установки. Вы можете настроить файл ответов для своей среды установки.

> [!NOTE]
> Пример файла ответов, Db2server.rsp, включен в репозиторий [Db2onAzure](http://aka.ms/db2onazure) на сайте GitHub. Чтобы этот файл работал в вашей среде, его сначала необходимо отредактировать.

**Параметры файла ответов Db2 pureScale**

| Имя экрана               | Поле                                        | Значение                                                                                                 |
|---------------------------|----------------------------------------------|-------------------------------------------------------------------------------------------------------|
| Экран приветствия                   |                                              | "New Install" (Новая установка)                                                                                           |
| "Choose a Product" (Выбор продукта)          |                                              | "Db2 Version 11.1.3.3. Server Editions with Db2 pureScale" (Db2 версии 11.1.3.3, серверные выпуски с Db2 pureScale)                                              |
| Параметр Configuration             | Каталог                                    | /data1/opt/ibm/db2/V11.1                                                                              |
|                           | "Select the installation type" (Выбор типа установки)                 | "Typical" (Стандартный)                                                                                               |
|                           | "I agree to the IBM terms" (Я принимаю условия IBM)                     | Флажок установлен                                                                                               |
| "Instance Owner" (Владелец экземпляра)            | "Existing User For Instance, User name" (Существующий пользователь экземпляра, имя пользователя)        | Db2sdin1                                                                                              |
| "Fenced User" (Изолированный пользователь)               | "Existing User, User name" (Существующий пользователь, имя пользователя)                     | Db2sdfe1                                                                                              |
| "Cluster File System" (Файловая система кластера)       | "Shared disk partition device path" (Путь к устройству с общим разделом диска)            | /dev/dm-2                                                                                             |
|                           | "Mount point" (Точка подключения)                                  | /Db2sd\_1804a                                                                                         |
|                           | "Shared disk for data" (Общий диск для данных)                         | /dev/dm-1                                                                                             |
|                           | "Mount point (Data)" (Точка подключения, данные)                           | /Db2fs/datafs1                                                                                        |
|                           | "Shared disk for log" (Общий диск для журнала)                          | /dev/dm-0                                                                                             |
|                           | "Mount point (Log)" (Точка подключения, журнал)                            | /Db2fs/logfs1                                                                                         |
|                           | "Db2 Cluster Services Tiebreaker. Device path" (Схема разрешения конфликтов служб кластеров Db2, путь к устройству) | /dev/dm-3                                                                                             |
| "Host List" (Список узлов)                 | d1 [eth1], d2 [eth1], cf1 [eth1], cf2 [eth1] |                                                                                                       |
|                           | "Preferred primary CF" (Предпочтительный основной CF)                         | cf1                                                                                                   |
|                           | "Preferred secondary CF" (Предпочтительный дополнительный CF)                       | cf2                                                                                                   |
| "Response File and Summary" (Файл ответов и сводка) | Первый параметр                                 | "Install Db2 Server Edition with the IBM Db2 pureScale feature and save my settings in a response file" (Установить Db2 Server Edition с функцией IBM Db2 pureScale и сохранить параметры в файле ответов) |
|                           | "Response file name" (Имя файла ответов)                           | /root/DB2server.rsp                                                                                   |

### <a name="notes-about-this-deployment"></a>Заметки об этом развертывании

-   Значения /dev-dm0, /dev-dm1, /dev-dm2 и /dev-dm3 могут измениться после перезагрузки на виртуальной машине, где выполняется установка (d0 в автоматизированном скрипте). Чтобы найти правильные значения, перед заполнением файла ответов выполните следующую команду на сервере, на котором будет выполняться установка:

```
   [root\@d0 rhel]\# ls -als /dev/mapper
   total 0
   0 drwxr-xr-x 2 root root 140 May 30 11:07 .
   0 drwxr-xr-x 19 root root 4060 May 30 11:31 ..
   0 crw------- 1 root root 10, 236 May 30 11:04 control
   0 lrwxrwxrwx 1 root root 7 May 30 11:07 db2data1 -\> ../dm-1
   0 lrwxrwxrwx 1 root root 7 May 30 11:07 db2log1 -\> ../dm-0
   0 lrwxrwxrwx 1 root root 7 May 30 11:26 db2shared -\> ../dm-2
   0 lrwxrwxrwx 1 root root 7 May 30 11:08 db2tieb -\> ../dm-3
```

-   В скриптах установки для дисков iSCSI используются псевдонимы, чтобы можно было легко найти фактические имена.

-   При запуске скрипта установки на d0 значения **/dev/dm-\*** могут отличаться на d1, cf0 и cf1. Для установки Db2 pureScale это не имеет значения.

## <a name="troubleshooting-and-known-issues"></a>Устранение неполадок и известные проблемы

Репозиторий GitHub включает базу знаний, которую ведут авторы. В ней вы найдете описания потенциальных проблем и способов их решения. Например, проблемы могут возникать:

-   при попытке связаться с IP-адресом шлюза;

-   компиляции GPL;

-   сбое подтверждения безопасности между узлами;

-   обнаружении установщиком Db2 существующей файловой системы;

-   установке GPFS вручную;

-   установке Db2 pureScale, когда уже выполнено создание GPFS;

-   удалении Db2 pureScale и GPFS.

Дополнительные сведения об этих и других известных проблемах см. в файле kb.md в репозитории [Db2onAzure](http://aka.ms/Db2onAzure).

## <a name="next-steps"></a>Дополнительная информация

-   [iSCSI GlusterFS](https://docs.gluster.org/en/latest/Administrator%20Guide/GlusterFS%20iSCSI/)

-   [Создание обязательных пользователей для установки Db2 pureScale Feature](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.qb.server.doc/doc/t0055374.html?pos=2)

-   [Db2icrt: команда для создания экземпляра](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.admin.cmd.doc/doc/r0002057.html)

-   [Кластеры Db2 pureScale](http://www.ibmbigdatahub.com/blog/db2-purescale-clustered-database-solution-part-1)

-   [IBM Data Studio](https://www.ibm.com/developerworks/downloads/im/data/index.html/)

-   [Альянс по модернизации платформы: IBM Db2 на Azure](https://www.platformmodernization.org/pages/ibmdb2azure.aspx)

-   [Виртуальный центр обработки данных Azure: руководство по миграции методом lift-and-shift](https://azure.microsoft.com/resources/azure-virtual-datacenter-lift-and-shift-guide/)
