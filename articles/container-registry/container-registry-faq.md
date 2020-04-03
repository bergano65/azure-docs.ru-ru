---
title: Вопросы и ответы
description: Ответы на часто задаваемые вопросы, связанные со службой регистрации контейнеров Azure
author: sajayantony
ms.topic: article
ms.date: 03/18/2020
ms.author: sajaya
ms.openlocfilehash: 7452b5dd3c952a13a28566914d2fe513689d4751
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/02/2020
ms.locfileid: "80618794"
---
# <a name="frequently-asked-questions-about-azure-container-registry"></a>Часто задаваемые вопросы о реестре контейнеров Azure

В этой статье часто рассматриваются часто задаваемые вопросы и известные вопросы о реестре контейнеров Azure.

## <a name="resource-management"></a>Управление ресурсами

- [Можно ли создать реестр контейнеров Azure с помощью шаблона «Менеджер ресурсов»?](#can-i-create-an-azure-container-registry-using-a-resource-manager-template)
- [Существует ли сканирование уязвимостей безопасности для изображений в ACR?](#is-there-security-vulnerability-scanning-for-images-in-acr)
- [Как настроить Kubernetes с помощью реестра контейнеров Azure?](#how-do-i-configure-kubernetes-with-azure-container-registry)
- [Как получить учетные данные админа для реестра контейнеров?](#how-do-i-get-admin-credentials-for-a-container-registry)
- [Как получить учетные данные admin в шаблоне менеджера ресурсов?](#how-do-i-get-admin-credentials-in-a-resource-manager-template)
- [Удаление репликации не удается с запретным статусом, хотя репликация удаляется с помощью Azure CLI или Azure PowerShell](#delete-of-replication-fails-with-forbidden-status-although-the-replication-gets-deleted-using-the-azure-cli-or-azure-powershell)
- [Правила брандмауэра успешно обновляются, но они не всплываются](#firewall-rules-are-updated-successfully-but-they-do-not-take-effect)

### <a name="can-i-create-an-azure-container-registry-using-a-resource-manager-template"></a>Можно ли создать реестр контейнеров Azure с помощью шаблона «Менеджер ресурсов»?

Да. Вот [шаблон,](https://github.com/Azure/azure-quickstart-templates/tree/master/101-container-registry) который можно использовать для создания реестра.

### <a name="is-there-security-vulnerability-scanning-for-images-in-acr"></a>Существует ли сканирование уязвимостей безопасности для изображений в ACR?

Да. Ознакомьтесь с документацией из [Центра безопасности Azure,](https://docs.microsoft.com/azure/security-center/azure-container-registry-integration) [Twistlock](https://www.twistlock.com/2016/11/07/twistlock-supports-azure-container-registry/) и [Aqua.](https://blog.aquasec.com/image-vulnerability-scanning-in-azure-container-registry)

### <a name="how-do-i-configure-kubernetes-with-azure-container-registry"></a>Как настроить Kubernetes с помощью реестра контейнеров Azure?

Ознакомьтесь с документацией для [Kubernetes](https://kubernetes.io/docs/user-guide/images/#using-azure-container-registry-acr) и шагами для [сервиса Azure Kubernetes.](../aks/cluster-container-registry-integration.md)

### <a name="how-do-i-get-admin-credentials-for-a-container-registry"></a>Как получить учетные данные админа для реестра контейнеров?

> [!IMPORTANT]
> Учетная запись пользователя admin предназначена для того, чтобы один пользователь был готов получить доступ к реестру, главным образом для целей тестирования. Не рекомендуется совместное использование учетных данных администратора несколькими пользователями. Для пользователей и субъектов-служб в сценариях автоматического входа рекомендуется использовать отдельные удостоверения. Смотрите [обзор аутентификации](container-registry-authentication.md).

Прежде чем получить учетные данные от аммиака, убедитесь, что пользователь админ реестра включен.

Чтобы получить учетные данные с помощью Azure CLI:

```azurecli
az acr credential show -n myRegistry
```

Использование Azure Powershell:

```powershell
Invoke-AzureRmResourceAction -Action listCredentials -ResourceType Microsoft.ContainerRegistry/registries -ResourceGroupName myResourceGroup -ResourceName myRegistry
```

### <a name="how-do-i-get-admin-credentials-in-a-resource-manager-template"></a>Как получить учетные данные admin в шаблоне менеджера ресурсов?

> [!IMPORTANT]
> Учетная запись пользователя admin предназначена для того, чтобы один пользователь был готов получить доступ к реестру, главным образом для целей тестирования. Не рекомендуется совместное использование учетных данных администратора несколькими пользователями. Для пользователей и субъектов-служб в сценариях автоматического входа рекомендуется использовать отдельные удостоверения. Смотрите [обзор аутентификации](container-registry-authentication.md).

Прежде чем получить учетные данные от аммиака, убедитесь, что пользователь админ реестра включен.

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

### <a name="delete-of-replication-fails-with-forbidden-status-although-the-replication-gets-deleted-using-the-azure-cli-or-azure-powershell"></a>Удаление репликации не удается с запретным статусом, хотя репликация удаляется с помощью Azure CLI или Azure PowerShell

Ошибка проявляется, когда пользователь имеет разрешения в реестре, но не имеет разрешений на уровне Reader в подписке. Чтобы решить эту проблему, назначайте пользователям разрешения Reader по подписке:


```azurecli  
az role assignment create --role "Reader" --assignee user@contoso.com --scope /subscriptions/<subscription_id> 
```

### <a name="firewall-rules-are-updated-successfully-but-they-do-not-take-effect"></a>Правила брандмауэра успешно обновляются, но они не всплываются

Распространение изменений правил брандмауэра занимает некоторое время. После изменения настроек брандмауэра, пожалуйста, подождите несколько минут, прежде чем проверить это изменение.


## <a name="registry-operations"></a>Операции с реестром

- [Как получить доступ к реестру докеров HTTP API V2?](#how-do-i-access-docker-registry-http-api-v2)
- [Как удалить все манифесты, на которые не ссылается ни один тег в репозитории?](#how-do-i-delete-all-manifests-that-are-not-referenced-by-any-tag-in-a-repository)
- [Почему использование квот реестра не уменьшается после удаляния изображений?](#why-does-the-registry-quota-usage-not-reduce-after-deleting-images)
- [Как проверить изменения квот на хранение?](#how-do-i-validate-storage-quota-changes)
- [Как проверить подлинность с помощью реестра при запуске CLI в контейнере?](#how-do-i-authenticate-with-my-registry-when-running-the-cli-in-a-container)
- [Как включить TLS 1.2?](#how-to-enable-tls-12)
- [Поддерживает ли реестр контейнеров Azure Доверие контента?](#does-azure-container-registry-support-content-trust)
- [Как предоставить доступ к изображениям без разрешения на управление ресурсом реестра?](#how-do-i-grant-access-to-pull-or-push-images-without-permission-to-manage-the-registry-resource)
- [Как включить автоматический карантин изображения для реестра?](#how-do-i-enable-automatic-image-quarantine-for-a-registry)
- [Как включить анонимный доступ к вытягиванию?](#how-do-i-enable-anonymous-pull-access)

### <a name="how-do-i-access-docker-registry-http-api-v2"></a>Как получить доступ к реестру докеров HTTP API V2?

ACR поддерживает Docker Registry HTTP API V2. AA можно получить по `https://<your registry login server>/v2/`адресу . Пример: `https://mycontainerregistry.azurecr.io/v2/`

### <a name="how-do-i-delete-all-manifests-that-are-not-referenced-by-any-tag-in-a-repository"></a>Как удалить все манифесты, на которые не ссылается ни один тег в репозитории?

Если вы находитесь на Баш:

```azurecli
az acr repository show-manifests -n myRegistry --repository myRepository --query "[?tags[0]==null].digest" -o tsv  | xargs -I% az acr repository delete -n myRegistry -t myRepository@%
```

Для Powershell:

```azurecli
az acr repository show-manifests -n myRegistry --repository myRepository --query "[?tags[0]==null].digest" -o tsv | %{ az acr repository delete -n myRegistry -t myRepository@$_ }
```

Примечание: Вы `-y` можете добавить в команду удаления, чтобы пропустить подтверждение.

Для получения дополнительной информации смотрите [удалить изображения контейнеров в реестре контейнеров Azure.](container-registry-delete.md)

### <a name="why-does-the-registry-quota-usage-not-reduce-after-deleting-images"></a>Почему использование квот реестра не уменьшается после удаляния изображений?

Эта ситуация может произойти, если основные слои по-прежнему ссылаются на другие изображения контейнера. Если вы удалите изображение без ссылок, использование реестра обновляется в течение нескольких минут.

### <a name="how-do-i-validate-storage-quota-changes"></a>Как проверить изменения квот на хранение?

Создайте изображение с 1 ГБ слоя, используя следующий файл докера. Это гарантирует, что изображение имеет слой, который не разделяется любым другим изображением в реестре.

```dockerfile
FROM alpine
RUN dd if=/dev/urandom of=1GB.bin  bs=32M  count=32
RUN ls -lh 1GB.bin
```

Создайте и нажмите изображение в свой реестр с помощью докера CLI.

```bash
docker build -t myregistry.azurecr.io/1gb:latest .
docker push myregistry.azurecr.io/1gb:latest
```

Вы должны быть в состоянии видеть, что использование хранилища увеличилось на портале Azure, или вы можете задать запрос использования с помощью CLI.

```azurecli
az acr show-usage -n myregistry
```

Удалите изображение с помощью Azure CLI или портала и проверьте обновленное использование в течение нескольких минут.

```azurecli
az acr repository delete -n myregistry --image 1gb
```

### <a name="how-do-i-authenticate-with-my-registry-when-running-the-cli-in-a-container"></a>Как проверить подлинность с помощью реестра при запуске CLI в контейнере?

Необходимо запустить контейнер Azure CLI, устанавливая розетку Docker:

```bash
docker run -it -v /var/run/docker.sock:/var/run/docker.sock azuresdk/azure-cli-python:dev
```

В контейнере `docker`установите:

```bash
apk --update add docker
```

Затем проверить подлинность в вашем реестре:

```azurecli
az acr login -n MyRegistry
```

### <a name="how-to-enable-tls-12"></a>Как включить TLS 1.2?

Включить TLS 1.2 с помощью любого недавнего клиента докера (версия 18.03.0 и выше). 

> [!IMPORTANT]
> С 13 января 2020 года Реестр контейнеров Azure будет требовать использовать протокол TLS версии 1.2 для всех безопасных подключений серверов и приложений. Поддержка протоколов TLS версии 1.0 и 1.1 будет прекращена.

### <a name="does-azure-container-registry-support-content-trust"></a>Поддерживает ли реестр контейнеров Azure Доверие контента?

Да, вы можете использовать доверенные изображения в реестре контейнеров Azure, так как [нотариус Docker](https://docs.docker.com/notary/getting_started/) был интегрирован и может быть включен. Для получения подробной информации смотрите [содержимое Доверия в реестре контейнеров Azure](container-registry-content-trust.md).


####  <a name="where-is-the-file-for-the-thumbprint-located"></a>Где находится файл отпечатков пальцев?

В `~/.docker/trust/tuf/myregistry.azurecr.io/myrepository/metadata`соответствии с :

* В `root.json`.
* Открытые ключи и сертификаты роли делегации хранятся в файле `targets.json` JSON `targets/releases` его родительской роли (например, для роли).

Предлагается проверить эти открытые ключи и сертификаты после общей проверки TUF, проведенной клиентом Докера и Нотариуса.

### <a name="how-do-i-grant-access-to-pull-or-push-images-without-permission-to-manage-the-registry-resource"></a>Как предоставить доступ к изображениям без разрешения на управление ресурсом реестра?

ACR поддерживает [пользовательские роли,](container-registry-roles.md) которые обеспечивают различные уровни разрешений. В `AcrPull` частности, роли `AcrPush` позволяют пользователям вытягивать и/или толкать изображения без разрешения на управление ресурсом реестра в Azure.

* Портал Azure: Ваш реестр -> контроль доступа (IAM) -> Добавить (Выберите `AcrPull` или `AcrPush` для роли).
* Azure CLI: Найдите идентификатор ресурсов реестра, запустив следующую команду:

  ```azurecli
  az acr show -n myRegistry
  ```
  
  Затем вы можете `AcrPull` назначить или `AcrPush` роль для пользователя `AcrPull`(следующий пример использует):

  ```azurecli
  az role assignment create --scope resource_id --role AcrPull --assignee user@example.com
  ```

  Или присвоить роль принципу службы, указанному в идентификаторе приложения:

  ```azurecli
  az role assignment create --scope resource_id --role AcrPull --assignee 00000000-0000-0000-0000-000000000000
  ```

Затем назначено получить аутентификации и доступ к изображениям в реестре.

* Для проверки подлинности в реестре:
    
  ```azurecli
  az acr login -n myRegistry 
  ```

* Для списка репозиториев:

  ```azurecli
  az acr repository list -n myRegistry
  ```

* Чтобы вытащить изображение:

  ```bash
  docker pull myregistry.azurecr.io/hello-world
  ```

При использовании только `AcrPull` `AcrPush` роли или роли у назначенца нет разрешения на управление ресурсом реестра в Azure. Например, `az acr list` `az acr show -n myRegistry` или не будет отображаться реестр.

### <a name="how-do-i-enable-automatic-image-quarantine-for-a-registry"></a>Как включить автоматический карантин изображения для реестра?

Карантин изображения в настоящее время является функцией предварительного просмотра ACR. Можно включить карантинный режим реестра, чтобы обычные пользователи были видны только тем изображениям, которые успешно прошли сканирование безопасности. Для получения [подробной](https://github.com/Azure/acr/tree/master/docs/preview/quarantine)информации см.

### <a name="how-do-i-enable-anonymous-pull-access"></a>Как включить анонимный доступ к вытягиванию?

Настройка реестра контейнеров Azure для анонимного (публичного) доступа к вытягиванию в настоящее время является функцией предварительного просмотра. Для обеспечения публичного доступа, https://aka.ms/acr/support/create-ticketпожалуйста, откройте билет поддержки по адресу . Для получения [подробной](https://feedback.azure.com/forums/903958-azure-container-registry/suggestions/32517127-enable-anonymous-access-to-registries)информации см.


## <a name="diagnostics-and-health-checks"></a>Диагностика и проверка здоровья

- [Проверьте здоровье с`az acr check-health`](#check-health-with-az-acr-check-health)
- [докер тянуть не удается с ошибкой: нетто / http: запрос отменен во время ожидания соединения (Client.Timeout превысил в ожидании заголовки)](#docker-pull-fails-with-error-nethttp-request-canceled-while-waiting-for-connection-clienttimeout-exceeded-while-awaiting-headers)
- [докер нажмите успешно, но докер тянуть не удается с ошибкой: несанкционированный: аутентификация требуется](#docker-push-succeeds-but-docker-pull-fails-with-error-unauthorized-authentication-required)
- [`az acr login`преуспевает, но команды докера не удается с ошибкой: несанкционированно: требуется аутентификация](#az-acr-login-succeeds-but-docker-fails-with-error-unauthorized-authentication-required)
- [Включить и получить отладка журналы докер daemon](#enable-and-get-the-debug-logs-of-the-docker-daemon)    
- [Новые пользовательские разрешения могут не всменяться сразу после обновления](#new-user-permissions-may-not-be-effective-immediately-after-updating)
- [Информация о подлинности не приводится в правильном формате при прямых вызовах REST API](#authentication-information-is-not-given-in-the-correct-format-on-direct-rest-api-calls)
- [Почему портал Azure не перечисляет все мои репозитории или теги?](#why-does-the-azure-portal-not-list-all-my-repositories-or-tags)
- [Почему портал Azure не может получить репозитории или теги?](#why-does-the-azure-portal-fail-to-fetch-repositories-or-tags)
- [Почему мой запрос на вытягивание или нажатие не выполняется при запрещенной операции?](#why-does-my-pull-or-push-request-fail-with-disallowed-operation)
- [Как собрать следы http на Windows?](#how-do-i-collect-http-traces-on-windows)

### <a name="check-health-with-az-acr-check-health"></a>Проверьте здоровье с`az acr check-health`

Для устранения неполадок в общей среде и проблем с реестром регистра [см.](container-registry-check-health.md)

### <a name="docker-pull-fails-with-error-nethttp-request-canceled-while-waiting-for-connection-clienttimeout-exceeded-while-awaiting-headers"></a>докер тянуть не удается с ошибкой: нетто / http: запрос отменен во время ожидания соединения (Client.Timeout превысил в ожидании заголовки)

 - Если эта ошибка является переходной проблемой, то повторная попытка будет успешной.
 - Если `docker pull` сбой непрерывно, то может быть проблема с Docker daemon. Как правило, проблему можно смягчить путем перезапуска Docker daemon. 
 - Если вы продолжаете видеть эту проблему после перезагрузки Docker daemon, то проблема может быть некоторые проблемы с подключением к сети с машиной. Чтобы проверить, является ли общая сеть на машине работоспособной, запустите следующую команду для проверки подключения конечных точек. Минимальная `az acr` версия, содержащая эту команду проверки подключения, составляет 2.2.9. Обновите свой Azure CLI, если вы используете старую версию.
 
  ```azurecli
  az acr check-health -n myRegistry
  ```

 - Вы всегда должны иметь механизм повтора на всех операциях клиента Docker.

### <a name="docker-pull-is-slow"></a>Докер тянуть медленно
Используйте [этот](http://www.azurespeed.com/Azure/Download) инструмент для проверки скорости загрузки сети машин. Если сеть машин работает медленно, рассмотрите возможность использования Azure VM в том же регионе, что и реестр. Это обычно дает вам более быструю скорость сети.

### <a name="docker-push-is-slow"></a>Докер толчок медленный
Используйте [этот](http://www.azurespeed.com/Azure/Upload) инструмент для проверки скорости загрузки сети машин. Если сеть машин работает медленно, рассмотрите возможность использования Azure VM в том же регионе, что и реестр. Это обычно дает вам более быструю скорость сети.

### <a name="docker-push-succeeds-but-docker-pull-fails-with-error-unauthorized-authentication-required"></a>Докер нажмите удается, но докер тянуть не удается с ошибкой: несанкционированный: аутентификация требуется

Эта ошибка может произойти с версией Red Hat daemon Docker, где `--signature-verification` включена по умолчанию. Вы можете проверить варианты Docker daemon для Red Hat Enterprise Linux (RHEL) или Fedora, запустив следующую команду:

```bash
grep OPTIONS /etc/sysconfig/docker
```

Например, Fedora 28 Server имеет следующие варианты докера:

`OPTIONS='--selinux-enabled --log-driver=journald --live-restore'`

При `--signature-verification=false` пропаже не `docker pull` удается с ошибкой, похожей на:

```output
Trying to pull repository myregistry.azurecr.io/myimage ...
unauthorized: authentication required
```

Чтобы устранить эту ошибку, сделайте следующее:
1. Добавьте `--signature-verification=false` опцию в файл `/etc/sysconfig/docker`конфигурации Docker daemon. Пример:
   
   `OPTIONS='--selinux-enabled --log-driver=journald --live-restore --signature-verification=false'`
   
2. Перезапустите службу Docker daemon, запустив следующую команду:
   
   ```bash
   sudo systemctl restart docker.service
   ```

Подробную `--signature-verification` информацию можно `man dockerd`найти, запустив .

### <a name="az-acr-login-succeeds-but-docker-fails-with-error-unauthorized-authentication-required"></a>az acr логин удается, но докер не справляется с ошибкой: несанкционированный: аутентификация требуется

Например, `docker push myregistry.azurecr.io/myimage:latest`убедитесь, что вы используете URL-адрес сервера с нижним регистром, даже если название ресурса реестра является верхним или смешанным `myRegistry`случаем.

### <a name="enable-and-get-the-debug-logs-of-the-docker-daemon"></a>Включить и получить отладки журналы Докер daemon    

Начните `dockerd` `debug` с опции. Во-первых, создайте файл конфигурации`/etc/docker/daemon.json`Docker daemon (), `debug` если он не существует, и добавьте опцию:

```json
{    
    "debug": true    
}
```

Затем перезапустите демон. Например, с Ubuntu 14.04:

```bash
sudo service docker restart
```

Подробности можно найти в [документации Docker](https://docs.docker.com/engine/admin/#enable-debugging).    

 * В зависимости от системы журналы могут создаваться в разных местах. Например, для Ubuntu 14.04, `/var/log/upstart/docker.log`это .    
Подробную информацию можно узнать [из документации Docker.](https://docs.docker.com/engine/admin/#read-the-logs)    

 * Для Docker для Windows журналы генерируются под %LOCALAPPDATA%/docker/. Однако он не может содержать всю информацию об отладке еще.    

   Для того, чтобы получить доступ к полному журналу daemon, вам может понадобиться несколько дополнительных шагов:

    ```console
    docker run --privileged -it --rm -v /var/run/docker.sock:/var/run/docker.sock -v /usr/local/bin/docker:/usr/local/bin/docker alpine sh

    docker run --net=host --ipc=host --uts=host --pid=host -it --security-opt=seccomp=unconfined --privileged --rm -v /:/host alpine /bin/sh
    chroot /host
    ```
    Теперь у вас есть доступ ко `dockerd`всем файлам запуска VM. Бревно `/var/log/docker.log`находится на .

### <a name="new-user-permissions-may-not-be-effective-immediately-after-updating"></a>Новые пользовательские разрешения могут не всменяться сразу после обновления

При предоставлении новых разрешений (новых ролей) директору службы изменение может ввести в действие не сразу. Существуют две возможные причины этой ошибки.

* Задержка выполнения заданий функционального каталога Azure Active. Обычно это быстро, но это может занять несколько минут из-за задержки распространения.
* Задержка разрешения на сервере токенов ACR. Этот процесс может занять больше 10 минут. Чтобы смягчить, `docker logout` вы можете, а затем снова проверить подлинность с тем же пользователем через 1 минуту:

  ```bash
  docker logout myregistry.azurecr.io
  docker login myregistry.azurecr.io
  ```

В настоящее время ACR не поддерживает удаление репликации дома пользователями. Обходной путь заключается в том, чтобы включить домашнее `"condition": false` воспроизведение создать в шаблоне, но пропустить его создание, добавив, как показано ниже:

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

### <a name="authentication-information-is-not-given-in-the-correct-format-on-direct-rest-api-calls"></a>Информация о подлинности не приводится в правильном формате при прямых вызовах REST API

Вы можете `InvalidAuthenticationInfo` столкнуться с `curl` ошибкой, `-L`особенно `--location` с помощью инструмента с опцией , (следовать перенаправления).
Например, получение капли с `curl` `-L` помощью опции и базовой аутентификации:

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

Основной причиной `curl` является то, что некоторые реализации следуют перенаправлениям заголовков из исходного запроса.

Чтобы решить проблему, необходимо следовать перенаправления вручную без заголовков. Печать заголовки ответов `-D -` с `curl` опцией, `Location` а затем извлечь: заголовок:

```bash
redirect_url=$(curl -s -D - -H "Authorization: basic $credential" https://$registry.azurecr.io/v2/$repository/blobs/$digest | grep "^Location: " | cut -d " " -f2 | tr -d '\r')
curl $redirect_url
```

### <a name="why-does-the-azure-portal-not-list-all-my-repositories-or-tags"></a>Почему портал Azure не перечисляет все мои репозитории или теги? 

Если вы используете браузер Microsoft Edge/IE, вы можете увидеть не более 100 репозиторий или тегов. Если в вашем реестре более 100 репозиторий или тегов, мы рекомендуем использовать браузер Firefox или Chrome для их всех.

### <a name="why-does-the-azure-portal-fail-to-fetch-repositories-or-tags"></a>Почему портал Azure не может получить репозитории или теги?

Браузер может быть не в состоянии отправить запрос на получение репозиторий или тегов на сервер. Там могут быть различные причины, такие как:

* Отсутствие подключения к сети
* Брандмауэр
* Блокировщики рекламы
* Ошибки DNS

Пожалуйста, свяжитесь с вашим сетевым администратором или проверьте конфигурацию и подключение сети. Попробуйте `az acr check-health -n yourRegistry` запустить с помощью Azure CLI, чтобы проверить, способна ли среда подключиться к реестру контейнеров. Кроме того, вы также можете попробовать инкогнито или приватную сессию в браузере, чтобы избежать каких-либо устаревших кэш браузера или файлов cookie.

### <a name="why-does-my-pull-or-push-request-fail-with-disallowed-operation"></a>Почему мой запрос на вытягивание или нажатие не выполняется при запрещенной операции?

Вот несколько сценариев, где операции, возможно, запрещены:
* Классические реестры больше не поддерживаются. Пожалуйста, пересмотрите на поддерживаемые [SKUs](https://aka.ms/acr/skus) с помощью [обновления az acr](https://docs.microsoft.com/cli/azure/acr?view=azure-cli-latest#az-acr-update) или портала Azure.
* Изображение или репозиторий, возможно, заблокированы так, что он не может быть удален или обновлен. Для просмотра текущих атрибутов можно использовать команду [репозитория az acr.](https://docs.microsoft.com/azure/container-registry/container-registry-image-lock)
* Некоторые операции запрещены, если изображение находится в карантине. Подробнее о [карантине](https://github.com/Azure/acr/tree/master/docs/preview/quarantine).

### <a name="how-do-i-collect-http-traces-on-windows"></a>Как собрать следы http на Windows?

#### <a name="prerequisites"></a>Предварительные требования

- Включить расшифровку https в скрипач:<https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS>
- Позволяет Docker использовать прокси через докер ui:<https://docs.docker.com/docker-for-windows/#proxies>
- Не забудьте вернуться, когда полный.  Докер не будет работать с этим включенным и скрипач не работает.

#### <a name="windows-containers"></a>Контейнеры Windows

Настройка Docker прокси до 127.0.0.1:8888

#### <a name="linux-containers"></a>Контейнеры Linux

Найти IP Докер VM виртуальный переключатель:

```powershell
(Get-NetIPAddress -InterfaceAlias "*Docker*" -AddressFamily IPv4).IPAddress
```

Налажить прокси Docker на вывод предыдущей команды и порт 8888 (например, 10.0.75.1:8888)

## <a name="tasks"></a>Задачи

- [Как отменить пакетные забеги?](#how-do-i-batch-cancel-runs)
- [Как включить папку .git в команду сборки az acr?](#how-do-i-include-the-git-folder-in-az-acr-build-command)
- [Поддерживает ли задачи GitLab для триггеров Source?](#does-tasks-support-gitlab-for-source-triggers)
- [Какую службу управления репозиторием git поддерживает задачи?](#what-git-repository-management-service-does-tasks-support)

### <a name="how-do-i-batch-cancel-runs"></a>Как отменить пакетные забеги?

Следующие команды отменяют все выполняемые задачи в указанном реестре.

```azurecli
az acr task list-runs -r $myregistry --run-status Running --query '[].runId' -o tsv \
| xargs -I% az acr task cancel-run -r $myregistry --run-id %
```

### <a name="how-do-i-include-the-git-folder-in-az-acr-build-command"></a>Как включить папку .git в команду сборки az acr?

Если вы передаете `az acr build` команду локальную папку исходного кода, `.git` папка исключается из загруженного пакета по умолчанию. Можно создать `.dockerignore` файл со следующим параметром. Он говорит команде восстановить все `.git` файлы в загруженном пакете. 

`!.git/**`

Эта настройка также `az acr run` относится к команде.

### <a name="does-tasks-support-gitlab-for-source-triggers"></a>Поддерживает ли задачи GitLab для триггеров Source?

В настоящее время мы не поддерживаем GitLab для триггеров Исходного кода.

### <a name="what-git-repository-management-service-does-tasks-support"></a>Какую службу управления репозиторием git поддерживает задачи?

| Сервис Git | Контекст исходного кода | Ручная сборка | Автосборка через триггер коммитов |
|---|---|---|---|
| GitHub | https://github.com/user/myapp-repo.git#mybranch:myfolder | Да | Да |
| Azure Repos | https://dev.azure.com/user/myproject/_git/myapp-repo#mybranch:myfolder | Да | Да |
| GitLab | https://gitlab.com/user/myapp-repo.git#mybranch:myfolder | Да | нет |
| Bitbucket; | https://user@bitbucket.org/user/mayapp-repo.git#mybranch:myfolder | Да | нет |

## <a name="run-error-message-troubleshooting"></a>Выполнить устранение сообщения об ошибке

| Сообщение об ошибке | Руководство по устранению неполадок |
|---|---|
|Доступ для VM не был настроен, следовательно, подписки не были найдены|Это может произойти, `az login --identity` если вы используете в aCR Task. Это переходная ошибка, возникает в случае нераспространения назначения ролей вашей Управляемой идентификации. Ожидание несколько секунд до повторного проработки.|

## <a name="cicd-integration"></a>Интеграция CI/CD

- [CircleCI](https://github.com/Azure/acr/blob/master/docs/integration/CircleCI.md)
- [Действия GitHub](https://github.com/Azure/acr/blob/master/docs/integration/github-actions/github-actions.md)

## <a name="next-steps"></a>Следующие шаги

* [Подробнее](container-registry-intro.md) о реестре контейнеров Azure.
