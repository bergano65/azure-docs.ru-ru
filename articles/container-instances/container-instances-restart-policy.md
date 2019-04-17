---
title: Использование политик перезапуска для контейнерных задач в службе "Экземпляры контейнеров Azure"
description: Узнайте, как использовать Экземпляры контейнеров Azure для задач, выполняемых до завершения, включая сборку, тестирование или преобразование изображений.
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: article
ms.date: 04/15/2019
ms.author: danlep
ms.openlocfilehash: 06872eefd0d500a22214109ad5055dd236b5a6ac
ms.sourcegitcommit: 5f348bf7d6cf8e074576c73055e17d7036982ddb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2019
ms.locfileid: "59606843"
---
# <a name="run-containerized-tasks-with-restart-policies"></a>Выполнение контейнерных задач с помощью политики перезапуска

Простота и скорость развертывания контейнеров в службе "Экземпляры контейнеров Azure" позволяет выполнять в экземпляре контейнера такие разовые задачи, как сборка, тестирование или преобразование изображений.

Настраиваемая политика перезапуска позволяет указать, что контейнер нужно остановить после завершения всех его процессов. Так как работа экземпляров контейнеров оплачивается посекундно, в счет включаются только те вычислительные ресурсы, которые использовались во время выполнения контейнера с задачей.

В этой статье представлены примеры, в которых используется Azure CLI. Для их выполнения потребуется [локально установленная][azure-cli-install] версия Azure CLI 2.0.21 или выше. Кроме того, можно использовать CLI в [Azure Cloud Shell](../cloud-shell/overview.md).

## <a name="container-restart-policy"></a>Политика перезапуска контейнера

При создании [группы контейнеров](container-instances-container-groups.md) в службе "Экземпляры контейнеров Azure" вы можете выбрать один из трех режимов политики перезапуска.

| Политика перезапуска   | ОПИСАНИЕ |
| ---------------- | :---------- |
| `Always` | Контейнеры в группе контейнеров всегда перезапускаются. Эта политика применяется **по умолчанию**, если при создании контейнера не указать другую политику перезапуска. |
| `Never` | Контейнеры в группе контейнеров никогда не перезапускаются. Такие контейнеры будут выполнены не более одного раза. |
| `OnFailure` | Контейнеры в группе контейнеров перезапускаются, только если процесс в контейнере завершается ошибкой (то есть с ненулевым кодом выхода). Такие контейнеры будут выполнены не менее одного раза. |

## <a name="specify-a-restart-policy"></a>Указание политики перезапуска

Способ определения политики перезапуска зависит от способа создания экземпляра контейнера: с помощью Azure CLI, командлетов PowerShell Azure или портала Azure. В Azure CLI следует указать параметр `--restart-policy` при вызове команды [az container create][az-container-create].

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer \
    --image mycontainerimage \
    --restart-policy OnFailure
```

## <a name="run-to-completion-example"></a>Пример выполнения до завершения

Чтобы увидеть применение политики перезагрузки, создайте экземпляр контейнера Майкрософт [aci-wordcount] [ aci-wordcount-image] изображений и укажите `OnFailure` политику перезапуска. Контейнер из этого примера запускает скрипт Python, который по умолчанию анализирует текст пьесы [Гамлет](http://shakespeare.mit.edu/hamlet/full.html) Уильяма Шекспира, выдает в STDOUT 10 самых употребимых слов в тексте, и завершает работу.

Запустите этот контейнер с помощью команды [az container create][az-container-create], как показано ниже.

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer \
    --image mcr.microsoft.com/azuredocs/aci-wordcount:latest \
    --restart-policy OnFailure
```

Служба "Экземпляры контейнеров Azure" запускает контейнер, а затем останавливает его, когда завершится работа его приложения (или скрипта, как в нашем примере). Когда служба "Экземпляры контейнеров Azure" останавливает контейнер, для которого указана политика перезапуска `Never` или `OnFailure`, для состояния контейнера устанавливается значение **Terminated** (Завершено). Текущее состояние контейнера вы можете проверить с помощью команды [az container show][az-container-show], как показано ниже.

```azurecli-interactive
az container show --resource-group myResourceGroup --name mycontainer --query containers[0].instanceView.currentState.state
```

Выходные данные примера:

```bash
"Terminated"
```

Когда состояние контейнера примет значение *Terminated* (Завершено), вы сможете просмотреть выходные данные выполненной в нем задачи, используя журналы контейнера. Для просмотра выходных данных скрипта выполните команду [az container logs][az-container-logs], как показано ниже.

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer
```

Выходные данные:

```bash
[('the', 990),
 ('and', 702),
 ('of', 628),
 ('to', 610),
 ('I', 544),
 ('you', 495),
 ('a', 453),
 ('my', 441),
 ('in', 399),
 ('HAMLET', 386)]
```

В нашем примере вы увидите выходные данные, отправленные скриптом в STDOUT. Но задачи, заключенные в контейнер, могут записывать выходные данные в постоянное хранилище, чтобы их можно было использовать позднее. Например, их можно отправить в [файловый ресурс Azure](container-instances-mounting-azure-files-volume.md)

## <a name="next-steps"></a>Дальнейшие действия

Сценарии, основанные на задачах, такие как пакетная обработка большого набора данных с несколькими контейнерами, можно воспользоваться преимуществами custom [переменные среды](container-instances-environment-variables.md) или [командные строки](container-instances-start-command.md) во время выполнения.

Дополнительные сведения о том, как сохранить выходные данные контейнеров, которые выполняются до завершения, вы найдете в статье [Подключение файлового ресурса Azure с помощью Экземпляров контейнеров Azure](container-instances-mounting-azure-files-volume.md).

<!-- LINKS - External -->
[aci-wordcount-image]: https://hub.docker.com/_/microsoft-azuredocs-aci-wordcount

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container?view=azure-cli-latest#az-container-create
[az-container-logs]: /cli/azure/container?view=azure-cli-latest#az-container-logs
[az-container-show]: /cli/azure/container?view=azure-cli-latest#az-container-show
[azure-cli-install]: /cli/azure/install-azure-cli
