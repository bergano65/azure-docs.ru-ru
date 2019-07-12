---
title: Настройка подключения "точка — сеть" к Управляемому экземпляру Базы данных SQL Azure | Документация Майкрософт
description: Сведения об установке подключения "точка — сеть" к Управляемому экземпляру Базы данных SQL Azure в SQL Server Management Studio с локального клиентского компьютера.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, carlrab, bonova, jovanpop
manager: craigg
ms.date: 03/13/2019
ms.openlocfilehash: 33e1287edbca6b603d103f11636959b6fe13e578
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/11/2019
ms.locfileid: "67798096"
---
# <a name="quickstart-configure-a-point-to-site-connection-to-an-azure-sql-database-managed-instance-from-on-premises"></a>Краткое руководство. Настройка соединения "точка-сайт" с помощью управляемого экземпляра Базы данных SQL Azure

В этом кратком руководстве показано, как подключиться к Управляемому экземпляру Базы данных SQL Azure в [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS) с локального клиентского компьютера через подключение "точка — сеть". См. сведения о [подключениях "точка — сеть"](../vpn-gateway/point-to-site-about.md).

## <a name="prerequisites"></a>предварительные требования

В этом кратком руководстве:

- В качестве начальной точки используются ресурсы, созданные в руководстве по [созданию управляемого экземпляра](sql-database-managed-instance-get-started.md).
- Требуется PowerShell 5.1 и AZ PowerShell 1.4.0 или более поздней на локальном клиентском компьютере. При необходимости ознакомьтесь с инструкциями по [установке модуля PowerShell для Azure](https://docs.microsoft.com/powershell/azure/install-az-ps#install-the-azure-powershell-module).
- На локальном клиентском компьютере требуется последняя версия [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS).

## <a name="attach-a-vpn-gateway-to-your-managed-instance-virtual-network"></a>Подключение VPN-шлюза к виртуальной сети Управляемого экземпляра

1. Откройте PowerShell на клиентском компьютере на предприятии.

2. Скопируйте этот сценарий PowerShell. Этот сценарий подключает VPN-шлюз к виртуальной сети Управляемого экземпляра, созданной в кратком руководстве [Создание Управляемого экземпляра SQL Azure](sql-database-managed-instance-get-started.md). Скрипт выполняет следующее:

   - Создает и устанавливает сертификаты на клиентском компьютере.
   - Вычисляет будущий диапазон IP-адресов подсети VPN-шлюза.
   - Создает подсеть шлюза.
   - Развертывает шаблон Azure Resource Manager, который присоединяет VPN-шлюз к подсети VPN.

     ```powershell
     $scriptUrlBase = 'https://raw.githubusercontent.com/Microsoft/sql-server-samples/master/samples/manage/azure-sql-db-managed-instance/attach-vpn-gateway'

     $parameters = @{
       subscriptionId = '<subscriptionId>'
       resourceGroupName = '<resourceGroupName>'
       virtualNetworkName = '<virtualNetworkName>'
       certificateNamePrefix  = '<certificateNamePrefix>'
       }

     Invoke-Command -ScriptBlock ([Scriptblock]::Create((iwr ($scriptUrlBase+'/attachVPNGateway.ps1?t='+ [DateTime]::Now.Ticks)).Content)) -ArgumentList $parameters, $scriptUrlBase
     ```

     > [!IMPORTANT]
     > Чтобы использовать модуль Azure PowerShell Resource Manager, а не Az модуля, используйте следующий командлет: `attachVPNGateway.ps1` вместо `attachVPNGatewayAz.ps1` командлета.

3. Вставьте сценарий в окне PowerShell и укажите необходимые параметры. Значения для `<subscriptionId>`, `<resourceGroup>` и `<virtualNetworkName>` должны соответствовать тем, которые используются в кратком руководстве по [созданию управляемого экземпляра SQL Azure](sql-database-managed-instance-get-started.md). В качестве значения для `<certificateNamePrefix>` можно указать строку на ваш выбор.

4. Выполните следующий сценарий PowerShell.

> [!IMPORTANT]
> Подождите, пока выполнения сценария PowerShell.

## <a name="create-a-vpn-connection-to-your-managed-instance"></a>Создание VPN-подключения к Управляемому экземпляру

1. Войдите на [портале Azure](https://portal.azure.com/).
2. Откройте группу ресурсов, в которой вы создали шлюз виртуальной сети, а затем откройте ресурс шлюза виртуальной сети.
3. Щелкните **Конфигурация "точка — сеть"** , а затем — **Скачивание VPN-клиента**.

    ![Скачивание VPN-клиента](./media/sql-database-managed-instance-configure-p2s/download-vpn-client.png)  
4. На клиентском компьютере в локальной Извлеките файлы из ZIP-файл и затем откройте папку с извлеченные файлы.
5. Откройте "**WindowsAmd64** и откройте **VpnClientSetupAmd64.exe** файла.
6. Если появится сообщение **Windows protected your PC** (Система Windows защитила ваш компьютер), щелкните **Дополнительно**, а затем **Выполнить в любом случае**.

    ![Установка VPN-клиента](./media/sql-database-managed-instance-configure-p2s/vpn-client-defender.png)\
7. В диалоговом окне контроля учетных записей щелкните **Да** для продолжения.
8. В диалоговом окне, ссылающиеся на вашей виртуальной сети, выберите **Да** для установки VPN-клиента для виртуальной сети.

## <a name="connect-to-the-vpn-connection"></a>Установка соединения с VPN-подключением

1. Перейдите к **VPN** в **сеть и Интернет** на клиентском компьютере в локальной и выберите виртуальную сеть для подключения к этой виртуальной сети управляемого экземпляра. На следующем рисунке такая виртуальная сеть называется **MyNewVNet**.

    ![VPN-подключение](./media/sql-database-managed-instance-configure-p2s/vpn-connection.png)  
2. Нажмите кнопку **Подключиться**.
3. В диалоговом окне выберите **Подключиться**.

    ![VPN-подключение](./media/sql-database-managed-instance-configure-p2s/vpn-connection2.png)  
4. При появлении запроса о том, что диспетчеру подключений требуется более высокий уровень привилегий, чтобы обновить таблицу маршрутов, выберите **Продолжить**.
5. Чтобы продолжить, выберите **Да** в диалоговом окне контроля учетных записей.

   Вы установили VPN-подключение к виртуальной сети управляемого экземпляра.

    ![VPN-подключение](./media/sql-database-managed-instance-configure-p2s/vpn-connection-succeeded.png)  

## <a name="use-ssms-to-connect-to-the-managed-instance"></a>Подключение к Управляемому экземпляру с использованием SSMS

1. На локальном клиентском компьютере откройте SQL Server Management Studio (SSMS).
2. В диалоговом окне **Подключение к серверу** в поле **Имя сервера** введите полное **имя узла** для Управляемого экземпляра.
3. Выберите **Проверка подлинности SQL Server**, укажите имя пользователя и пароль, а затем нажмите кнопку **Подключиться**.

    ![подключение SSMS](./media/sql-database-managed-instance-configure-vm/ssms-connect.png)  

После подключения можно будет просматривать системные и пользовательские базы данных в узле "Базы данных". Также можно просматривать различные объекты в узлах безопасности, серверных объектов, репликации, управления, агента SQL Server и XEvent Profiler.

## <a name="next-steps"></a>Следующие шаги

- Краткое руководство по подключению с виртуальной машины Azure см. в статье о [настройке подключения "точка — сеть"](sql-database-managed-instance-configure-p2s.md).
- Обзор вариантов подключения для приложений см. в статье [Подключение приложения к Управляемому экземпляру Базы данных SQL](sql-database-managed-instance-connect-app.md).
- Чтобы восстановить имеющуюся базу данных SQL Server из локальной среды в управляемом экземпляре, можно использовать [Azure Database Migration Service для миграции](../dms/tutorial-sql-server-to-managed-instance.md) или [команду T-SQL RESTORE](sql-database-managed-instance-get-started-restore.md) для восстановления из файла резервной копии базы данных.
