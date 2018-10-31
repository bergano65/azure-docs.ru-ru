---
title: Подключение к Azure Stack | Документация Майкрософт
description: Сведения о подключении к пакету ASDK.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: 3cebbfa6-819a-41e3-9f1b-14ca0a2aaba3
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2018
ms.author: jeffgilb
ms.openlocfilehash: 55be312046f5cdea2c1481ed435b5859ab2c2540
ms.sourcegitcommit: f6050791e910c22bd3c749c6d0f09b1ba8fccf0c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50026823"
---
# <a name="connect-to-the-azure-stack-development-kit"></a>Подключение к пакету средств разработки Azure Stack

Для управления ресурсами сначала необходимо подключиться к пакету средств разработки Azure Stack (ASDK). В этой статье описаны действия, выполнив которые, вы сможете подключиться к пакету ASDK. Вы можете использовать один из следующих вариантов подключения:

* [Подключение к удаленному рабочему столу.](#connect-with-remote-desktop) При подключении с помощью удаленного рабочего стола один пользователь может быстро выполнить подключение к пакету средств разработки.
* [Виртуальная частная сеть (VPN).](#connect-with-vpn) При подключении с помощью VPN несколько пользователей могут подключаться одновременно с использованием клиентов за пределами инфраструктуры Azure Stack. Для VPN-подключения требуются некоторые настройки.

<a name="connect-to-azure-stack-with-remote-desktop"></a>
##  <a name="connect-to-azure-stack-by-using-remote-desktop-connection"></a>Подключение к Azure Stack с помощью удаленного рабочего стола

С помощью подключения удаленного рабочего стола один пользователь может управлять ресурсами на портале оператора или пользовательском портале.

1. Откройте подключение к удаленному рабочему столу (mstc.exe) и подключитесь к компьютеру, на котором размещен пакет средств разработки, с учетной записью **AzureStack\AzureStackAdmin**, используя пароль, указанный во время установки ASDK.  

2. Откройте диспетчер сервера (ServerManager.exe) на компьютере с пакетом средств разработки. Выберите **Локальный сервер**, отключите **конфигурацию усиленной безопасности Internet Explorer** и закройте диспетчер сервера.

3. Войдите на портал администрирования с учетной записью **AzureStack\CloudAdmin** или используйте другие учетные данные оператора Azure Stack. Адрес портала администрирования ASDK: [https://adminportal.local.azurestack.external](https://adminportal.local.azurestack.external).

4. Войдите на пользовательский портал с учетной записью **AzureStack\CloudAdmin** или используйте другие учетные данные пользователя Azure Stack. Адрес пользовательского портала ASDK: [https://portal.local.azurestack.external](https://portal.local.azurestack.external).

> [!NOTE]
> Дополнительные сведения о том, когда следует использовать ту или иную учетную запись, см. в разделе [Основы администрирования ASDK](asdk-admin-basics.md#what-account-should-i-use).

<a name="connect-to-azure-stack-with-vpn"></a>
## <a name="connect-to-azure-stack-by-using-vpn"></a>Подключение к Azure Stack с помощью VPN

К пакету ASDK можно установить VPN-подключение с разделенным туннелированием, чтобы получать доступ к порталам Azure Stack и установленным локально инструментам, таким как Visual Studio и PowerShell. Используя VPN-подключения, несколько пользователей могут одновременно подключаться к ресурсам Azure Stack, размещенным в ASDK.

VPN-подключение поддерживается в развертываниях Azure AD и служб федерации Active Directory (AD FS).

> [!NOTE]
> VPN-подключение не предоставляет возможность подключения к виртуальным машинам инфраструктуры Azure Stack.

### <a name="prerequisites"></a>Предварительные требования
Прежде чем настраивать VPN-подключение к ASDK, убедитесь, что выполнены следующие предварительные требования.

- Установите [Azure PowerShell, совместимый с Azure Stack](asdk-post-deploy.md#install-azure-stack-powershell), на своем локальном компьютере.  
- Скачайте [средства, необходимые для работы с Azure Stack](asdk-post-deploy.md#download-the-azure-stack-tools).

### <a name="set-up-vpn-connectivity"></a>Настройка VPN-подключения

Чтобы создать VPN-подключение к пакету ASDK, откройте PowerShell с правами администратора на локальном компьютере под управлением Windows. Затем выполните следующий скрипт (обновите значения IP-адреса и пароля для своей среды):

```PowerShell
# Change directories to the default Azure Stack tools directory
cd C:\AzureStack-Tools-master

# Configure Windows Remote Management (WinRM), if it's not already configured.
winrm quickconfig  

Set-ExecutionPolicy RemoteSigned

# Import the Connect module.
Import-Module .\Connect\AzureStack.Connect.psm1

# Add the development kit host computer’s IP address as the ASDK certificate authority (CA) to the list of trusted hosts. Make sure you update the IP address and password values for your environment.

$hostIP = "<Azure Stack host IP address>"

$Password = ConvertTo-SecureString `
  "<operator's password provided when deploying Azure Stack>" `
  -AsPlainText `
  -Force

Set-Item wsman:\localhost\Client\TrustedHosts `
  -Value $hostIP `
  -Concatenate

# Create a VPN connection entry for the local user.
Add-AzsVpnConnection `
  -ServerAddress $hostIP `
  -Password $Password

```

Если программа установки завершается успешно, **azurestack** появится в списке VPN-подключений.

![Сетевые подключения](media/asdk-connect/image3.png)  

### <a name="connect-to-azure-stack"></a>Подключение к Azure Stack

Подключитесь к экземпляру Azure Stack, используя один из следующих двух способов:  

* Использование команды `Connect-AzsVpn `:
    
  ```PowerShell
  Connect-AzsVpn `
    -Password $Password
  ```

  При появлении запроса обозначьте узел Azure Stack как доверенный и установите сертификат из **AzureStackCertificateAuthority** в хранилище сертификатов на локальном компьютере 
  
  > [!IMPORTANT]
  > Запрос может быть скрыт окном PowerShell.

* На локальном компьютере выберите **Параметры сети** > **VPN** > **azurestack** > **Подключиться**. При запросе на вход введите имя пользователя (**AzureStack\AzureStackAdmin**) и пароль.

### <a name="test-vpn-connectivity"></a>Проверка VPN-подключения

Чтобы проверить соединение с порталом, откройте веб-браузер и перейдите на пользовательский портал (https://portal.local.azurestack.external/) или на портал администрирования (https://adminportal.local.azurestack.external/). Войдите и создайте ресурсы.  

## <a name="next-steps"></a>Дополнительная информация

[Устранение неполадок](asdk-troubleshooting.md)
