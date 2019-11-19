---
author: paulbouwer
ms.service: container-service
ms.topic: include
ms.date: 11/15/2019
ms.author: pabouwer
ms.openlocfilehash: d812baa15380b7824deaed081d2857fbc6e0b1b6
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/19/2019
ms.locfileid: "74170829"
---
## <a name="download-and-install-the-istio-istioctl-client-binary"></a>Скачивание и установка двоичного файла клиента Istio истиоктл

В оболочке на основе Bash в MacOS используйте `curl` для загрузки выпуска Istio, а затем извлеките с помощью `tar` следующим образом:

```bash
# Specify the Istio version that will be leveraged throughout these instructions
ISTIO_VERSION=1.4.0

curl -sL "https://github.com/istio/istio/releases/download/$ISTIO_VERSION/istio-$ISTIO_VERSION-osx.tar.gz" | tar xz
```

`istioctl` клиентский двоичный файл выполняется на клиентском компьютере и позволяет взаимодействовать с сеткой службы Istio. Используйте следующие команды, чтобы установить двоичный файл клиента Istio `istioctl` в оболочке на основе Bash в MacOS. Эти команды копируют двоичный файл клиента `istioctl` в стандартное расположение программ для пользователя в `PATH`.

```bash
cd istio-$ISTIO_VERSION
sudo cp ./bin/istioctl /usr/local/bin/istioctl
sudo chmod +x /usr/local/bin/istioctl
```

Если вам нужна функция завершения командной строки для двоичного файла `istioctl` клиента Istio, настройте ее следующим образом:

```bash
# Generate the bash completion file and source it in your current shell
mkdir -p ~/completions && istioctl collateral --bash -o ~/completions
source ~/completions/istioctl.bash

# Source the bash completion file in your .bashrc so that the command-line completions
# are permanently available in your shell
echo "source ~/completions/istioctl.bash" >> ~/.bashrc
```