---
title: Установка SAP HANA на виртуальные машины Azure Документы Microsoft Docs
description: Руководство по установке SAP HANA на Azure VMs
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: c51a2a06-6e97-429b-a346-b433a785c9f0
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/19/2020
ms.author: juergent
ms.openlocfilehash: e017e082472e7a4a2fab6a2845e52d3dc7acc460
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80123347"
---
# <a name="installation-of-sap-hana-on-azure-virtual-machines"></a>Установка SAP HANA на виртуальные машины Azure
## <a name="introduction"></a>Вступление
Это руководство поможет вам указать на нужные ресурсы для успешного развертывания HANA в виртуальных машинах Azure. Это руководство укажет вам на ресурсы документации, которые необходимо проверить перед установкой SAP HANA в Azure VM. Таким образом, вы можете выполнить правильные шаги, чтобы закончить с поддерживаемой конфигурацией SAP HANA в Azure VMs.  

> [!NOTE]
> Это руководство описывает развертывание SAP HANA на виртуальных машинах Azure. Для получения информации о том, как развернуть SAP HANA в крупных экземплярах HANA, см. [Как установить и настроить SAP HANA (Большие инстанции) в Azure.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-installation)
 
## <a name="prerequisites"></a>Предварительные требования
Это руководство также предполагает, что вы знакомы с:
* SAP HANA и SAP NetWeaver и их локальная установка.
* Как установить и эксплуатировать экземпляры приложений SAP HANA и SAP в Azure.
* Концепции и процедуры, задокументированные в:
   * Планирование развертывания SAP в Azure, которое включает планирование виртуальной сети Azure и использование хранилища Azure. Смотрите [руководство SAP NetWeaver на виртуальных машинах Azure - Руководство по планированию и реализации](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)
   * Принципы и способы развертывания виртуальных машин в Azure. Посмотреть [развертывание виртуальных машин Azure для SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/deployment-guide)
   * Концепции высокой доступности для SAP HANA, как это описано в [SAP HANA, высокой доступности для виртуальных машин Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview)

## <a name="step-by-step-before-deploying"></a>Шаг за шагом перед развертыванием
В этом разделе перечислены различные шаги, которые необходимо выполнить, прежде чем начать с установки SAP HANA в виртуальную машину Azure. Порядок перечисляется и как таковой должен быть выполнен в соответствии с перечисленными:

1. Не все возможные сценарии развертывания поддерживаются в Azure. Поэтому следует проверить [рабочую нагрузку SAP на практических машинах Azure, поддерживаемых сценариями,](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-planning-supported-configurations) которые вы имеете в виду при развертывании SAP HANA. Если сценарий не указан, необходимо предположить, что он не был протестирован и, как следствие, не поддерживается
2. Предполагая, что у вас есть приблизительное представление о требованиях к памяти для развертывания SAP HANA, необходимо найти подходящий VM Azure. Не все vMs, сертифицированные для SAP NetWeaver, как это описано в [примечании поддержки SAP #1928533,](https://launchpad.support.sap.com/#/notes/1928533)сертифицированы SAP HANA. Источником правды для сертифицированных SAP HANA VMs является [аппаратный каталог SAP HANA.](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) Единицы, начинающимся с **S,** являются [подразделениями HANA Large Instances,](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) а не Azure VMs.
3. Различные типы Azure VM имеют различные минимальные версии операционной системы для SUSE Linux или Red Hat Linux. На веб-сайте [SAP HANA аппаратного каталога](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure), вам нужно нажать на запись в списке SAP HANA сертифицированных единиц, чтобы получить подробные данные этого подразделения. Помимо поддерживаемой рабочей нагрузки HANA, в списке указаны релизы ОС, которые поддерживаются этими подразделениями для SAP HANA
4. Что касается релизов операционной системы, необходимо учитывать некоторые минимальные выбросы ядра. Эти минимальные релизы задокументированы в следующих примечаниях поддержки SAP:
    - [Заметка поддержки SAP #2814271 резервного копирования SAP HANA выходит из строя на Azure с ошибкой Checksum](https://launchpad.support.sap.com/#/notes/2814271)
    - [Заметка поддержки SAP #2753418 потенциальной деградации производительности из-за возврата Timer](https://launchpad.support.sap.com/#/notes/2753418)
    - [Заметка поддержки SAP #2791572 ухудшение производительности из-за отсутствия поддержки VDSO для Hyper-V в Azure](https://launchpad.support.sap.com/#/notes/2791572)
4. Основываясь на выпуске ОС, который поддерживается для выбора виртуального типа машины, необходимо проверить, поддерживается ли желаемый релиз SAP HANA с этим выпуском операционной системы. Прочитайте [примечание поддержки SAP #2235581](https://launchpad.support.sap.com/#/notes/2235581) для матрицы поддержки sAP HANA с различными версиями операционной системы.
5. Возможно, вы нашли допустимую комбинацию типа Azure VM, выпуска операционной системы и выпуска SAP HANA, необходимо зарегистрироваться в матрице доступности продуктов SAP. В матрице доступности SAP вы можете узнать, поддерживается ли продукт SAP, который вы хотите запустить в отношении вашей базы данных SAP HANA.


## <a name="step-by-step-vm-deployment-and-guest-os-considerations"></a>Пошагонная развертывание VM и соображения гостевой ОС
На этом этапе необходимо пройти через шаги развертывания VM (ы) для установки HANA и в конечном итоге оптимизировать выбранную операционную систему после установки.

1. Выбирай базовое изображение из галереи Azure. Если вы хотите создать собственное изображение операционной системы для SAP HANA, вам необходимо знать все различные пакеты, необходимые для успешной установки SAP HANA. В противном случае рекомендуется использовать изображения SUSE и Red Hat для SAP или SAP HANA из галереи изображений Azure. Эти изображения включают пакеты, необходимые для успешной установки HANA. На основании договора поддержки с поставщиком операционной системы, вам нужно выбрать изображение, где вы приносите свою собственную лицензию. Или вы выбираете изображение ОС, включающий поддержку
2. Если вы выбрали изображение гостевой ОС, которое требует от вас чего собственной лицензии, вам нужно зарегистрировать изображение ОС с подпиской, так что вы можете скачать и применить последние патчи. Этот шаг потребует публичного доступа в Интернет. Если только вы не настроили свой частный экземпляр, например, Сервера SMT в Azure.
3. Определите конфигурацию сети VM. Более подробную информацию можно прочитать в документе [sAP HANA конфигураций инфраструктуры и операций на Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations). Имейте в виду, что в Azure нет квот пропускной связи сети. В результате единственная цель направления трафика через различные ВНИЦ основывается на соображениях безопасности. Мы доверяем вам найти поддающийся поддержке компромисс между сложностью трафика, конечной которого являются несколько VNICs, и требованиями, предъявляемыми к аспектам безопасности.
3. Примените последние патчи к операционной системе после развертывания и регистрации VM. Зарегистрирован либо с вашей собственной подпиской. Или в случае, если вы выбрали изображение, которое включает в себя поддержку операционной системы VM должны иметь доступ к патчи уже. 
4. Примените мелодии, необходимые для SAP HANA. Эти мелодии перечислены в этих примечаниях поддержки SAP:

    - [Заметка поддержки SAP #2694118 - Red Hat Enterprise Linux HA Add-On на Azure](https://launchpad.support.sap.com/#/notes/2694118)
    - [Заметка поддержки SAP #1984787 - SUSE LINUX Enterprise Server 12: Примечания к установке](https://launchpad.support.sap.com/#/notes/1984787) 
    - [Заметка поддержки SAP #2578899 - SUSE Linux Enterprise Server 15: Installation Note](https://launchpad.support.sap.com/#/notes/2578899)
    - [SAP примечание поддержки #2002167 - Red Hat Предприятие Linux 7.x: Установка и обновление](https://launchpad.support.sap.com/#/notes/0002002167)
    - [SAP Support Note #2292690 — SAP HANA DB: Recommended OS settings for RHEL 7](https://launchpad.support.sap.com/#/notes/0002292690) (Примечание по поддержке SAP №2292690. База данных SAP HANA: рекомендуемые параметры операционной системы для RHEL 7). 
    -  [Примечание поддержки SAP #2772999 - Red Hat Enterprise Linux 8.x: Установка и конфигурация](https://launchpad.support.sap.com/#/notes/2772999) 
    -  [Примечание поддержки SAP #2777782 - SAP HANA DB: Рекомендуемые настройки ОС для RHEL 8](https://launchpad.support.sap.com/#/notes/2777782)
    -  [Заметка поддержки SAP #2455582 - Linux: Запуск SAP-приложений, составленных с gCC 6.x](https://launchpad.support.sap.com/#/notes/0002455582)
    -  [Заметка поддержки SAP #2382421 - Оптимизация конфигурации сети на уровне HANA и OS-Level](https://launchpad.support.sap.com/#/notes/2382421)

1. Выберите тип хранения Azure для SAP HANA. На этом этапе необходимо определиться с макетом хранилища для установки SAP HANA. Вы собираетесь использовать либо прикрепленные диски Azure, либо находящиеся в них акции Azure NFS. Типы хранения Данных Azure, которые или поддерживаемые и комбинации различных типов хранилищ Azure, которые могут быть использованы, задокументированы в [конфигурациях виртуальной машины SAP HANa Azure.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage) Возьмем конфигурации, задокументированные в качестве отправной точки. Для непроизводственных систем можно настроить более низкую пропускную стоимость или IOPS. Для производственных целей может потребоваться настроить немного больше пропускной переливок и IOPS.
2. Убедитесь, что вы настроили [Azure Write Accelerator](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator) для объемов, содержащих журналы транзакций DBMS или журналы повторов при использовании M-Series или Mv2-Series VMs. Будьте в курсе ограничений для ускорителя write, как это описано.
2. Проверьте, включена ли [ускоренная сеть Azure](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/) на развертывании VM(s).

> [!NOTE]
> Не все команды в различных профилях сапожной настройки или, как описано в заметках, могут успешно работать на Azure. Команды, которые будут манипулировать режимом питания VMs, обычно возвращаются с ошибкой, так как режим питания базового оборудования узла Azure не может быть изменен.

## <a name="step-by-step-preparations-specific-to-azure-virtual-machines"></a>Пошаговая подготовка, специфичная для виртуальных машин Azure
Одной из особенностей Azure является установка расширения Azure VM, которое предоставляет данные мониторинга для агента SAP Host. Подробная информация об установке этого расширения мониторинга задокументирована в:

-  [SAP Note 2191498](https://launchpad.support.sap.com/#/notes/2191498/E) обсуждает расширенный мониторинг SAP с Linux VMs на Azure 
-  [SAP Примечание 1102124](https://launchpad.support.sap.com/#/notes/1102124/E) обсуждает информацию о SAPOSCOL на Linux 
-  [SAP Note 2178632](https://launchpad.support.sap.com/#/notes/2178632/E) обсуждает ключевые показатели мониторинга для SAP на Microsoft Azure
-  [Развертывание виртуальных машин Azure для SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/deployment-guide#d98edcd3-f2a1-49f7-b26a-07448ceb60ca)

## <a name="sap-hana-installation"></a>Установка SAP HANA
При развертывании виртуальных машин Azure и регистрации и настройке операционных систем можно установить SAP HANA в соответствии с установкой SAP. Как хорошее начало, чтобы добраться до этой документации, начните с этого сайта SAP [HANA ресурсов](https://www.sap.com/products/hana/implementation/resources.html)

Для конфигураций масштабирования SAP HANA с использованием прямых прикрепленных дисков Azure Premium Storage или Ultra, прочитайте специфику в документе [конфигураций инфраструктуры SAP HANA и операций на Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations#configuring-azure-infrastructure-for-sap-hana-scale-out)


## <a name="additional-resources-for-sap-hana-backup"></a>Дополнительные ресурсы для резервного копирования SAP HANA
Для получения информации о том, как создать резервное копирование баз данных SAP HANA на MMs Azure, см.:
* [Руководство по резервному копированию SAP HANA на виртуальных машинах Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)
* [Резервное копирование SAP HANA в Azure на уровне файлов](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-file-level)

## <a name="next-steps"></a>Дальнейшие действия
Читайте документацию:

- [Конфигурации инфраструктуры SAP HANA и работа с ней в Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations)
- [Конфигурации хранилища виртуальных машин SAP HANA в Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage)





