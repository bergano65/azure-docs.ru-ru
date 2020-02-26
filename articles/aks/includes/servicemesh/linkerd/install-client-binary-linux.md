---
author: paulbouwer
ms.topic: include
ms.date: 10/09/2019
ms.author: pabouwer
ms.openlocfilehash: 7a22256c88f9cee3ce62c68a2de4a5974d76f026
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/25/2020
ms.locfileid: "77593744"
---
## <a name="download-and-install-the-linkerd-linkerd-client-binary"></a>Скачивание и установка двоичного файла клиента Linkerd Linkerd

В оболочке на основе Bash в [подсистеме Linux или Windows для Linux][install-wsl]используйте `curl` для загрузки выпуска Linkerd следующим образом:

```bash
# Specify the Linkerd version that will be leveraged throughout these instructions
LINKERD_VERSION=stable-2.6.0

curl -sLO "https://github.com/linkerd/linkerd2/releases/download/$LINKERD_VERSION/linkerd2-cli-$LINKERD_VERSION-linux"
```

`linkerd` клиентский двоичный файл выполняется на клиентском компьютере и позволяет взаимодействовать с сеткой службы Linkerd. Используйте следующие команды для установки двоичного файла клиента Linkerd `linkerd` в оболочке на основе Bash в Linux или [подсистеме Windows для Linux][install-wsl]. Эти команды копируют двоичный файл клиента `linkerd` в стандартное расположение программ для пользователя в `PATH`.

```bash
sudo cp ./linkerd2-cli-$LINKERD_VERSION-linux /usr/local/bin/linkerd
sudo chmod +x /usr/local/bin/linkerd
```

Если вы хотите задать завершение командной строки для двоичного файла клиента Linkerd `linkerd`, настройте его следующим образом:

```bash
# Generate the bash completion file and source it in your current shell
mkdir -p ~/completions && linkerd completion bash > ~/completions/linkerd.bash
source ~/completions/linkerd.bash

# Source the bash completion file in your .bashrc so that the command-line completions
# are permanently available in your shell
echo "source ~/completions/linkerd.bash" >> ~/.bashrc
```

<!-- LINKS - external -->
[install-wsl]: https://docs.microsoft.com/windows/wsl/install-win10