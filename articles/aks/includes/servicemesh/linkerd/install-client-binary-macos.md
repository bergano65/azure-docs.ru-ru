---
author: paulbouwer
ms.topic: include
ms.date: 10/09/2019
ms.author: pabouwer
ms.openlocfilehash: 876e05d7b18ac193edbc9cf842ea2c1bf0555d54
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77593746"
---
## <a name="download-and-install-the-linkerd-linkerd-client-binary"></a>Скачать и установить Linkerd linkerd клиент двоичный

В оболочке на основе Bash `curl` на MacOS, используйте для загрузки релиза Linkerd следующим образом:

```bash
# Specify the Linkerd version that will be leveraged throughout these instructions
LINKERD_VERSION=stable-2.6.0

curl -sLO "https://github.com/linkerd/linkerd2/releases/download/$LINKERD_VERSION/linkerd2-cli-$LINKERD_VERSION-darwin"
```

Клиент `linkerd` двоичный работает на вашей клиентской машине и позволяет взаимодействовать с сеткой службы Linkerd. Используйте следующие команды для установки двоичного клиента Linkerd `linkerd` в оболочку на основе Bash на MacOS. Эти команды копируют двоичный файл клиента `linkerd` в стандартное расположение программ для пользователя в `PATH`.

```bash
sudo cp ./linkerd2-cli-$LINKERD_VERSION-darwin /usr/local/bin/linkerd
sudo chmod +x /usr/local/bin/linkerd
```

Если вы хотите завершить командную строку `linkerd` для двоичного клиента Linkerd, то установите его следующим образом:

```bash
# Generate the bash completion file and source it in your current shell
mkdir -p ~/completions && linkerd completion bash > ~/completions/linkerd.bash
source ~/completions/linkerd.bash

# Source the bash completion file in your .bashrc so that the command-line completions
# are permanently available in your shell
echo "source ~/completions/linkerd.bash" >> ~/.bashrc
```
