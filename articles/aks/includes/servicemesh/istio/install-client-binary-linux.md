---
author: paulbouwer
ms.topic: include
ms.date: 11/15/2019
ms.author: pabouwer
ms.openlocfilehash: b310de560f9791e1fc49d54dfbf0789c38d37f57
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "77594017"
---
## <a name="download-and-install-the-istio-istioctl-client-binary"></a>Скачивание и установка двоичного файла клиента Istio истиоктл

В оболочке на основе Bash в [подсистеме Linux или Windows для Linux][install-wsl]используйте `curl` для загрузки выпуска Istio, а затем извлеките `tar` с помощью следующим образом:

```bash
# Specify the Istio version that will be leveraged throughout these instructions
ISTIO_VERSION=1.4.0

curl -sL "https://github.com/istio/istio/releases/download/$ISTIO_VERSION/istio-$ISTIO_VERSION-linux.tar.gz" | tar xz
```

`istioctl` Клиентский двоичный файл выполняется на клиентском компьютере и позволяет взаимодействовать с сеткой службы Istio. Чтобы установить двоичный файл `istioctl` клиента Istio в оболочке bash в Linux или [подсистеме Linux для Windows][install-wsl], используйте следующие команды. Эти команды копируют двоичный файл клиента `istioctl` в стандартное расположение программ для пользователя в `PATH`.

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