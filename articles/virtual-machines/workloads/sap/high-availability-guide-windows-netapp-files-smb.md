---
title: Azure VMs HA для SAP NW на Windows с azure NetApp Файлов (SMB) Документы Майкрософт
description: Высокая доступность SAP NetWeaver на MMs Azure на Windows с файлами Azure NetApp (SMB) для приложений SAP
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 5e514964-c907-4324-b659-16dd825f6f87
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 10/29/2019
ms.author: radeltch
ms.openlocfilehash: b41db629c5308348f632b3dc51c75822ba361c60
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77591359"
---
# <a name="high-availability-for-sap-netweaver-on-azure-vms-on-windows-with-azure-netapp-filessmb-for-sap-applications"></a>Высокая доступность SAP NetWeaver на MMs Azure на Windows с файлами Azure NetApp (SMB) для приложений SAP

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[anf-azure-doc]:https://docs.microsoft.com/azure/azure-netapp-files/
[anf-avail-matrix]:https://azure.microsoft.com/global-infrastructure/services/?products=storage&regions=all
[anf-register]:https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-register
[anf-sap-applications-azure]:https://www.netapp.com/us/media/tr-4746.pdf

[2205917]:https://launchpad.support.sap.com/#/notes/2205917
[1944799]:https://launchpad.support.sap.com/#/notes/1944799
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1984787]:https://launchpad.support.sap.com/#/notes/1984787
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[1410736]:https://launchpad.support.sap.com/#/notes/1410736

[sap-swcenter]:https://support.sap.com/en/my-support/software-downloads.html

[suse-ha-guide]:https://www.suse.com/products/sles-for-sap/resource-library/sap-best-practices/
[suse-drbd-guide]:https://www.suse.com/documentation/sle-ha-12/singlehtml/book_sleha_techguides/book_sleha_techguides.html
[suse-ha-12sp3-relnotes]:https://www.suse.com/releasenotes/x86_64/SLE-HA/12-SP3/

[template-multisid-xscs]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json
[template-converged]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-converged-md%2Fazuredeploy.json
[template-file-server]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-file-server-md%2Fazuredeploy.json

[sap-hana-ha]:sap-hana-high-availability.md
[nfs-ha]:high-availability-guide-suse-nfs.md

В этой статье описывается, как развертывать, настраивать виртуальные машины, устанавливать кластерную структуру и устанавливать на Windows VMs высокодоступную систему SAP NetWeaver 7.50 с использованием [SMB](https://docs.microsoft.com/windows/win32/fileio/microsoft-smb-protocol-and-cifs-protocol-overview) в [файлах NetApp Azure.](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-introduction/)  

Слой базы данных не подробно описан в этой статье. Мы предполагаем, что [виртуальная сеть](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) Azure уже создана.  

Прежде всего прочитайте следующие примечания и документы SAP:

* [Документация файлов Сети Приложений Azure][anf-azure-doc] 
* SAP Примечание [1928533][1928533], который содержит:  
  * Список размеров Azure VM, которые поддерживаются для развертывания программного обеспечения SAP
  * важные сведения о доступных ресурсах для каждого размера виртуальной машины Azure;
  * сведения о поддерживаемом программном обеспечении SAP и сочетаниях операционных систем и баз данных;
  * Требуется версия ядра SAP для Windows в Microsoft Azure
* примечание к SAP [2015553][2015553], в котором описываются предварительные требования к SAP при развертывании программного обеспечения SAP в Azure;
* примечание к SAP [2178632][2178632], содержащее подробные сведения обо всех доступных метриках мониторинга для SAP в Azure;
* примечание к SAP [1999351][1999351], содержащее дополнительные сведения об устранении неполадок, связанных с расширением для расширенного мониторинга Azure для SAP;
* SAP Note [2287140](https://launchpad.support.sap.com/#/notes/2287140) перечисляет предпосылки для функции SMB-поддерживаемого CA протокола SMB 3.x.
* SAP Note [2802770](https://launchpad.support.sap.com/#/notes/2802770) имеет информацию об устранении неполадок для медленной транзакции SAP AL11 на Windows 2012 и 2016.
* SAP Note [1911507](https://launchpad.support.sap.com/#/notes/1911507) имеет информацию о прозрачной функции сбоя для файла на Windows Server с протоколом SMB 3.0.
* SAP Note [662452](https://launchpad.support.sap.com/#/notes/662452) имеет рекомендацию (деактивация поколения 8.3) для устранения производительности/ошибок файловой системы при доступе к данным.
* [Установка в Azure высокодоступной системы SAP NetWeaver в отказоустойчивом кластере Windows с файловым ресурсом для экземпляров SAP ASCS/SCS](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-installation-wsfc-file-share) 
* [Архитектура высокого уровня доступности и сценарии для SAP NetWeaver на виртуальных машинах Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-architecture-scenarios)
* [Добавление порта зонда в конфигурацию кластера ASCS](sap-high-availability-installation-wsfc-file-share.md)
* [Установка (A)SCS Instance на кластере Failover](https://www.sap.com/documents/2017/07/f453332f-c97c-0010-82c7-eda71af511fa.html)
* [Создание тома SMB для Azure NetApp Files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes-smb#requirements-for-active-directory-connections)
* [Приложения NetApp SAP от Microsoft Azure с помощью файлов NetApp Azure][anf-sap-applications-azure]

## <a name="overview"></a>Обзор

В SAP разработан новый подход и альтернатива общим дискам кластера для кластеризации экземпляра SAP ASCS/SCS в отказоустойчивом кластере Windows. Вместо того, чтобы использовать кластерные общие диски, можно использовать долю файла SMB для развертывания файлов глобального хоста SAP. Файлы Azure NetApp поддерживают SMBv3 (наряду с NFS) с NTFS ACL с помощью Active Directory. Файлы Azure NetApp автоматически очень доступны (так как это служба PaaS). Эти функции делают Azure NetApp Files отличным вариантом для размещения раздела файла SMB для SAP глобальным.  
Поддерживаются как [службы домена Azure Active Directory (AD),](https://docs.microsoft.com/azure/active-directory-domain-services/overview) так и [службы домена Active Directory Domain (AD DS).](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview) Вы можете использовать существующие контроллеры доменов Active Directory с помощью файлов Azure NetApp. Контроллеры домена могут быть в Azure как виртуальные машины, или в помещениях через ExpressRoute или S2S VPN. В этой статье мы будем использовать контроллер домена в Azure VM.  
Высокая доступность (HA) для центральных служб SAP Netweaver требует совместного хранения. Для достижения этой цели на Windows, до сих пор необходимо было построить либо sOFS кластера или использовать кластер общий диск s / W, как SIOS. Теперь можно достичь SAP Netweaver HA с помощью общего хранилища, развернутого в файлах NetApp Azure. Использование файлов Сети Приложений Azure для общего хранилища устраняет необходимость в SOFS или SIOS.  

> [!NOTE]
> Кластеризация экземпляров SAP ASCS/SCS с файловым ресурсом поддерживается для продуктов SAP NetWeaver 7.40 (и более поздней версии) с ядром SAP 7.49 (и более поздней версии).  

![Архитектура SAP ASCS/SCS HA с долей малого и среднего бизнеса](./media/virtual-machines-shared-sap-high-availability-guide/high-availability-windows-azure-netapp-files-smb.png)

Предпосылки для совместной акции файла SMB:
* Протокол SMB 3.0 (или более поздней версии).
* Возможность установки списков управления доступом Active Directory (ACL) для групп пользователей Active Directory и компьютерного объекта computer$.
* Доля файлов должна быть включена в HA.

Доля служб SAP Central в этой эталонной архитектуре предлагается Файлами NetApp Azure:

![Архитектура SAP ASCS/SCS HA с долей малого и среднего бизнеса](./media/virtual-machines-shared-sap-high-availability-guide/high-availability-windows-azure-netapp-files-smb-detail.png)

## <a name="create-and-mount-smb-volume-for-azure-netapp-files"></a>Создание и монтаж объема SMB для файлов Azure NetApp

Выполните следующие шаги, как подготовка к использованию файлов NetApp Azure.  

1. Следите за шагами, чтобы [зарегистрироваться для файлов NetApp Azure](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-register)  
2. Создайте учетную запись Azure NetApp, следуя шагам, описанным в [создании учетной записи NetApp](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-netapp-account)  
3. Настройка пула емкости в соответствии с инструкциями по [настройке пула емкости](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-set-up-capacity-pool)
4. Ресурсы Файлов Доступа КВПа Azure должны находиться в делегированной подсети. Следуйте инструкциям, чтобы [делегировать подсеть в Файлы NetApp Azure](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-delegate-subnet) для создания делегированной подсети.  

> [!IMPORTANT]
> Перед созданием объема SMB необходимо создать соединения Active Directory. Просмотрите [требования к соединениям Active Directory.](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes-smb#requirements-for-active-directory-connections)  

5. Создание подключения Active Directory, описанного в [«Создание активного подключения каталога»](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes-smb#create-an-active-directory-connection)  
6. Создание объема SMB Azure NetApp Файлов SMB, следуя инструкциям в [добавлении объема SMB](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes-smb#add-an-smb-volume)  
7. Установите объем sMB на вашей виртуальной машине Windows.

> [!TIP]
> Вы можете найти инструкции о томе файлов Azure NetApp, если вы переходите в [Azure Portal](https://portal.azure.com/#home) к объекту Файлов Сети Azure, нажмите на лезвие **томов,** а затем **установите инструкции.**  

## <a name="prepare-the-infrastructure-for-sap-ha-by-using-a-windows-failover-cluster"></a>Подготовка инфраструктуры для SAP HA с помощью кластера Сбой Windows 

1. [Установка требуемых Ip-адресов DNS](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-shared-disk#b22d7b3b-4343-40ff-a319-097e13f62f9e)  
2. [Установите статические IP-адреса для виртуальных машин SAP.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-shared-disk#84c019fe-8c58-4dac-9e54-173efd4b2c30)
3. [Настройте статические IP-адреса для внутренней подсистемы балансировки нагрузки Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-shared-disk#7a8f3e9b-0624-4051-9e41-b73fff816a9e).
4. [Задайте правила балансировки нагрузки ASCS/SCS по умолчанию для внутренней подсистемы балансировки нагрузки Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-shared-disk#f19bd997-154d-4583-a46e-7f5a69d0153c).
5. [Измените правила балансировки нагрузки ASCS/SCS для балансировора внутренней нагрузки Azure.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-shared-disk#fe0bd8b5-2b43-45e3-8295-80bee5415716)
6. [Добавьте виртуальные компьютеры Windows в домен.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-shared-disk#e69e9a34-4601-47a3-a41c-d2e11c626c0c)
7. [Добавление записей реестра на обоих кластерных узлах экземпляра SAP ASCS/SCS](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-shared-disk#661035b2-4d0f-4d31-86f8-dc0a50d78158)
8. [Настройка кластера сбоя Windows Server для экземпляра SAP ASCS/SCS](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-shared-disk#0d67f090-7928-43e0-8772-5ccbf8f59aab)
9. Если вы используете Windows Server 2016, мы рекомендуем настроить [Azure Cloud Witness.](https://docs.microsoft.com/windows-server/failover-clustering/deploy-cloud-witness)


## <a name="install-sap-ascs-instance-on-both-nodes"></a>Установка экземпляра SAP ASCS на обоих узлах

Вам нужно следующее программное обеспечение от SAP:
   * SAP Software Provisioning Manager (SWPM) версия инструмента установки SPS25 или позже.
   * Ядро SAP 7.49 или более позднее
   * Создайте виртуальное имя хоста (название кластерной сети) для кластерного экземпляра SAP ASCS/SCS, как описано в [«Создать виртуальное имя хоста» для кластерного экземпляра SAP ASCS/SCS.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-installation-wsfc-shared-disk#a97ad604-9094-44fe-a364-f89cb39bf097)

> [!NOTE]
> Кластеризация экземпляров SAP ASCS/SCS с файловым ресурсом поддерживается для продуктов SAP NetWeaver 7.40 (и более поздней версии) с ядром SAP 7.49 (и более поздней версии).  

### <a name="install-an-ascsscs-instance-on-the-first-ascsscs-cluster-node"></a>Установка экземпляра ASCS/SCS на первом кластерном узлах ASCS/SCS

1. Установите экземпляр SAP ASCS/SCS на первом кластерном узлах. Запустите инструмент установки SAP SWPM, а затем перейдите на: **Продукт** > **DBMS** > Установка > Приложения Сервер ABAP (или Java) > системы высокой доступности > экземпляре ASCS/SCS > первый кластерный узлы.  

2. Выберите **кластер обмена файлами** в качестве конфигурации совместного кластера в SWPM.  
3. При запросе на **шаг SAP системы кластера параметры,** введите имя хоста для Azure NetApp Файлы SMB доля вы уже создали как **имя хостатора файла.**  В этом примере имя хоста доли SMB **anfsmb-9562.** 

> [!IMPORTANT]
> Если предварительные результаты проверки в SWPM показывают, что условие функции непрерывной доступности не выполняется, это может быть решено, следуя инструкциям в [сообщении об ошибке с задержкой при попытке получить доступ к общей папке, которая больше не существует в Windows.](https://support.microsoft.com/help/2820470/delayed-error-message-when-you-try-to-access-a-shared-folder-that-no-l)  

> [!TIP]
> Если предварительные результаты проверки в SWPM показывает условие swap Size не выполнено, вы можете настроить размер SWAP, перенаправившись в My Computer>System Properties>настройки производительности> Расширенный> виртуальная память> Изменение.  

4. Налаживать кластерный `SAP-SID-IP` ресурс SAP, порт зонда, с помощью PowerShell. Выполните эту конфигурацию на одном из кластерных узлов SAP ASCS/SCS, как описано в [порту зонда Настройка.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-installation-wsfc-shared-disk#10822f4f-32e7-4871-b63a-9b86c76ce761)

### <a name="install-an-ascsscs-instance-on-the-second-ascsscs-cluster-node"></a>Установка экземпляра ASCS/SCS на втором кластерном узлах ASCS/SCS

1. Установите экземпляр SAP ASCS/SCS на втором кластерном узлах. Запустите инструмент установки SAP SWPM, а затем перейдите на **продукт** > **DBMS** > Установка > Application Server ABAP (или Java) > системы высокой доступности > экземпляре ASCS/SCS > Дополнительный кластерный узла.  

### <a name="install-a-dbms-instance-and-sap-application-servers"></a>Установка экземпляра СУБД и серверов приложений SAP

Завершите установку SAP, установив:

   * Экземпляр DBMS  
   * Основной сервер приложений SAP  
   * Дополнительный сервер приложений SAP  

## <a name="test-the-sap-ascsscs-instance-failover"></a>Протестировать сбой экземпляра SAP ASCS/SCS 

### <a name="fail-over-from-cluster-node-a-to-cluster-node-b-and-back"></a>Сбой от кластерного узла A до кластерного узла B и обратно
В этом тестовом сценарии мы будем называть кластерный узло sapascs1 узлом А, а кластерный узло sapascs2 как узло B.

1. Проверить, что кластерные ресурсы работают на уде a. ![Рисунок 1: Windows Server failover кластерных ресурсов, работающих на уде А до теста failover](./media/virtual-machines-shared-sap-high-availability-guide/high-availability-windows-azure-netapp-files-smb-figure-1.png)  

2. Перезагрузка кластерного узла А. Ресурсы кластера SAP перейдут на ![кластерный узла B. Рисунок 2: Сбой кластерных ресурсов Windows Server, работающих на уде еде B после теста на сбой](./media/virtual-machines-shared-sap-high-availability-guide/high-availability-windows-azure-netapp-files-smb-figure-2.png)  


## <a name="lock-entry-test"></a>Тест на ввод блокировки

1.Проверить, что сервер репликации SAP Enqueue (ERS) активен  
2. Войти в систему SAP, выполнить транзакцию SU01 и открыть идентификатор пользователя в режиме изменения. Это создаст вход блокировки SAP.  
3. При входе в систему SAP отобразите вход блокировки, отправившись в транзакцию ST12.  
4. Сбой над ресурсами ASCS от кластерного узла A до кластерного узла B.  
5. Убедитесь, что запись блокировки, генерируемая до сбоя ресурсов кластера SAP ASCS/SCS, сохраняется.  

![Рисунок 3: Запись блокировки сохраняется после сбоя теста](./media/virtual-machines-shared-sap-high-availability-guide/high-availability-windows-azure-netapp-files-smb-figure-3.png)  

Для получения дополнительной информации, [см. Устранение неполадок для Enqueue Failover в ASCS с ERS](https://wiki.scn.sap.com/wiki/display/SI/Troubleshooting+for+Enqueue+Failover+in+ASCS+with+ERS)
## <a name="next-steps"></a>Дальнейшие действия

* [SAP NetWeaver на виртуальных машинах Windows. Руководство по планированию и внедрению][planning-guide]
* [Развертывание программного обеспечения SAP на виртуальных машинах Azure][deployment-guide]
* [Развертывание СУБД виртуальных машин Azure для SAP][dbms-guide]
* Узнать, как установить высокую доступность и планировать аварийное восстановление SAP 
* HANA на Azure (большие экземпляры), [см. SAP HANA (крупные экземпляры) высокой доступности и аварийного восстановления на Azure.](hana-overview-high-availability-disaster-recovery.md)
* Дополнительные сведения об установке высокого уровня доступности и плана для аварийного восстановления SAP HANA на виртуальных машинах Azure см. в статье [Высокий уровень доступности SAP HANA на виртуальных машинах Azure][sap-hana-ha].
