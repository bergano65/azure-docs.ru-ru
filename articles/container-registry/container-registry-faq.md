---
title: Реестр контейнеров Azure — часто задаваемые вопросы
description: Ответы на часто задаваемые вопросы, связанные со службой реестр контейнеров Azure
services: container-registry
author: sajayantony
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 5/13/2019
ms.author: sajaya
ms.openlocfilehash: 86efb6b655405500f994a5a5ec7acbd18c645004
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2019
ms.locfileid: "65957855"
---
# <a name="frequently-asked-questions-about-azure-container-registry"></a>Часто задаваемые вопросы о реестре контейнеров Azure

В этой статье рассматриваются часто задаваемые вопросы и известные проблемы, о реестре контейнеров Azure.

## <a name="resource-management"></a>Управление ресурсами

- [Можно ли создать реестр контейнеров Azure с помощью шаблона Resource Manager?](#can-i-create-an-azure-container-registry-using-a-resource-manager-template)
- [Есть ли для образов в ACR сканирование уязвимостей безопасности?](#is-there-security-vulnerability-scanning-for-images-in-acr)
- [Как настроить Kubernetes с помощью реестра контейнеров Azure?](#how-do-i-configure-kubernetes-with-azure-container-registry)
- [Как получить учетные данные администратора для реестра контейнеров?](#how-do-i-get-admin-credentials-for-a-container-registry)
- [Как получить учетные данные администратора в шаблоне Resource Manager?](#how-do-i-get-admin-credentials-in-a-resource-manager-template)
- [Удаление репликации завершается сбоем с состоянием запрещено, несмотря на то, что репликация будет удалена с помощью Azure CLI или Azure PowerShell](#delete-of-replication-fails-with-forbidden-status-although-the-replication-gets-deleted-using-the-azure-cli-or-azure-powershell)

### <a name="can-i-create-an-azure-container-registry-using-a-resource-manager-template"></a>Можно ли создать реестр контейнеров Azure с помощью шаблона Resource Manager?

Да. Вот [шаблона](https://github.com/Azure/azure-cli/blob/master/src/command_modules/azure-cli-acr/azure/cli/command_modules/acr/template.json) , можно использовать для создания в реестр.

### <a name="is-there-security-vulnerability-scanning-for-images-in-acr"></a>Есть ли для образов в ACR сканирование уязвимостей безопасности?

Да. См. в документации [Twistlock](https://www.twistlock.com/2016/11/07/twistlock-supports-azure-container-registry/) и [Aqua](http://blog.aquasec.com/image-vulnerability-scanning-in-azure-container-registry).

### <a name="how-do-i-configure-kubernetes-with-azure-container-registry"></a>Как настроить Kubernetes с помощью реестра контейнеров Azure?

См. в документации [Kubernetes](http://kubernetes.io/docs/user-guide/images/#using-azure-container-registry-acr) и пошаговые инструкции по [службе Azure Kubernetes](container-registry-auth-aks.md).

### <a name="how-do-i-get-admin-credentials-for-a-container-registry"></a>Как получить учетные данные администратора для реестра контейнеров?

> [!IMPORTANT]
> Учетной записи администратора предназначена для одного пользователя для доступа к реестру, главным образом для целей тестирования. Не рекомендуется совместное использование учетных данных администратора несколькими пользователями. Для пользователей и субъектов-служб в сценариях автоматического входа рекомендуется использовать отдельные удостоверения. См. в разделе [проверка подлинности и авторизация](container-registry-authentication.md).

Прежде чем клиент получит учетные данные администратора, убедитесь, что администратор реестра включен.

Чтобы получить учетные данные с помощью Azure CLI:

```azurecli
az acr credential show -n myRegistry
```

С помощью Azure Powershell.

```powershell
Invoke-AzureRmResourceAction -Action listCredentials -ResourceType Microsoft.ContainerRegistry/registries -ResourceGroupName myResourceGroup -ResourceName myRegistry
```

### <a name="how-do-i-get-admin-credentials-in-a-resource-manager-template"></a>Как получить учетные данные администратора в шаблоне Resource Manager?

> [!IMPORTANT]
> Учетной записи администратора предназначена для одного пользователя для доступа к реестру, главным образом для целей тестирования. Не рекомендуется совместное использование учетных данных администратора несколькими пользователями. Для пользователей и субъектов-служб в сценариях автоматического входа рекомендуется использовать отдельные удостоверения. См. в разделе [проверка подлинности и авторизация](container-registry-authentication.md).

Прежде чем клиент получит учетные данные администратора, убедитесь, что администратор реестра включен.

Чтобы получить первый пароль:

```json
{
    "password": "[listCredentials(resourceId('Microsoft.ContainerRegistry/registries', 'myRegistry'), '2017-10-01').passwords[0].value]"
}
```

Чтобы получить второй пароль:

```json
{
    "password": "[listCredentials(resourceId('Microsoft.ContainerRegistry/registries', 'myRegistry'), '2017-10-01').passwords[1].value]"
}
```

### <a name="delete-of-replication-fails-with-forbidden-status-although-the-replication-gets-deleted-using-the-azure-cli-or-azure-powershell"></a>Удаление репликации завершается сбоем с состоянием запрещено, несмотря на то, что репликация будет удалена с помощью Azure CLI или Azure PowerShell

Ошибка возникает в том случае, если пользователь имеет разрешения для реестра, но не имеет разрешения чтения на уровне подписки. Чтобы устранить эту проблему, назначьте пользователю разрешения на чтение для подписки:


```azurecli  
az role assignment create --role "Reader" --assignee user@contoso.com --scope /subscriptions/<subscription_id> 
```

## <a name="registry-operations"></a>Операции с реестром

- [Как получить доступ к Docker реестра HTTP API версии 2?](#how-do-i-access-docker-registry-http-api-v2)
- [Как удалить все манифесты, на которые не ссылается любому тегу в репозитории](#how-do-i-delete-all-manifests-that-are-not-referenced-by-any-tag-in-a-repository)
- [Почему использование квоты реестра не приводит к уменьшению после удаления образов?](#why-does-the-registry-quota-usage-not-reduce-after-deleting-images)
- [Как проверить изменения квоты хранилища?](#how-do-i-validate-storage-quota-changes)
- [Как выполнить аутентификацию с помощью реестра при запуске интерфейса командной строки в контейнере?](#how-do-i-authenticate-with-my-registry-when-running-the-cli-in-a-container)
- [Реестр контейнеров Azure предлагает только конфигурации TLS версии 1.2 и как включить TLS версии 1.2?](#does-azure-container-registry-offer-tls-v12-only-configuration-and-how-to-enable-tls-v12)
- [Реестр контейнеров Azure поддерживает содержимого доверять?](#does-azure-container-registry-support-content-trust)
- [Как предоставить доступ к образам по запросу или принудительная без разрешения на управление ресурсами реестра?](#how-do-i-grant-access-to-pull-or-push-images-without-permission-to-manage-the-registry-resource)
- [Как включить карантин автоматической изображения для реестра](#how-do-i-enable-automatic-image-quarantine-for-a-registry)

### <a name="how-do-i-access-docker-registry-http-api-v2"></a>Как получить доступ к Docker реестра HTTP API версии 2?

ACR поддерживает API V2 HTTP реестра Docker. API-интерфейсы доступно по адресу `https://<your registry login server>/v2/`. Пример: `https://mycontainerregistry.azurecr.io/v2/`

### <a name="how-do-i-delete-all-manifests-that-are-not-referenced-by-any-tag-in-a-repository"></a>Как удалить все манифесты, на которые не ссылается любому тегу в репозитории

Если вы используете bash:

```bash
az acr repository show-manifests -n myRegistry --repository myRepository --query "[?tags[0]==null].digest" -o tsv  | xargs -I% az acr repository delete -n myRegistry -t myRepository@%
```

Для Powershell:

```powershell
az acr repository show-manifests -n myRegistry --repository myRepository --query "[?tags[0]==null].digest" -o tsv | %{ az acr repository delete -n myRegistry -t myRepository@$_ }
```

Примечание. Вы можете добавить `-y` в команду delete, чтобы пропустить подтверждение.

Дополнительные сведения см. в разделе [удалить образы контейнеров в реестре контейнеров Azure](container-registry-delete.md).

### <a name="why-does-the-registry-quota-usage-not-reduce-after-deleting-images"></a>Почему использование квоты реестра не приводит к уменьшению после удаления образов?

Это может происходить, если нижние слои по-прежнему ссылок на другие образы контейнеров. При удалении образа без ссылок, использования реестра обновляет через несколько минут.

### <a name="how-do-i-validate-storage-quota-changes"></a>Как проверить изменения квоты хранилища?

Создание образа с уровнем 1 ГБ, используя следующий файл docker. Это гарантирует, что образ имеет слой, который не является общим для любого другого изображения в реестре.

```dockerfile
FROM alpine
RUN dd if=/dev/urandom of=1GB.bin  bs=32M  count=32
RUN ls -lh 1GB.bin
```

Создайте и отправьте образ в реестр с помощью docker CLI.

```bash
docker build -t myregistry.azurecr.io/1gb:latest .
docker push myregistry.azurecr.io/1gb:latest
```

Можно видеть, что используемый объем хранилища увеличивается на портале Azure, или можно запросить использования с помощью интерфейса командной строки.

```bash
az acr show-usage -n myregistry
```

Удалить образ с помощью Azure CLI или портала и проверять обновленные использование через несколько минут.

```bash
az acr repository delete -n myregistry --image 1gb
```

### <a name="how-do-i-authenticate-with-my-registry-when-running-the-cli-in-a-container"></a>Как выполнить аутентификацию с помощью реестра при запуске интерфейса командной строки в контейнере?

Необходимо запустить контейнер интерфейса командной строки Azure, подключив сокет Docker:

```bash
docker run -it -v /var/run/docker.sock:/var/run/docker.sock azuresdk/azure-cli-python:dev
```

В контейнере, установить `docker`:

```bash
apk --update add docker
```

Затем проходить проверку подлинности в реестре:

```azurecli
az acr login -n MyRegistry
```

### <a name="does-azure-container-registry-offer-tls-v12-only-configuration-and-how-to-enable-tls-v12"></a>Реестр контейнеров Azure предлагает только конфигурации TLS версии 1.2 и как включить TLS версии 1.2?

Да. Включение протокола TLS с помощью последних клиента docker (версии 18.03.0 и выше). 

### <a name="does-azure-container-registry-support-content-trust"></a>Реестр контейнеров Azure поддерживает содержимого доверять?

Да, можно использовать доверенные образы в реестре контейнеров Azure, так как [Docker удостоверения](https://docs.docker.com/notary/getting_started/) интегрированное и может быть включен. Дополнительные сведения см. в разделе [содержимого доверия в реестре контейнеров Azure](container-registry-content-trust.md).


####  <a name="where-is-the-file-for-the-thumbprint-located"></a>Где находится файл, расположенный отпечаток?

В разделе `~/.docker/trust/tuf/myregistry.azurecr.io/myrepository/metadata`:

* Открытые ключи и сертификаты всех ролей (за исключением роли делегирование) хранятся в `root.json`.
* Открытые ключи и сертификаты делегирование роли хранятся в JSON-файл из его родительской роли (например `targets.json` для `targets/releases` роли).

Рекомендуется, чтобы проверить эти открытые ключи и сертификаты после общей проверки TUF сделать с помощью Docker и удостоверения клиента.

### <a name="how-do-i-grant-access-to-pull-or-push-images-without-permission-to-manage-the-registry-resource"></a>Как предоставить доступ к образам по запросу или принудительная без разрешения на управление ресурсами реестра?

Поддерживает ACR [пользовательские роли](container-registry-roles.md) , которые предоставляют различные уровни разрешений. В частности `AcrPull` и `AcrPush` роли позволяют пользователям по запросу или принудительной изображения без разрешения на управление реестра ресурса в Azure.

* Портал Azure: Реестр "->" Управление доступом (IAM) -> Добавить (выберите `AcrPull` или `AcrPush` для роли).
* Azure CLI: Найти идентификатор ресурса реестра, выполнив следующую команду:

  ```azurecli
  az acr show -n myRegistry
  ```
  
  Затем можно присвоить `AcrPull` или `AcrPush` роли для пользователя (в следующем примере используется `AcrPull`):

  ```azurecli
    az role assignment create --scope resource_id --role AcrPull --assignee user@example.com
    ```

  Или назначьте роль субъекта-службы, определяемое его ИД приложения:

  ```
  az role assignment create --scope resource_id --role AcrPull --assignee 00000000-0000-0000-0000-000000000000
  ```

Уполномоченное лицо будет иметь для проверки подлинности и доступа к образов в реестре.

* Для проверки подлинности в реестр:
    
  ```azurecli
  az acr login -n myRegistry 
  ```

* В списке репозиториев:

  ```azurecli
  az acr repository list -n myRegistry
  ```

 Чтобы извлечь образ:
    
  ```azurecli
  docker pull myregistry.azurecr.io/hello-world
  ```

С использованием только `AcrPull` или `AcrPush` роль, уполномоченного не имеет разрешения на управление реестра ресурса в Azure. Например `az acr list` или `az acr show -n myRegistry` могут не отобразиться в реестре.

### <a name="how-do-i-enable-automatic-image-quarantine-for-a-registry"></a>Как включить карантин автоматической изображения для реестра?

Изображение карантина в настоящее время — это функция предварительной версии ACR. Вы можете включить режим карантина реестра, чтобы только те изображения, которые успешно прошли проверку безопасности являются видимыми для обычных пользователей. Дополнительные сведения см. в разделе [репозиторий ACR GitHub](https://github.com/Azure/acr/tree/master/docs/preview/quarantine).

## <a name="diagnostics"></a>Диагностика

- [Вытягивание из docker завершается с ошибкой: net/http: запрос отменен во время ожидания подключения (превышено время, пока ожидаются заголовки Client.Timeout)](#docker-pull-fails-with-error-nethttp-request-canceled-while-waiting-for-connection-clienttimeout-exceeded-while-awaiting-headers)
- [docker Push-уведомлений успешно, но docker по запросу происходит сбой с ошибкой: несанкционированного: требуется проверка подлинности](#docker-push-succeeds-but-docker-pull-fails-with-error-unauthorized-authentication-required)
- [Включения и получения журналов отладки управляющей программы docker](#enable-and-get-the-debug-logs-of-the-docker-daemon) 
- [Новые разрешения пользователя не действовать сразу после обновления](#new-user-permissions-may-not-be-effective-immediately-after-updating)
- [Сведения о проверке подлинности не имеет неправильный формат на прямые вызовы REST API](#authentication-information-is-not-given-in-the-correct-format-on-direct-rest-api-calls)
- [Почему портала Azure не включает все репозитории или теги?](#why-does-the-azure-portal-not-list-all-my-repositories-or-tags)

### <a name="docker-pull-fails-with-error-nethttp-request-canceled-while-waiting-for-connection-clienttimeout-exceeded-while-awaiting-headers"></a>Вытягивание из docker завершается с ошибкой: net/http: запрос отменен во время ожидания подключения (превышено время, пока ожидаются заголовки Client.Timeout)

 - Если эта ошибка является временной, попытка будет успешной. 
 - Если `docker pull` сбое постоянно, тогда возможны проблемы с управляющей программы docker. Обычно проблему можно устранить путем перезапуска управляющей программы docker. 
 - Если вы продолжаете видеть эту проблему, перезапустите управляющую программу docker, проблема может быть некоторые проблемы с сетевым подключением с машиной. Чтобы проверить, является ли работоспособным общие сети на компьютере, повторите команду, такие как `ping www.bing.com`.
 - Вы должны всегда иметь механизм повтора на всех операций клиента docker.

### <a name="docker-push-succeeds-but-docker-pull-fails-with-error-unauthorized-authentication-required"></a>docker Push-уведомлений успешно, но docker по запросу происходит сбой с ошибкой: несанкционированного: требуется проверка подлинности

Эта ошибка может возникнуть в версии Red Hat управляющей программы docker, где `--signature-verification` включена по умолчанию. Параметры управляющей программы docker для Red Hat Enterprise Linux (RHEL) или Fedora можно просмотреть, выполнив следующую команду:

```bash
grep OPTIONS /etc/sysconfig/docker
```

Например Fedora 28 сервер имеет следующие параметры управляющей программы docker:

```
OPTIONS='--selinux-enabled --log-driver=journald --live-restore'
```

С помощью `--signature-verification=false` отсутствует, `docker pull` выдает сообщение об ошибке:

```bash
Trying to pull repository myregistry.azurecr.io/myimage ...
unauthorized: authentication required
```

Чтобы устранить эту ошибку, сделайте следующее:
1. Добавьте параметр `--signature-verification=false` в файл конфигурации управляющей программы docker `/etc/sysconfig/docker`. Например:

  ```
  OPTIONS='--selinux-enabled --log-driver=journald --live-restore --signature-verification=false'
  ```
2. Перезапустите службы управляющей программы docker, выполнив следующую команду:

  ```bash
  sudo systemctl restart docker.service
  ```

Сведения о `--signature-verification` можно найти, выполнив `man dockerd`.

### <a name="enable-and-get-the-debug-logs-of-the-docker-daemon"></a>Включения и получения журналов отладки управляющей программы docker  

Запуск `dockerd` с `debug` параметр. Во-первых, создайте файл конфигурации управляющей программы docker (`/etc/docker/daemon.json`), если он не существует и добавьте `debug` параметр:

```json
{   
    "debug": true   
}
```

Перезапустите управляющую программу. Например с Ubuntu 14.04:

```bash
sudo service docker restart
```

Сведения можно найти в [документации Docker](https://docs.docker.com/engine/admin/#enable-debugging). 

 * Журналы могут быть созданы в разных местах, в зависимости от вашей системы. Например, для Ubuntu 14.04 он имеет `/var/log/upstart/docker.log`.   
См. в разделе [документации Docker](https://docs.docker.com/engine/admin/#read-the-logs) сведения.    

 * Для Docker для Windows журналы создаются в каталоге % LOCALAPPDATA%/docker/. Тем не менее он не может содержать все отладочные данные еще.   

   Для доступа к журналу полный управляющей программы, может потребоваться несколько дополнительных действий.

    ```console
    docker run --privileged -it --rm -v /var/run/docker.sock:/var/run/docker.sock -v /usr/local/bin/docker:/usr/local/bin/docker alpine sh

    docker run --net=host --ipc=host --uts=host --pid=host -it --security-opt=seccomp=unconfined --privileged --rm -v /:/host alpine /bin/sh
    chroot /host
    ```
    Теперь у вас есть доступ ко всем файлам в виртуальной Машине, запущенной `dockerd`. Журнал находится в `/var/log/docker.log`.

### <a name="new-user-permissions-may-not-be-effective-immediately-after-updating"></a>Новые разрешения пользователя не действовать сразу после обновления

При предоставлении новых разрешений (новые роли) для субъекта-службы, изменения могут не вступают в силу немедленно. Есть две возможные причины:

* Задержка назначения ролей Azure Active Directory. Обычно это быстро, но может потребоваться минут из-за задержки распространения.
* Задержка разрешения на сервер маркера ACR. Это может занять до 10 минут. Чтобы устранить проблему, вы можете `docker logout` и затем снова пройти проверку подлинности с тем же пользователем через одну минуту:

  ```bash
  docker logout myregistry.azurecr.io
  docker login myregistry.azurecr.io
  ```

Сейчас записи контроля доступа не поддерживает удаление домашней репликации для пользователей. Обходной путь является включение домашней репликации создать в шаблоне, но пропустите его создания путем добавления `"condition": false` как показано ниже:

```json
{
    "name": "[concat(parameters('acrName'), '/', parameters('location'))]",
    "condition": false,
    "type": "Microsoft.ContainerRegistry/registries/replications",
    "apiVersion": "2017-10-01",
    "location": "[parameters('location')]",
    "properties": {},
    "dependsOn": [
        "[concat('Microsoft.ContainerRegistry/registries/', parameters('acrName'))]"
     ]
},
```

### <a name="authentication-information-is-not-given-in-the-correct-format-on-direct-rest-api-calls"></a>Сведения о проверке подлинности не имеет неправильный формат на прямые вызовы REST API

Могут возникнуть `InvalidAuthenticationInfo` ошибки, особенно с помощью `curl` средство с параметром `-L`, `--location` (чтобы следовать перенаправлениям).
Например, получение BLOB-объектов с помощью `curl` с `-L` параметр и обычной проверки подлинности:

```bash
curl -L -H "Authorization: basic $credential" https://$registry.azurecr.io/v2/$repository/blobs/$digest
```

может привести к следующим образом:

```xml
<?xml version="1.0" encoding="utf-8"?>
<Error><Code>InvalidAuthenticationInfo</Code><Message>Authentication information is not given in the correct format. Check the value of Authorization header.
RequestId:00000000-0000-0000-0000-000000000000
Time:2019-01-01T00:00:00.0000000Z</Message></Error>
```

Причина в том, что некоторые `curl` реализаций следовать перенаправлениям с заголовками из исходного запроса.

Чтобы устранить эту проблему, необходимо следовать перенаправлениям вручную без заголовков. Печать заголовков ответа с `-D -` параметр `curl` и извлеките: `Location` заголовка:

```bash
redirect_url=$(curl -s -D - -H "Authorization: basic $credential" https://$registry.azurecr.io/v2/$repository/blobs/$digest | grep "^Location: " | cut -d " " -f2 | tr -d '\r')
curl $redirect_url
```

### <a name="why-does-the-azure-portal-not-list-all-my-repositories-or-tags"></a>Почему портала Azure не включает все репозитории или теги? 

Если вы используете браузер Edge, вы увидите не более 100 репозитории или теги, отображаемые. Если реестр содержит более ста репозитории или теги, мы рекомендуем использовать Firefox или Chrome браузера, чтобы перечислить их все.

## <a name="tasks"></a>Задачи

- [Каким образом пакетная выполняется Отмена?](#how-do-i-batch-cancel-runs)
- [Как включить папку .git в команде сборки az acr?](#how-do-i-include-the-git-folder-in-az-acr-build-command)

### <a name="how-do-i-batch-cancel-runs"></a>Каким образом пакетная выполняется Отмена?

Следующие команды Отменить все выполняемые задачи в реестре задано.

```azurecli
az acr task list-runs -r $myregistry --run-status Running --query '[].runId' -o tsv \
| xargs -I% az acr task cancel-run -r $myregistry --run-id %
```

### <a name="how-do-i-include-the-git-folder-in-az-acr-build-command"></a>Как включить папку .git в команде сборки az acr?

Если передать папку локального источника для `az acr build` команде `.git` папку исключается из загруженного пакета по умолчанию. Можно создать `.dockerignore` файл со следующими параметрами. Он указывает команду, чтобы восстановить все файлы в `.git` в переданном пакете. 

```
!.git/**
```

Этот параметр также применяется к `az acr run` команды.

## <a name="cicd-integration"></a>Интеграция CI/CD

- [CircleCI](https://github.com/Azure/acr/blob/master/docs/integration/CircleCI.md)
- [Действия GitHub](https://github.com/Azure/acr/blob/master/docs/integration/github-actions/github-actions.md)


## <a name="next-steps"></a>Дальнейшие действия

* [Дополнительные сведения](container-registry-intro.md) о реестре контейнеров Azure.
