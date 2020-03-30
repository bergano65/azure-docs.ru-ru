---
author: paulbouwer
ms.topic: include
ms.date: 11/15/2019
ms.author: pabouwer
ms.openlocfilehash: b310de560f9791e1fc49d54dfbf0789c38d37f57
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77594017"
---
## <a name="download-and-install-the-istio-istioctl-client-binary"></a>Скачать и установить двоичный клиент Istio istioctl

В Bash-оболочки на Linux или [Подсистемы Windows для Linux][install-wsl], использовать `curl` `tar` для загрузки релиза Istio, а затем извлечь из следующего:

```bash
# Specify the Istio version that will be leveraged throughout these instructions
ISTIO_VERSION=1.4.0

curl -sL "https://github.com/istio/istio/releases/download/$ISTIO_VERSION/istio-$ISTIO_VERSION-linux.tar.gz" | tar xz
```

Клиент `istioctl` двоичный работает на вашей клиентской машине и позволяет взаимодействовать с сеткой обслуживания Istio. Чтобы установить двоичный файл `istioctl` клиента Istio в оболочке bash в Linux или [подсистеме Linux для Windows][install-wsl], используйте следующие команды. Эти команды копируют двоичный файл клиента `istioctl` в стандартное расположение программ для пользователя в `PATH`.

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

<!-- LINKS - external -->
[install-wsl]: https://docs.microsoft.com/windows/wsl/install-win10