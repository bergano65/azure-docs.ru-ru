---
author: paulbouwer
ms.service: container-service
ms.topic: include
ms.date: 11/15/2019
ms.author: pabouwer
ms.openlocfilehash: a1a608b4f4e1afe4a3cf99229561a91cc3f9fa96
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/19/2019
ms.locfileid: "74170846"
---
## <a name="download-and-install-the-istio-istioctl-client-binary"></a>Скачивание и установка двоичного файла клиента Istio истиоктл

В оболочке на основе PowerShell в Windows используйте `Invoke-WebRequest` для загрузки выпуска Istio и последующего извлечения с `Expand-Archive` следующим образом:

```powershell
# Specify the Istio version that will be leveraged throughout these instructions
$ISTIO_VERSION="1.4.0"

# Enforce TLS 1.2
[Net.ServicePointManager]::SecurityProtocol = "tls12"
$ProgressPreference = 'SilentlyContinue'; Invoke-WebRequest -URI "https://github.com/istio/istio/releases/download/$ISTIO_VERSION/istio-$ISTIO_VERSION-win.zip" -OutFile "istio-$ISTIO_VERSION.zip"
Expand-Archive -Path "istio-$ISTIO_VERSION.zip" -DestinationPath .
```

`istioctl` клиентский двоичный файл выполняется на клиентском компьютере и позволяет взаимодействовать с сеткой службы Istio. Используйте следующие команды для установки двоичного файла клиента Istio `istioctl` в оболочке на основе PowerShell в Windows. Эти команды копируют двоичный файл клиента `istioctl` в папку Istio, а затем делают его доступными как немедленно (в текущей оболочке), так и навсегда (при перезапуске оболочки) с помощью `PATH`. Для выполнения этих команд не требуются повышенные привилегии (администратора), и вам не нужно перезапускать оболочку.

```powershell
# Copy istioctl.exe to C:\Istio
cd istio-$ISTIO_VERSION
New-Item -ItemType Directory -Force -Path "C:\Istio"
Copy-Item -Path .\bin\istioctl.exe -Destination "C:\Istio\"

# Add C:\Istio to PATH. 
# Make the new PATH permanently available for the current User, and also immediately available in the current shell.
$PATH = [environment]::GetEnvironmentVariable("PATH", "User") + "; C:\Istio\"
[environment]::SetEnvironmentVariable("PATH", $PATH, "User") 
[environment]::SetEnvironmentVariable("PATH", $PATH)
```