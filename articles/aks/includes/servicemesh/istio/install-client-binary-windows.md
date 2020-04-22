---
author: paulbouwer
ms.topic: include
ms.date: 11/15/2019
ms.author: pabouwer
ms.openlocfilehash: e26a2c214a03243d6507296c1e981706be8c56db
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81737016"
---
## <a name="download-and-install-the-istio-istioctl-client-binary"></a>Скачать и установить двоичный клиент Istio istioctl

В оболочке на основе PowerShell `Invoke-WebRequest` на Windows используйте для загрузки релиза Istio, а затем извлекайте `Expand-Archive` следующие

```powershell
# Specify the Istio version that will be leveraged throughout these instructions
$ISTIO_VERSION="1.4.0"

# Enforce TLS 1.2
[Net.ServicePointManager]::SecurityProtocol = "tls12"
$ProgressPreference = 'SilentlyContinue'; Invoke-WebRequest -URI "https://github.com/istio/istio/releases/download/$ISTIO_VERSION/istio-$ISTIO_VERSION-win.zip" -OutFile "istio-$ISTIO_VERSION.zip"
Expand-Archive -Path "istio-$ISTIO_VERSION.zip" -DestinationPath .
```

Клиент `istioctl` двоичный работает на вашей клиентской машине и позволяет взаимодействовать с сеткой обслуживания Istio. Используйте следующие команды для установки двоичного файла клиента Istio `istioctl` в оболочку на основе PowerShell в Windows. Эти команды копируют `istioctl` двоичную клиентскую папку Istio, а затем делают ее доступной как немедленно `PATH`(в текущей оболочке), так и постоянно (через перезапуск оболочки) через ваш . Для выполнения этих команд не нужны повышенные (админские) привилегии, и вам не нужно перезапускать оболочку.

```powershell
# Copy istioctl.exe to C:\Istio
cd istio-$ISTIO_VERSION
New-Item -ItemType Directory -Force -Path "C:\Istio"
Copy-Item -Path .\bin\istioctl.exe -Destination "C:\Istio\"

# Add C:\Istio to PATH. 
# Make the new PATH permanently available for the current User
$USER_PATH = [environment]::GetEnvironmentVariable("PATH", "User") + ";C:\Istio\"
[environment]::SetEnvironmentVariable("PATH", $USER_PATH, "User")
# Make the new PATH immediately available in the current shell
$env:PATH += ";C:\Istio\"
```