---
title: включение файла
description: включение файла
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: include
ms.date: 06/10/2018
ms.author: raynew
ms.custom: include file
ms.openlocfilehash: d77269c1e965d5bca1e32b756ef26e2c694e5c81
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/07/2019
ms.locfileid: "73747848"
---
**Требования к конфигурации и серверу обработки**


## <a name="hardware-requirements"></a>Требования к оборудованию

**Компонент** | **Требование** 
--- | ---
Ядра ЦП | 8 
ОЗУ | 16 ГБ
Количество дисков | 3 (диск ОС, диск кэша сервера обработки, диск хранения (для восстановления размещения)) 
Свободное место на диске (кэш сервера обработки) | 600 ГБ
Свободное место на диске (диск хранения) | 600 ГБ
 | 

## <a name="software-requirements"></a>Требования к программному обеспечению

**Компонент** | **Требование** 
--- | ---
операционная система | Windows Server 2012 R2 <br> Windows Server 2016
Язык операционной системы | Английский (en-us)
Роли Windows Server | Не включайте эти роли: <br> — доменные службы Active Directory; <br>— службы IIS; <br> — Hyper-V. 
Групповые политики | Не включать эти групповые политики: <br> — запрет на использование командной строки; <br> — запрет на использование инструментов редактирования реестра; <br> — логика доверия для вложенных файлов; <br> — включение выполнения скриптов. <br> [Подробнее](https://technet.microsoft.com/library/gg176671(v=ws.10).aspx)
IIS | — Нет ранее созданного веб-сайта по умолчанию. <br> — Веб-сайт или приложение не ожидают передачи данных через порт 443. <br>— Включить [анонимную проверку подлинности](https://technet.microsoft.com/library/cc731244(v=ws.10).aspx). <br> — Включить параметр [FastCGI](https://technet.microsoft.com/library/cc753077(v=ws.10).aspx) .
| 

## <a name="network-requirements"></a>Требования к сети

**Компонент** | **Требование** 
--- | --- 
Тип IP-адреса | Статическое 
порты; | 443 (оркестрация канала управления)<br>9443 (передача данных) 
Тип сетевой карты | VMXNET3 (если сервер конфигурации является виртуальной машиной VMware)
 |
**Доступ к Интернету** (серверу требуется доступ к следующим URL-адресам, напрямую или через прокси-сервер):|
\*.backup.windowsazure.com | Используется для передачи и координации реплицированных данных
\*.store.core.windows.net | Используется для передачи и координации реплицированных данных
\*.blob.core.windows.net | Используется для получения доступа к учетной записи хранения, в которой хранятся реплицируемые данные
\*.hypervrecoverymanager.windowsazure.com | Используется для операций управления репликацией и координации
https:\//management.azure.com | Используется для операций управления репликацией и координации 
*.services.visualstudio.com | Используется в целях телеметрии (необязательно)
time.nist.gov | Используется для проверки синхронизации времени между системой и глобальным временем
time.windows.com | Используется для проверки синхронизации времени между системой и глобальным временем
| <ul> <li> https:\//login.microsoftonline.com </li><li> https:\//secure.aadcdn.microsoftonline-p.com </li><li> HTTPS:\//login.live.com </li><li> HTTPS:\//graph.windows.net </li><li> https:\//login.windows.net </li><li> HTTPS:\//www.live.com </li><li> HTTPS:\//www.microsoft.com </li></ul> | Программе установки OVF требуется доступ к этим URL-адресам. Они используются для управления доступом и управления удостоверениями с Azure Active Directory.
https:\//dev.mysql.com/get/Downloads/MySQLInstaller/mysql-installer-community-5.7.20.0.msi  | Для завершения загрузки MySQL. </br> В нескольких регионах загрузка может быть перенаправлена на URL-адрес CDN. При необходимости убедитесь, что URL-адрес CDN также список разрешений.
|

## <a name="required-software"></a>Необходимое программное обеспечение

**Компонент** | **Требование** 
--- | ---
VMware vSphere PowerCLI | [PowerCLI версии 6,0](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1) следует устанавливать, если сервер конфигурации работает на виртуальной машине VMware.
MySQL | Должен быть установлен компонент MySQL. Можно установить вручную или Azure Site Recovery можно установить. (Дополнительные сведения см. в разделе [Настройка параметров](../articles/site-recovery/vmware-azure-deploy-configuration-server.md#configure-settings).)

## <a name="sizing-and-capacity-requirements"></a>Требования к размеру и емкости

В следующей таблице приведены требования к емкости для сервера конфигурации. При репликации нескольких виртуальных машин VMware ознакомьтесь с [вопросами планирования емкости](../articles/site-recovery/site-recovery-plan-capacity-vmware.md) и запустите [средство планировщик развертывания Azure Site Recovery](../articles/site-recovery/site-recovery-deployment-planner.md).


**ЦП** | **Память** | **Диск кэша** | **Частота изменения данных** | **Реплицируемые компьютеры**
--- | --- | --- | --- | ---
8 виртуальных ЦП<br/><br/> 2 сокета по 4 ядра с частотой \@ 2,5 ГГц | 16 ГБ | 300 ГБ | 500 ГБ или менее | < 100 компьютеров
12 виртуальных ЦП<br/><br/> 2 сокета по 6 ядер с частотой \@ 2,5 ГГц | 18 ГБ | 600 ГБ | От 500 ГБ до 1 ТБ | От 100 до 150 компьютеров
16 виртуальных ЦП<br/><br/> 2 сокета по 8 ядер с частотой \@ 2,5 ГГц | 32 ГБ | 1 TБ | от 1 до 2 ТБ | 150 – 200 компьютеров

