---
title: Настройка WinRM после создания виртуальной машины Azure | Документация Майкрософт
description: Описание процесса настройки службы удаленного управления Windows (WinRM) после создания виртуальной машины, размещенной в Azure.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: pbutlerm
ms.openlocfilehash: 6350d5bde737c46cf14a9aef75a7ec57260a6afa
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/10/2018
ms.locfileid: "53196510"
---
# <a name="configure-winrm-after-virtual-machine-creation"></a>Настройка WinRM после создания виртуальной машины

В этой статье описывается настройка имеющейся виртуальной машины (ВМ), размещенной в Azure, для включения WinRM по протоколу HTTPS.  Она применима только к виртуальным машинам на платформе Windows и требует выполнения следующих двух действий:

1. Включение передачи трафика через порт для WinRM по протоколу HTTPS.  Соответствующий параметр настраивается для виртуальной машины на портале Azure.
2. Настройка виртуальной машины для включения WinRM с помощью предоставленного скрипта PowerShell.


## <a name="enabling-port-traffic"></a>Включение передачи трафика через порт

Для WinRM по протоколу HTTPS используется порт 5896, который не включен по умолчанию на предварительно настроенных виртуальных машин с Windows, предлагаемых в Azure Marketplace. Чтобы его включить, добавьте новое правило в группу безопасности сети (NSG) на [портале Azure](https://portal.azure.com) в соответствии с приведенными ниже инструкциями.  Дополнительные сведения о группах безопасности сети см. в статье [Группы безопасности сети](https://docs.microsoft.com/azure/virtual-network/security-overview).

1.  Перейдите к колонке **Виртуальные машины >**  <*имя_виртуальной_машины*>    **> Параметры/Сети**.
2.  Щелкните имя группы безопасности сети (в этом примере **testvm11002**) для отображения ее свойств:

    ![Свойства группы безопасности сети](./media/nsg-properties.png)
 
3. В разделе **Параметры** выберите **Правила безопасности для входящего трафика** для отображения этой колонки.
4. Нажмите кнопку **+ Добавить** для создания нового правила под названием `WinRM_HTTPS` для TCP-порта 5986.

    ![Добавление правила безопасности для входящего сетевого трафика](./media/nsg-new-rule.png)

5. Когда вы укажете все необходимые значения, нажмите кнопку **ОК**.  Список правил безопасности для входящего трафика должен содержать следующие новые записи.

    ![Список правил безопасности для входящего сетевого трафика](./media/nsg-new-inbound-listing.png)


## <a name="configure-vm-to-enable-winrm"></a>Настройка виртуальной машины для включения WinRM 

Следуйте инструкциям ниже, чтобы включить и настроить функцию службы удаленного управления Windows на виртуальной машине Windows.   

1. Установите подключение к удаленному рабочему столу на размещенной в Azure виртуальной машине.  Дополнительные сведения см. в статье [Как подключиться к виртуальной машине Azure под управлением Windows и войти на нее](https://docs.microsoft.com/azure/virtual-machines/windows/connect-logon).  Остальные действия будут выполняться на виртуальной машине.
2. Загрузите следующие файлы и сохраните их в папке на виртуальной машине:
    - [ConfigureWinRM.ps1](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-winrm-windows/ConfigureWinRM.ps1)
    - [makecert.exe](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-winrm-windows/makecert.exe)
    - [winrmconf.cmd](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-winrm-windows/winrmconf.cmd)
3. Откройте **консоль PowerShell** с более высоким уровнем привилегий (**Запуск от имени администратора**). 
4. Выполните следующую команду, указав обязательный параметр: полное доменное имя (FQDN) для виртуальной машины: <br/>
   `ConfigureWinRM.ps1 <vm-domain-name>`

    ![Скрипт настройки Powershell 1](./media/powershell-file1.png)

    Этот скрипт зависит от двух других файлов в этой же папке.


## <a name="next-steps"></a>Дополнительная информация

После настройки WinRM вы будете готовы к [развертыванию виртуальной машины с содержащих ее виртуальных жестких дисков](./cpp-deploy-vm-vhd.md).
