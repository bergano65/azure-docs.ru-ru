---
author: paulbouwer
ms.topic: include
ms.date: 10/09/2019
ms.author: pabouwer
ms.openlocfilehash: 1a023475de1ce2891916807632d9ee15e382326c
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81735285"
---
## <a name="download-and-install-the-linkerd-linkerd-client-binary"></a>Скачать и установить Linkerd linkerd клиент двоичный

В оболочке на базе PowerShell `Invoke-WebRequest` на Windows используйте для загрузки релиза Linkerd следующим образом:

```powershell
# Specify the Linkerd version that will be leveraged throughout these instructions
$LINKERD_VERSION="stable-2.6.0"

# Enforce TLS 1.2
[Net.ServicePointManager]::SecurityProtocol = "tls12"
$ProgressPreference = 'SilentlyContinue'; Invoke-WebRequest -URI "https://github.com/linkerd/linkerd2/releases/download/$LINKERD_VERSION/linkerd2-cli-$LINKERD_VERSION-windows.exe" -OutFile "linkerd2-cli-$LINKERD_VERSION-windows.exe"
```

Клиент `linkerd` двоичный работает на вашей клиентской машине и позволяет взаимодействовать с сеткой службы Linkerd. Используйте следующие команды для установки двоичного клиента Linkerd `linkerd` в оболочку на основе PowerShell на Windows. Эти команды копируют `linkerd` двоичную клиентскую папку Linkerd, а затем делают ее доступной как немедленно `PATH`(в текущей оболочке), так и постоянно (через перезапуск оболочки) через ваш . Для выполнения этих команд не нужны повышенные (админские) привилегии, и вам не нужно перезапускать оболочку.

```powershell
# Copy linkerd.exe to C:\Linkerd
New-Item -ItemType Directory -Force -Path "C:\Linkerd"
Copy-Item -Path ".\linkerd2-cli-$LINKERD_VERSION-windows.exe" -Destination "C:\Linkerd\linkerd.exe"

# Add C:\Linkerd to PATH. 
# Make the new PATH permanently available for the current User
$USER_PATH = [environment]::GetEnvironmentVariable("PATH", "User") + ";C:\Linkerd\"
[environment]::SetEnvironmentVariable("PATH", $USER_PATH, "User")
# Make the new PATH immediately available in the current shell
$env:PATH += ";C:\Linkerd\"
```