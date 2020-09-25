---
title: Настройка подключения "точка — сеть" с помощью SSMS
titleSuffix: Azure SQL Managed Instance
description: Подключитесь к Azure SQL Управляемый экземпляр с помощью SQL Server Management Studio (SSMS), используя подключение типа "точка — сеть" с локального клиентского компьютера.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, jovanpop
ms.date: 03/13/2019
ms.openlocfilehash: a629d230d63506a163cac0c530a1a8fbfed9627f
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91325085"
---
# <a name="quickstart-configure-a-point-to-site-connection-to-azure-sql-managed-instance-from-on-premises"></a>Краткое руководство. Настройка подключения типа "точка — сеть" к Azure SQL Управляемый экземпляр из локальной среды
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

В этом кратком руководстве показано, как подключиться к Azure SQL Управляемый экземпляр с помощью [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS) с локального клиентского компьютера через подключение типа "точка — сеть". Дополнительные сведения о подключениях типа "точка — сеть" см. [в статье о VPN](../../vpn-gateway/point-to-site-about.md)-подключении "точка — сеть".

## <a name="prerequisites"></a>Предварительные требования

В этом кратком руководстве:

- Использует ресурсы, созданные при  [создании управляемого экземпляра](instance-create-quickstart.md) в качестве отправной точки.
- Требуется PowerShell 5,1 и Azure PowerShell 1.4.0 или более поздней версии на локальном клиентском компьютере. При необходимости ознакомьтесь с инструкциями по [установке модуля PowerShell для Azure](https://docs.microsoft.com/powershell/azure/install-az-ps#install-the-azure-powershell-module).
- Требуется последняя версия [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) на локальном клиентском компьютере.

## <a name="attach-a-vpn-gateway-to-a-virtual-network"></a>Подключение VPN-шлюза к виртуальной сети

1. Откройте PowerShell на локальном клиентском компьютере.

2. Скопируйте этот сценарий PowerShell. Этот сценарий присоединяет VPN-шлюз к виртуальной сети SQL Управляемый экземпляр, созданной в кратком руководстве по [созданию управляемого экземпляра](instance-create-quickstart.md) . Этот сценарий использует модуль Azure PowerShell AZ и выполняет следующие действия для узлов на основе Windows или Linux:

   - Создает и устанавливает сертификаты на клиентском компьютере.
   - Вычисляет диапазон IP-адресов подсети VPN-шлюза в будущем
   - Создание подсети шлюза
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

3. Вставьте сценарий в окне PowerShell и укажите необходимые параметры. Значения для параметров `<subscriptionId>` , `<resourceGroup>` и `<virtualNetworkName>` должны совпадать с теми, которые использовались в кратком руководстве по [созданию управляемого экземпляра](instance-create-quickstart.md) . В качестве значения для `<certificateNamePrefix>` можно указать строку на ваш выбор.

4. Выполните следующий сценарий PowerShell.

> [!IMPORTANT]
> Продолжайте работу до тех пор, пока не завершится выполнение сценария PowerShell.

## <a name="create-a-vpn-connection"></a>Создание VPN-подключения

1. Войдите на [портал Azure](https://portal.azure.com/).
2. Откройте группу ресурсов, в которой вы создали шлюз виртуальной сети, а затем откройте ресурс шлюза виртуальной сети.
3. Щелкните **Конфигурация "точка — сеть"**, а затем — **Скачивание VPN-клиента**.

    ![Скачивание VPN-клиента](./media/point-to-site-p2s-configure/download-vpn-client.png)  
4. На локальном клиентском компьютере извлеките файлы из ZIP-файла, а затем откройте папку с извлеченными файлами.
5. Откройте папку **WindowsAmd64** и откройте файл **VpnClientSetupAmd64.exe** .
6. Если появится сообщение **Windows protected your PC** (Система Windows защитила ваш компьютер), щелкните **Дополнительно**, а затем **Выполнить в любом случае**.

    ![Установка VPN-клиента](./media/point-to-site-p2s-configure/vpn-client-defender.png)
7. Если откроется диалоговое окно Контроль учетных записей пользователей, нажмите кнопку **Да**, чтобы продолжить.
8. В диалоговом окне, ссылающемся на виртуальную сеть, выберите **Да** , чтобы установить VPN-клиент для виртуальной сети.

## <a name="connect-to-the-vpn-connection"></a>Установка соединения с VPN-подключением

1. Перейдите к **VPN** в **сети & Интернет** на локальном клиентском компьютере и выберите ВИРТУАЛЬную сеть управляемый экземпляр SQL, чтобы установить подключение к этой виртуальной сети. На следующем рисунке такая виртуальная сеть называется **MyNewVNet**.

    ![VPN-подключение](./media/point-to-site-p2s-configure/vpn-connection.png)  
2. Щелкните **Подключить**.
3. В диалоговом окне выберите **Подключиться**.

    ![VPN-подключение](./media/point-to-site-p2s-configure/vpn-connection2.png)  
4. Когда появится запрос на то, что диспетчеру подключений требуются повышенные права для обновления таблицы маршрутов, нажмите кнопку **продолжить**.
5. Чтобы продолжить, выберите **Да** в диалоговом окне контроля учетных записей.

   Вы установили VPN-подключение к виртуальной сети SQL Управляемый экземпляр.

    ![VPN-подключение](./media/point-to-site-p2s-configure/vpn-connection-succeeded.png)  

## <a name="connect-with-ssms"></a>Подключение к SSMS

1. На локальном клиентском компьютере откройте SQL Server Management Studio.
2. В диалоговом окне **соединение с сервером** введите полное **имя узла** для управляемого экземпляра в поле **имя сервера** .
3. Выберите **Проверка подлинности SQL Server**, укажите имя пользователя и пароль, а затем нажмите кнопку **Подключиться**.

    ![Подключение SSMS](./media/point-to-site-p2s-configure/ssms-connect.png)  

После подключения можно будет просматривать системные и пользовательские базы данных в узле "Базы данных". Также можно просматривать различные объекты в узлах безопасности, серверных объектов, репликации, управления, агента SQL Server и XEvent Profiler.

## <a name="next-steps"></a>Дальнейшие действия

- Краткое руководство по подключению с виртуальной машины Azure см. в статье о [настройке подключения "точка — сеть"](point-to-site-p2s-configure.md).
- Обзор вариантов подключения для приложений см. в статье [Подключение приложений к Управляемому экземпляру SQL](connect-application-instance.md).
- Чтобы восстановить существующую SQL Server базу данных из локальной среды в управляемый экземпляр, можно использовать [Azure Database Migration Service для миграции](../../dms/tutorial-sql-server-to-managed-instance.md) или [команду T-SQL RESTORE](restore-sample-database-quickstart.md) для восстановления из файла резервной копии базы данных.
