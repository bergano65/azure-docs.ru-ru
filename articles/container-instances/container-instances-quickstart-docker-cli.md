---
title: Краткое руководство. Развертывание контейнера Docker в экземпляр контейнеров — Docker CLI
description: В этом кратком руководстве описано, как быстро развернуть контейнерное веб-приложение, выполняющееся в изолированном экземпляре контейнера Azure с помощью Docker CLI
ms.topic: quickstart
ms.date: 07/16/2020
ms.custom: devx-track-javascript
ms.openlocfilehash: 0e4569904ef6aee304518ce012889d10dc2ecbce
ms.sourcegitcommit: 42107c62f721da8550621a4651b3ef6c68704cd3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/29/2020
ms.locfileid: "87408090"
---
# <a name="quickstart-deploy-a-container-instance-in-azure-using-the-docker-cli"></a>Краткое руководство. Развертывание экземпляра контейнера в Azure с помощью Docker CLI

Служба "Экземпляры контейнеров Azure" позволяет легко и быстро запускать бессерверные контейнеры Docker в Azure. Выполняйте развертывание в экземпляр контейнера по запросу, если разрабатываете облачные приложения и хотите легко переходить с локальной разработки на облачное развертывание.

В этом кратком руководстве вы используете собственные команды Docker CLI, чтобы развернуть контейнер Docker и сделать его приложение доступным в Экземплярах контейнеров. Эта возможность предоставляется благодаря [интеграции между Docker и Azure](https://docs.docker.com/engine/context/aci-integration/) (бета-версия). Через несколько секунд после выполнения команды `docker run` вы можете перейти к приложению, которое выполняется в контейнере:

:::image type="content" source="media/container-instances-quickstart-docker-cli/view-application-running-in-an-azure-container-instance.png" alt-text="Приложение, развернутое с помощью службы "Экземпляры контейнеров Azure" (просмотр в браузере)":::

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись][azure-account], прежде чем начинать работу.

Для целей этого краткого руководства вам понадобится установить Docker Desktop Edge 2.3.2.0 или более поздней версии для [Windows](https://desktop.docker.com/win/edge/Docker%20Desktop%20Installer.exe) или [macOS](https://desktop.docker.com/mac/edge/Docker.dmg). Или установите [интерфейс командной строки интеграции Docker ACI для Linux](https://docs.docker.com/engine/context/aci-integration/#install-the-docker-aci-integration-cli-on-linux) (бета-версия). 

> [!IMPORTANT]
> Эта функция сейчас доступна в режиме предварительной версии и для нее в Docker нужно использовать функции бета-версии (предварительная версия). Узнайте больше о [стабильных и граничных версиях Docker Desktop](https://docs.docker.com/desktop/#stable-and-edge-versions). Поддерживаются не все функции Экземпляров контейнеров Azure. Предоставьте отзыв об интеграции Docker-Azure, создав вопрос в репозитории GitHub для [aci-integration-beta](https://github.com/docker/aci-integration-beta).

## <a name="create-azure-context"></a>Создание контекста Azure

Чтобы использовать команды Docker для запуска контейнеров в службе "Экземпляры контейнеров Azure", сначала войдите в Azure:

```bash
docker login azure
```

При появлении запроса введите или выберите свои учетные данные Azure.


Запустите `docker context create aci`, чтобы создать контекст ACI. Этот контекст связывает Docker с подпиской Azure, чтобы предоставить возможность создания экземпляров контейнеров. Например, чтобы создать контекст с именем *myacicontext*:

```
docker context create aci myacicontext
```

При появлении запроса выберите идентификатор подписки Azure, а затем существующую группу ресурсов или **создайте новую**. Если вы выбираете создание новой группы ресурсов, ее имя будет задано системой. Экземпляры контейнеров Azure, как и все ресурсы Azure, должны быть развернуты в группе ресурсов. Группы ресурсов позволяют организовать соответствующие ресурсы Azure и управлять ими.


Запустите `docker context ls`, чтобы убедиться, что вы добавили контекст ACI в контексты Docker:

```
docker context ls
```

## <a name="create-a-container"></a>Создание контейнера

Создав контекст Docker, можно приступить к созданию контейнера в Azure. В этом кратком руководстве используется общедоступный образ `mcr.microsoft.com/azuredocs/aci-helloworld`. Этот образ содержит небольшое веб-приложение, написанное на Node.js, которое обслуживает статические HTML-страницы.

Сначала перейдите в контекст ACI. В этом контексте выполняются все последующие команды Docker.

```
docker context use myacicontext
```

Выполните команду `docker run`, чтобы создать экземпляр контейнера Azure с портом 80, доступным в Интернете.

```
docker run -p 80:80 mcr.microsoft.com/azuredocs/aci-helloworld
```

Пример выходных данных при успешном развертывании:

```
[+] Running 2/2
 ⠿ hungry-kirch            Created                                                                               5.1s
 ⠿ single--container--aci  Done                                                                                 11.3s
hungry-kirch
```

Запустите `docker ps`, чтобы получить сведения о выполняющемся контейнере и общедоступном IP-адресе:

```
docker ps
```


В примере выходных данных показан общедоступный IP-адрес, в данном случае *52.230.225.232*:

```
CONTAINER ID        IMAGE                                        COMMAND             STATUS              PORTS
hungry-kirch        mcr.microsoft.com/azuredocs/aci-helloworld                       Running             52.230.225.232:80->80/tcp
```

 Затем перейдите по IP-адресу в браузере. Если появится примерно такая веб-страница, поздравляем! Вы успешно развернули приложение, выполняющееся в контейнере Docker в Azure.

:::image type="content" source="media/container-instances-quickstart-docker-cli/view-application-running-in-an-azure-container-instance.png" alt-text="Приложение, развернутое с помощью службы "Экземпляры контейнеров Azure" (просмотр в браузере)":::

## <a name="pull-the-container-logs"></a>Извлечение журналов контейнера

При необходимости устранения неполадок с контейнером или запущенным в нем приложением (или просто чтобы просмотреть выходные данные) начните с просмотра журналов экземпляра контейнера.

Например, выполните команду `docker logs`, чтобы просмотреть журналы контейнера *hungry-kirch* в контексте ACI:

```azurecli-interactive
docker logs hungry-kirch
```

В выходных данных будут содержаться журналы для контейнера, а также должны отобразиться запросы HTTP GET, созданные при просмотре приложения в браузере.

```output
listening on port 80
::ffff:10.240.255.55 - - [07/Jul/2020:17:43:53 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.121 Safari/537.36"
::ffff:10.240.255.55 - - [07/Jul/2020:17:44:36 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.121 Safari/537.36"
::ffff:10.240.255.55 - - [07/Jul/2020:17:44:36 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.121 Safari/537.36"
```


## <a name="clean-up-resources"></a>Очистка ресурсов

Завершив работу с контейнером, запустите `docker rm`, чтобы удалить его. Эта команда останавливает и удаляет экземпляр контейнера Azure.

```
docker rm hungry-kirch
```


## <a name="next-steps"></a>Дальнейшие действия

В этом кратком руководстве вы создали экземпляр контейнера Azure из общедоступного образа, используя интеграцию между Docker и Azure. Дополнительные сведения о сценариях интеграции см. в [документации по Docker](https://docs.docker.com/engine/context/aci-integration/). 

Кроме того, вы можете использовать [расширение Docker](https://aka.ms/VSCodeDocker) для Visual Studio Code, чтобы получить интегрированный интерфейс для разработки, запуска контейнеров, изображений и контекстов, а также управления ими.

Чтобы использовать средства Azure для создания экземпляров контейнеров и управления ими, ознакомьтесь с другими краткими руководствами об использовании [Azure CLI](container-instances-quickstart.md), [Azure PowerShell](container-instances-quickstart-powershell.md), [портала Azure](container-instances-quickstart-portal.md) и [шаблонов Azure Resource Manager](container-instances-quickstart-template.md).

Если вы хотите создать образ контейнера и развернуть его через частный реестр контейнеров Azure, перейдите к руководству по использованию службы "Экземпляры контейнеров Azure".

> [!div class="nextstepaction"]
> [Руководство по использованию службы "Экземпляры контейнеров Azure"](./container-instances-tutorial-prepare-app.md)

<!-- LINKS - External -->

[azure-account]: https://azure.microsoft.com/free/
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

