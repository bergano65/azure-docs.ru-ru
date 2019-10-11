---
title: Реестр контейнеров Azure — часто задаваемые вопросы
description: Ответы на часто задаваемые вопросы, связанные со службой реестра контейнеров Azure
services: container-registry
author: sajayantony
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 07/02/2019
ms.author: sajaya
ms.openlocfilehash: cbdf4f203204df2d26eb7c908167c1e1ca10d0ab
ms.sourcegitcommit: b4665f444dcafccd74415fb6cc3d3b65746a1a31
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2019
ms.locfileid: "72264317"
---
# <a name="frequently-asked-questions-about-azure-container-registry"></a>Часто задаваемые вопросы о реестре контейнеров Azure

В этой статье рассматриваются часто задаваемые вопросы и известные проблемы реестра контейнеров Azure.

## <a name="resource-management"></a>Управление ресурсами

- [Можно ли создать реестр контейнеров Azure с помощью шаблона диспетчер ресурсов?](#can-i-create-an-azure-container-registry-using-a-resource-manager-template)
- [Есть ли проверка уязвимости системы безопасности на наличие образов в записи контроля доступа?](#is-there-security-vulnerability-scanning-for-images-in-acr)
- [Разделы справки настроить Kubernetes с помощью реестра контейнеров Azure?](#how-do-i-configure-kubernetes-with-azure-container-registry)
- [Разделы справки получить учетные данные администратора для реестра контейнеров?](#how-do-i-get-admin-credentials-for-a-container-registry)
- [Разделы справки получить учетные данные администратора в шаблоне диспетчер ресурсов?](#how-do-i-get-admin-credentials-in-a-resource-manager-template)
- [Удаление репликации завершается сбоем с запрещенным состоянием, хотя репликация удаляется с помощью Azure CLI или Azure PowerShell](#delete-of-replication-fails-with-forbidden-status-although-the-replication-gets-deleted-using-the-azure-cli-or-azure-powershell)
- [Правила брандмауэра успешно обновлены, но они не вступают в силу](#firewall-rules-are-updated-successfully-but-they-do-not-take-effect)

### <a name="can-i-create-an-azure-container-registry-using-a-resource-manager-template"></a>Можно ли создать реестр контейнеров Azure с помощью шаблона диспетчер ресурсов?

Да. Ниже приведен [шаблон](https://github.com/Azure/azure-quickstart-templates/tree/master/101-container-registry) , который можно использовать для создания реестра.

### <a name="is-there-security-vulnerability-scanning-for-images-in-acr"></a>Есть ли проверка уязвимости системы безопасности на наличие образов в записи контроля доступа?

Да. См. документацию по [Twistlock](https://www.twistlock.com/2016/11/07/twistlock-supports-azure-container-registry/) и [голубому цвету](https://blog.aquasec.com/image-vulnerability-scanning-in-azure-container-registry).

### <a name="how-do-i-configure-kubernetes-with-azure-container-registry"></a>Разделы справки настроить Kubernetes с помощью реестра контейнеров Azure?

См. документацию по [Kubernetes](https://kubernetes.io/docs/user-guide/images/#using-azure-container-registry-acr) и действиям для [службы Kubernetes Azure](../aks/cluster-container-registry-integration.md).

### <a name="how-do-i-get-admin-credentials-for-a-container-registry"></a>Разделы справки получить учетные данные администратора для реестра контейнеров?

> [!IMPORTANT]
> Учетная запись администратора разработана для доступа одного пользователя к реестру, в основном для целей тестирования. Не рекомендуется совместное использование учетных данных администратора несколькими пользователями. Для пользователей и субъектов-служб в сценариях автоматического входа рекомендуется использовать отдельные удостоверения. См. раздел [Обзор проверки подлинности](container-registry-authentication.md).

Перед получением учетных данных администратора убедитесь, что включен администратор реестра.

Чтобы получить учетные данные с помощью Azure CLI:

```azurecli
az acr credential show -n myRegistry
```

С помощью Azure PowerShell:

```powershell
Invoke-AzureRmResourceAction -Action listCredentials -ResourceType Microsoft.ContainerRegistry/registries -ResourceGroupName myResourceGroup -ResourceName myRegistry
```

### <a name="how-do-i-get-admin-credentials-in-a-resource-manager-template"></a>Разделы справки получить учетные данные администратора в шаблоне диспетчер ресурсов?

> [!IMPORTANT]
> Учетная запись администратора разработана для доступа одного пользователя к реестру, в основном для целей тестирования. Не рекомендуется совместное использование учетных данных администратора несколькими пользователями. Для пользователей и субъектов-служб в сценариях автоматического входа рекомендуется использовать отдельные удостоверения. См. раздел [Обзор проверки подлинности](container-registry-authentication.md).

Перед получением учетных данных администратора убедитесь, что включен администратор реестра.

Чтобы получить первый пароль, сделайте следующее:

```json
{
    "password": "[listCredentials(resourceId('Microsoft.ContainerRegistry/registries', 'myRegistry'), '2017-10-01').passwords[0].value]"
}
```

Чтобы получить второй пароль, сделайте следующее:

```json
{
    "password": "[listCredentials(resourceId('Microsoft.ContainerRegistry/registries', 'myRegistry'), '2017-10-01').passwords[1].value]"
}
```

### <a name="delete-of-replication-fails-with-forbidden-status-although-the-replication-gets-deleted-using-the-azure-cli-or-azure-powershell"></a>Удаление репликации завершается сбоем с запрещенным состоянием, хотя репликация удаляется с помощью Azure CLI или Azure PowerShell

Эта ошибка возникает, когда пользователь имеет разрешения на доступ к реестру, но не имеет разрешений уровня чтения для подписки. Чтобы устранить эту проблему, назначьте пользователю разрешения читателя для подписки:


```azurecli  
az role assignment create --role "Reader" --assignee user@contoso.com --scope /subscriptions/<subscription_id> 
```

### <a name="firewall-rules-are-updated-successfully-but-they-do-not-take-effect"></a>Правила брандмауэра успешно обновлены, но они не вступают в силу

Распространение изменений правил брандмауэра занимает некоторое время. После изменения параметров брандмауэра подождите несколько минут, прежде чем проверять это изменение.


## <a name="registry-operations"></a>Операции с реестром

- [Разделы справки доступ к API HTTP версии 2 реестра DOCKER?](#how-do-i-access-docker-registry-http-api-v2)
- [Разделы справки удалить все манифесты, на которые не ссылаются какие-либо теги в репозитории?](#how-do-i-delete-all-manifests-that-are-not-referenced-by-any-tag-in-a-repository)
- [Почему использование квоты реестра не уменьшается после удаления образов?](#why-does-the-registry-quota-usage-not-reduce-after-deleting-images)
- [Разделы справки проверить изменения квоты хранилища?](#how-do-i-validate-storage-quota-changes)
- [Разделы справки выполнить проверку подлинности с помощью реестра при запуске CLI в контейнере?](#how-do-i-authenticate-with-my-registry-when-running-the-cli-in-a-container)
- [Реестр контейнеров Azure предлагает конфигурацию только TLS v 1.2 и включение TLS v 1.2?](#does-azure-container-registry-offer-tls-v12-only-configuration-and-how-to-enable-tls-v12)
- [Поддерживает ли реестр контейнеров Azure отношение доверия с содержимым?](#does-azure-container-registry-support-content-trust)
- [Разделы справки предоставить доступ к образам или Push-уведомлениям без разрешения на управление ресурсом реестра?](#how-do-i-grant-access-to-pull-or-push-images-without-permission-to-manage-the-registry-resource)
- [Разделы справки включить автоматическое помещение образа в карантин для реестра](#how-do-i-enable-automatic-image-quarantine-for-a-registry)

### <a name="how-do-i-access-docker-registry-http-api-v2"></a>Разделы справки доступ к API HTTP версии 2 реестра DOCKER?

Запись контроля доступа поддерживает HTTP API версии 2 реестра DOCKER. Доступ к API можно получить по адресу `https://<your registry login server>/v2/`. Пример: `https://mycontainerregistry.azurecr.io/v2/`

### <a name="how-do-i-delete-all-manifests-that-are-not-referenced-by-any-tag-in-a-repository"></a>Разделы справки удалить все манифесты, на которые не ссылаются какие-либо теги в репозитории?

Если вы используете bash:

```bash
az acr repository show-manifests -n myRegistry --repository myRepository --query "[?tags[0]==null].digest" -o tsv  | xargs -I% az acr repository delete -n myRegistry -t myRepository@%
```

Для PowerShell:

```powershell
az acr repository show-manifests -n myRegistry --repository myRepository --query "[?tags[0]==null].digest" -o tsv | %{ az acr repository delete -n myRegistry -t myRepository@$_ }
```

Примечание. Вы можете добавить `-y` в команду Delete, чтобы пропустить подтверждение.

Дополнительные сведения см. [в статье Удаление образов контейнеров в реестре контейнеров Azure](container-registry-delete.md).

### <a name="why-does-the-registry-quota-usage-not-reduce-after-deleting-images"></a>Почему использование квоты реестра не уменьшается после удаления образов?

Такая ситуация может возникнуть, если на базовые слои по-прежнему ссылаются другие образы контейнеров. Если удалить образ без ссылок, использование реестра будет обновляться через несколько минут.

### <a name="how-do-i-validate-storage-quota-changes"></a>Разделы справки проверить изменения квоты хранилища?

Создайте образ с уровнем 1 ГБ, используя следующий файл DOCKER. Это гарантирует, что изображение будет иметь слой, который не является общим для любого другого образа в реестре.

```dockerfile
FROM alpine
RUN dd if=/dev/urandom of=1GB.bin  bs=32M  count=32
RUN ls -lh 1GB.bin
```

Создайте образ и отправьте его в реестр с помощью DOCKER CLI.

```bash
docker build -t myregistry.azurecr.io/1gb:latest .
docker push myregistry.azurecr.io/1gb:latest
```

Вы увидите, что использование хранилища увеличилось в портал Azure, или вы можете запросить использование с помощью интерфейса командной строки.

```bash
az acr show-usage -n myregistry
```

Удалите образ с помощью Azure CLI или портала и проверьте обновленное использование через несколько минут.

```bash
az acr repository delete -n myregistry --image 1gb
```

### <a name="how-do-i-authenticate-with-my-registry-when-running-the-cli-in-a-container"></a>Разделы справки выполнить проверку подлинности с помощью реестра при запуске CLI в контейнере?

Необходимо запустить контейнер Azure CLI, подключив сокет docker:

```bash
docker run -it -v /var/run/docker.sock:/var/run/docker.sock azuresdk/azure-cli-python:dev
```

В контейнере установите `docker`:

```bash
apk --update add docker
```

Затем выполните аутентификацию в реестре:

```azurecli
az acr login -n MyRegistry
```

### <a name="does-azure-container-registry-offer-tls-v12-only-configuration-and-how-to-enable-tls-v12"></a>Реестр контейнеров Azure предлагает конфигурацию только TLS v 1.2 и включение TLS v 1.2?

Да. Включите TLS с помощью любого последнего клиента DOCKER (версия 18.03.0 и выше). 

### <a name="does-azure-container-registry-support-content-trust"></a>Поддерживает ли реестр контейнеров Azure отношение доверия с содержимым?

Да, вы можете использовать надежные образы в реестре контейнеров Azure, так как [DOCKER Нотари](https://docs.docker.com/notary/getting_started/) интегрирован и может быть включен. Дополнительные сведения см. [в статье о доверии содержимого в реестре контейнеров Azure](container-registry-content-trust.md).


####  <a name="where-is-the-file-for-the-thumbprint-located"></a>Где находится файл для отпечатка?

В разделе `~/.docker/trust/tuf/myregistry.azurecr.io/myrepository/metadata`:

* Открытые ключи и сертификаты всех ролей (за исключением ролей делегирования) хранятся в `root.json`.
* Открытые ключи и сертификаты роли делегирования хранятся в файле JSON своей родительской роли (например, `targets.json` для роли `targets/releases`).

Рекомендуется проверить эти открытые ключи и сертификаты после общей проверки туф, выполненной клиентом DOCKER и Нотари.

### <a name="how-do-i-grant-access-to-pull-or-push-images-without-permission-to-manage-the-registry-resource"></a>Разделы справки предоставить доступ к образам или Push-уведомлениям без разрешения на управление ресурсом реестра?

Запись контроля доступа поддерживает [пользовательские роли](container-registry-roles.md) , предоставляющие различные уровни разрешений. В частности, `AcrPull` и `AcrPush` позволяют пользователям получать и (или) отправлять изображения без разрешения на управление ресурсом реестра в Azure.

* Портал Azure: Реестр — > управления доступом (IAM) — > Добавить (выберите `AcrPull` или `AcrPush` для роли).
* Azure CLI: Найдите идентификатор ресурса реестра, выполнив следующую команду:

  ```azurecli
  az acr show -n myRegistry
  ```
  
  Затем можно назначить пользователю роль `AcrPull` или `AcrPush` (в следующем примере используется `AcrPull`):

  ```azurecli
    az role assignment create --scope resource_id --role AcrPull --assignee user@example.com
    ```

  Или назначьте роль субъекту-службе, определяемому ИДЕНТИФИКАТОРом приложения:

  ```
  az role assignment create --scope resource_id --role AcrPull --assignee 00000000-0000-0000-0000-000000000000
  ```

Затем уполномоченные могут проходить проверку подлинности и получать доступ к изображениям в реестре.

* Для проверки подлинности в реестре:
    
  ```azurecli
  az acr login -n myRegistry 
  ```

* Вывод списка репозиториев:

  ```azurecli
  az acr repository list -n myRegistry
  ```

 Чтобы извлечь изображение, сделайте следующее:
    
  ```azurecli
  docker pull myregistry.azurecr.io/hello-world
  ```

При использовании только роли `AcrPull` или `AcrPush` у уполномоченного не будет разрешения на управление ресурсом реестра в Azure. Например, `az acr list` или `az acr show -n myRegistry` не будет показывать реестр.

### <a name="how-do-i-enable-automatic-image-quarantine-for-a-registry"></a>Разделы справки включить автоматическое помещение образа в карантин для реестра?

Карантин изображений в настоящее время является предварительной версией функции записи контроля доступа. Можно включить режим карантина для реестра, чтобы только те образы, которые успешно прошли проверку безопасности, были видны обычным пользователям. Дополнительные сведения см. в [репозитории GitHub](https://github.com/Azure/acr/tree/master/docs/preview/quarantine)для записи контроля доступа.

## <a name="diagnostics-and-health-checks"></a>Диагностика и проверка работоспособности

- [Проверка работоспособности с `az acr check-health`](#check-health-with-az-acr-check-health)
- [Сбой docker pull с ошибкой: NET/http: запрос отменен при ожидании соединения (превышение времени ожидания клиента.](#docker-pull-fails-with-error-nethttp-request-canceled-while-waiting-for-connection-clienttimeout-exceeded-while-awaiting-headers)
- [Отправка DOCKER завершается успешно, но docker pull завершается ошибкой: не санкционировано: требуется проверка подлинности](#docker-push-succeeds-but-docker-pull-fails-with-error-unauthorized-authentication-required)
- [Включение и получение журналов отладки управляющей программы DOCKER](#enable-and-get-the-debug-logs-of-the-docker-daemon) 
- [Новые разрешения пользователя могут не действовать сразу после обновления](#new-user-permissions-may-not-be-effective-immediately-after-updating)
- [Данные проверки подлинности не задаются в правильном формате при вызове Direct REST API](#authentication-information-is-not-given-in-the-correct-format-on-direct-rest-api-calls)
- [Почему портал Azure не содержит список всех репозиториев или тегов?](#why-does-the-azure-portal-not-list-all-my-repositories-or-tags)
- [Почему портал Azure не удается получить репозитории или Теги?](#why-does-the-azure-portal-fail-to-fetch-repositories-or-tags)
- [Разделы справки собирайте трассировки HTTP в Windows?](#how-do-i-collect-http-traces-on-windows)

### <a name="check-health-with-az-acr-check-health"></a>Проверка работоспособности с `az acr check-health`

Сведения об устранении распространенных проблем среды и реестра см. в статье [Проверка работоспособности реестра контейнеров Azure](container-registry-check-health.md).

### <a name="docker-pull-fails-with-error-nethttp-request-canceled-while-waiting-for-connection-clienttimeout-exceeded-while-awaiting-headers"></a>Сбой docker pull с ошибкой: NET/http: запрос отменен при ожидании соединения (превышение времени ожидания клиента.

 - Если эта ошибка является временной, повторная попытка будет выполнена.
 - Если `docker pull` постоянно завершается сбоем, возможно, возникла проблема с управляющей программой DOCKER. Как правило, проблему можно устранить путем перезапуска управляющей программы DOCKER. 
 - Если эта проблема продолжает возникать после перезапуска управляющей программы DOCKER, проблема может быть связана с неполадками с сетевым подключением к компьютеру. Чтобы проверить работоспособность общей сети на компьютере, выполните следующую команду, чтобы проверить подключение к конечной точке. Минимальная версия `az acr`, содержащая эту команду проверки подключения, — 2.2.9. Обновите Azure CLI, если используется более старая версия.
 
   ```azurecli
    az acr check-health -n myRegistry
    ```
 - Всегда следует использовать механизм повтора во всех операциях клиента DOCKER.

### <a name="docker-pull-is-slow"></a>Слишком большое извлечение DOCKER
Используйте [это](http://www.azurespeed.com/Azure/Download) средство для проверки скорости загрузки сети компьютера. Если сеть компьютера работает слишком долго, попробуйте использовать виртуальную машину Azure в том же регионе, что и реестр. Обычно это обеспечивает более быструю скорость сети.

### <a name="docker-push-is-slow"></a>Задержка принудительной отправки DOCKER
Используйте [это](http://www.azurespeed.com/Azure/Upload) средство для проверки скорости передачи сети компьютера. Если сеть компьютера работает слишком долго, попробуйте использовать виртуальную машину Azure в том же регионе, что и реестр. Обычно это обеспечивает более быструю скорость сети.

### <a name="docker-push-succeeds-but-docker-pull-fails-with-error-unauthorized-authentication-required"></a>Отправка DOCKER завершается успешно, но docker pull завершается ошибкой: не санкционировано: требуется проверка подлинности

Эта ошибка может возникать при использовании версии Red Hat управляющей программы DOCKER, где `--signature-verification` включен по умолчанию. Можно проверить параметры управляющей программы DOCKER для Red Hat Enterprise Linux (RHEL) или Fedora, выполнив следующую команду:

```bash
grep OPTIONS /etc/sysconfig/docker
```

Например, Fedora 28 Server имеет следующие параметры управляющей программы docker:

```
OPTIONS='--selinux-enabled --log-driver=journald --live-restore'
```

При отсутствии `--signature-verification=false` `docker pull` завершается ошибкой, как показано ниже.

```bash
Trying to pull repository myregistry.azurecr.io/myimage ...
unauthorized: authentication required
```

Чтобы устранить эту ошибку, сделайте следующее:
1. Добавьте параметр `--signature-verification=false` в файл конфигурации управляющей программы DOCKER `/etc/sysconfig/docker`. Пример:

  ```
  OPTIONS='--selinux-enabled --log-driver=journald --live-restore --signature-verification=false'
  ```
2. Перезапустите службу управляющей программы DOCKER, выполнив следующую команду:

  ```bash
  sudo systemctl restart docker.service
  ```

Сведения о `--signature-verification` можно найти, выполнив `man dockerd`.

### <a name="enable-and-get-the-debug-logs-of-the-docker-daemon"></a>Включение и получение журналов отладки управляющей программы DOCKER  

Запустите `dockerd` с параметром `debug`. Сначала создайте файл конфигурации управляющей программы DOCKER (`/etc/docker/daemon.json`), если он не существует, и добавьте параметр `debug`:

```json
{   
    "debug": true   
}
```

Затем перезапустите управляющую программу. Например, в Ubuntu 14,04:

```bash
sudo service docker restart
```

Подробные сведения можно найти в [документации по DOCKER](https://docs.docker.com/engine/admin/#enable-debugging). 

 * Журналы могут быть созданы в разных местах в зависимости от используемой системы. Например, для Ubuntu 14,04 это `/var/log/upstart/docker.log`.   
Дополнительные сведения см. в [документации по DOCKER](https://docs.docker.com/engine/admin/#read-the-logs) .    

 * Для Docker для Windows журналы создаются в папке% LOCALAPPDATA%/доккер/. Однако он еще не может содержать всю отладочную информацию.   

   Для доступа к полному журналу управляющей программы могут потребоваться некоторые дополнительные действия.

    ```console
    docker run --privileged -it --rm -v /var/run/docker.sock:/var/run/docker.sock -v /usr/local/bin/docker:/usr/local/bin/docker alpine sh

    docker run --net=host --ipc=host --uts=host --pid=host -it --security-opt=seccomp=unconfined --privileged --rm -v /:/host alpine /bin/sh
    chroot /host
    ```
    Теперь у вас есть доступ ко всем файлам виртуальной машины, на которых выполняется `dockerd`. Журнал находится в `/var/log/docker.log`.

### <a name="new-user-permissions-may-not-be-effective-immediately-after-updating"></a>Новые разрешения пользователя могут не действовать сразу после обновления

При предоставлении новых разрешений (новых ролей) субъекту-службе изменение может не вступить в силу немедленно. Существует две возможные причины.

* Задержка назначения роли Azure Active Directory. Обычно это происходит быстро, но может занять несколько минут из-за задержки распространения.
* Задержка разрешений на сервере маркеров записи контроля доступа. Это может занять до 10 минут. Чтобы устранить эту проблемы, можно `docker logout`, а затем снова выполнить проверку подлинности с тем же пользователем через 1 минуту:

  ```bash
  docker logout myregistry.azurecr.io
  docker login myregistry.azurecr.io
  ```

В настоящее время запись контроля доступа пользователей не поддерживает удаление домашних репликаций. Обходной путь состоит в том, чтобы включить в шаблон главную репликацию, но пропустить ее создание, добавив `"condition": false`, как показано ниже:

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

### <a name="authentication-information-is-not-given-in-the-correct-format-on-direct-rest-api-calls"></a>Данные проверки подлинности не задаются в правильном формате при вызове Direct REST API

Может возникнуть ошибка `InvalidAuthenticationInfo`, особенно при использовании средства `curl` с параметром `-L`, `--location` (чтобы следовать перенаправлениям).
Например, можно получить большой двоичный объект, используя `curl` с параметром `-L` и обычной проверкой подлинности:

```bash
curl -L -H "Authorization: basic $credential" https://$registry.azurecr.io/v2/$repository/blobs/$digest
```

может привести к следующему ответу:

```xml
<?xml version="1.0" encoding="utf-8"?>
<Error><Code>InvalidAuthenticationInfo</Code><Message>Authentication information is not given in the correct format. Check the value of Authorization header.
RequestId:00000000-0000-0000-0000-000000000000
Time:2019-01-01T00:00:00.0000000Z</Message></Error>
```

Основная причина заключается в том, что некоторые реализации `curl` следуют перенаправлениям с заголовками исходного запроса.

Чтобы устранить эту проблему, необходимо следовать перенаправлениям вручную без заголовков. Распечатайте заголовки ответа с параметром `-D -` `curl` и затем Extract: `Location`:

```bash
redirect_url=$(curl -s -D - -H "Authorization: basic $credential" https://$registry.azurecr.io/v2/$repository/blobs/$digest | grep "^Location: " | cut -d " " -f2 | tr -d '\r')
curl $redirect_url
```

### <a name="why-does-the-azure-portal-not-list-all-my-repositories-or-tags"></a>Почему портал Azure не содержит список всех репозиториев или тегов? 

Если вы используете браузер Microsoft ребр/IE, можно увидеть не более 100 репозиториев или тегов. Если в реестре более 100 репозиториев или тегов, для их перечисления рекомендуется использовать браузер Firefox или Chrome.

### <a name="why-does-the-azure-portal-fail-to-fetch-repositories-or-tags"></a>Почему портал Azure не удается получить репозитории или Теги?

Возможно, браузер не сможет отправить запрос на получение репозиториев или тегов на сервер. Возможны различные причины:

* Отсутствие подключения к сети
* Брандмауэр
* Блокирование рекламы
* Ошибки DNS

Обратитесь к администратору сети или проверьте конфигурацию сети и подключение. Кроме того, в браузере можно попытаться использовать режиме инкогнито или частный сеанс, чтобы избежать устаревших кэшей браузера или файлов cookie.

### <a name="how-do-i-collect-http-traces-on-windows"></a>Разделы справки собирайте трассировки HTTP в Windows?

#### <a name="prerequisites"></a>Предварительные требования

- Включение расшифровки HTTPS в Fiddler: <https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS>
- Включение DOCKER для использования прокси-сервера через пользовательский интерфейс docker: <https://docs.docker.com/docker-for-windows/#proxies>
- Не забудьте выполнить откат после завершения.  DOCKER не будет работать, если этот параметр включен и Fiddler не работает.

#### <a name="windows-containers"></a>Контейнеры Windows

Настройка прокси-сервера DOCKER в значение 127.0.0.1:8888

#### <a name="linux-containers"></a>Контейнеры Linux

Найдите IP-адрес виртуального коммутатора виртуальной машины docker:

```powershell
(Get-NetIPAddress -InterfaceAlias "*Docker*" -AddressFamily IPv4).IPAddress
```

Настройте прокси-сервер DOCKER для вывода предыдущей команды и порта 8888 (например, 10.0.75.1:8888).

## <a name="tasks"></a>Задачи

- [Разделы справки выполнения пакетной отмены?](#how-do-i-batch-cancel-runs)
- [Разделы справки включить папку Git в команду AZ контроля доступа?](#how-do-i-include-the-git-folder-in-az-acr-build-command)

### <a name="how-do-i-batch-cancel-runs"></a>Разделы справки выполнения пакетной отмены?

Следующие команды отменяют все выполняемые задачи в указанном реестре.

```azurecli
az acr task list-runs -r $myregistry --run-status Running --query '[].runId' -o tsv \
| xargs -I% az acr task cancel-run -r $myregistry --run-id %
```

### <a name="how-do-i-include-the-git-folder-in-az-acr-build-command"></a>Разделы справки включить папку Git в команду AZ контроля доступа?

При передаче локальной исходной папки в команду `az acr build` папка `.git` будет исключена из переданного пакета по умолчанию. Вы можете создать файл `.dockerignore` со следующим параметром. Она сообщает команде восстановить все файлы в папке `.git` в отправленном пакете. 

```
!.git/**
```

Этот параметр также применяется к команде `az acr run`.

## <a name="cicd-integration"></a>Интеграция CI/CD

- [ЦирклеЦи](https://github.com/Azure/acr/blob/master/docs/integration/CircleCI.md)
- [Действия GitHub](https://github.com/Azure/acr/blob/master/docs/integration/github-actions/github-actions.md)

## <a name="next-steps"></a>Следующие шаги

* [Дополнительные сведения](container-registry-intro.md) о реестре контейнеров Azure.
