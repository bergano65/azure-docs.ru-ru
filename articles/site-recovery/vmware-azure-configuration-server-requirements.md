---
title: Требования к серверу конфигурации для аварийного восстановления VMware в Azure с помощью Azure Site Recovery | Документация Майкрософт
description: В этой статье описываются требования и поддержка при развертывании сервера конфигурации для аварийного восстановления VMware в Azure с помощью Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
services: site-recovery
ms.topic: article
ms.date: 03/18/2019
ms.author: raynew
ms.openlocfilehash: 399dcd744819cf4cb5d9f5f9636967c34e186a0e
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/22/2019
ms.locfileid: "60004607"
---
# <a name="configuration-server-requirements-for-vmware-disaster-recovery-to-azure"></a>Требования к серверу конфигурации для аварийного восстановления VMware в Azure

При использовании [Azure Site Recovery](site-recovery-overview.md) для аварийного восстановления виртуальных машин VMware и физических серверов в Azure развертывается локальный сервер конфигурации.

- Сервер конфигурации используется для управления обменом данными между локальной средой VMware и Azure. Он также управляет репликацией данных.
- См. дополнительные сведения о [компонентах и процессах сервера конфигурации](vmware-azure-architecture.md).

## <a name="configuration-server-deployment"></a>Развертывание сервера конфигурации

Для аварийного восстановления виртуальных машин VMware в Azure вы развертываете сервер конфигурации как виртуальную машину VMware.

- Site Recovery предоставляет шаблон OVA, который нужно загрузить с портала Azure и импортировать в vCenter Server для настройки виртуальной машины сервера конфигурации.
- При развертывании сервера конфигурации с использованием шаблона OVA виртуальная машина автоматически соответствует требованиям, перечисленным в этой статье.
- Мы настоятельно рекомендуем настроить сервер конфигурации с помощью шаблона OVA. Однако, если вы настраиваете аварийное восстановление для виртуальных машин VMware и не можете использовать шаблон OVA, можно развернуть сервер конфигурации, следуя [этим инструкциям](physical-azure-set-up-source.md).
- При развертывании сервера конфигурации для аварийного восстановления локальных физических компьютеров в Azure следуйте инструкциям в [этой статье](physical-azure-set-up-source.md). 


## <a name="hardware-requirements"></a>Требования к оборудованию

**Компонент** | **Требование** 
--- | ---
Ядра ЦП | 8 
ОЗУ | 16 ГБ
Количество дисков | 3 (диск ОС, диск кэша сервера обработки, диск хранения (для восстановления размещения)) 
Свободное место на диске (кэш сервера обработки) | 600 ГБ
Свободное место на диске (диск хранения) | 600 ГБ

## <a name="software-requirements"></a>Требования к программному обеспечению

**Компонент** | **Требование** 
--- | ---
Операционная система | Windows Server 2012 R2 <br> Windows Server 2016
Язык операционной системы | Английский (en-us)
Роли Windows Server | Не включайте эти роли: <br> — доменные службы Active Directory; <br>— службы IIS; <br> — Hyper-V. 
Групповые политики | Не включать эти групповые политики: <br> — запрет на использование командной строки; <br> — запрет на использование инструментов редактирования реестра; <br> — логика доверия для вложенных файлов; <br> — включение выполнения скриптов. <br> [Подробнее](https://technet.microsoft.com/library/gg176671(v=ws.10).aspx)
IIS | — должен отсутствовать предварительно созданный веб-сайт по умолчанию; <br> — должен отсутствовать предварительно созданный веб-сайт или приложение, ожидающее передачи данных через порт 443. <br>— включите [анонимную аутентификацию](https://technet.microsoft.com/library/cc731244(v=ws.10).aspx); <br> — включите параметр [FastCGI](https://technet.microsoft.com/library/cc753077(v=ws.10).aspx); 

## <a name="network-requirements"></a>Требования к сети

**Компонент** | **Требование** 
--- | --- 
Тип IP-адреса | Статическое 
Доступ к Интернету | Сервер должен иметь доступ к этим URL-адресам (напрямую или через прокси-сервер): <br> - \*.accesscontrol.windows.net<br> - \*.backup.windowsazure.com <br>- \*.store.core.windows.net<br> - \*.blob.core.windows.net<br> - \*.hypervrecoverymanager.windowsazure.com  <br> — https:\//management.azure.com <br> - *.services.visualstudio.com <br> time.nist.gov <br> time.windows.com <br> Для OVF также необходим доступ к следующим URL-адресам: <br> — https:\//login.microsoftonline.com <br> — https:\//secure.aadcdn.microsoftonline-p.com <br> — https:\//login.live.com  <br> — https:\//auth.gfx.ms <br> — https:\//graph.windows.net <br> — https:\//login.windows.net <br> — https:\//www.live.com <br> — https:\//www.microsoft.com <br> — https:\//dev.mysql.com/get/Downloads/MySQLInstaller/mysql-installer-community-5.7.20.0.msi 
порты; | 443 (оркестрация канала управления)<br>9443 (передача данных) 
Тип сетевой карты | VMXNET3 (если сервером конфигурации является виртуальная машина VMware)

## <a name="required-software"></a>Необходимое программное обеспечение

**Компонент** | **Требование** 
--- | ---
VMware vSphere PowerCLI | Если сервер конфигурации работает на виртуальной машине VMware, должен быть установлен компонент [PowerCLI версии 6.0](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1).
MySQL | Должен быть установлен компонент MySQL. Его можно установить вручную, или его может установить Site Recovery.

## <a name="sizing-and-capacity-requirements"></a>Требования к размеру и емкости

В следующей таблице приведены требования к емкости для сервера конфигурации. Если выполняется репликация виртуальных машин VMware, изучите [рекомендации по планированию емкости](site-recovery-plan-capacity-vmware.md) и запустите инструмент [Планировщик развертывания Azure Site Recovery](site-recovery-deployment-planner.md) для репликации VMWare. 

**Компонент** | **Требование** 
--- | ---

| **ЦП** | **Память** | **Диск кэша** | **Частота изменения данных** | **Реплицируемые компьютеры** |
| --- | --- | --- | --- | --- |
| 8 виртуальных ЦП<br/><br/> 2 сокета по 4 ядра с частотой \@ 2,5 ГГц | 16 ГБ | 300 ГБ | 500 ГБ или менее | Менее 100 компьютеров |
| 12 виртуальных ЦП<br/><br/> 2 сокета по 6 ядер с частотой \@ 2,5 ГГц | 18 ГБ | 600 ГБ | 500 ГБ — 1 ТБ | От 100 до 150 компьютеров |
| 16 виртуальных ЦП<br/><br/> 2 сокета по 8 ядер с частотой \@ 2,5 ГГц | 32 ГБ | 1 TБ | 1-2 TБ | 150—200 компьютеров | 



## <a name="next-steps"></a>Дальнейшие действия
Настройка аварийного восстановления [виртуальных машин VMware](vmware-azure-tutorial.md) в Azure.
