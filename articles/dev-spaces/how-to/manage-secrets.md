---
title: Управление секретами при работе со службой Azure Dev Space
services: azure-dev-spaces
ms.date: 12/03/2019
ms.topic: conceptual
description: Узнайте, как использовать секреты Kubernetes при запуске или построить время при разработке приложений с Azure Dev Spaces
keywords: Docker, Kubernetes, Azure, AKS, Azure Container Service, containers
ms.openlocfilehash: d9dd0de348612bbb3baf5fb351c1c9af1c228c1f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75438465"
---
# <a name="how-to-manage-secrets-when-working-with-an-azure-dev-space"></a>Управление секретами при работе со службой Azure Dev Space

Вашим службам могут потребоваться определенные пароли, строки подключения и другие секреты для баз данных или других защищенных служб Azure. Задавая значения этих секретов в файле конфигурации, их можно сделать доступными в коде в качестве переменных среды.  Эти файлы конфигурации должны обрабатываться с осторожностью, чтобы избежать ущерба для безопасности секретов.

## <a name="storing-and-using-runtime-secrets"></a>Хранение и использование секретов выполнения

Azure Dev Spaces предоставляет два рекомендуемых и упорядоченных варианта хранения секретов в диаграммах Helm, генерируемых клиентским инструментированием Azure Dev Spaces: в файле `values.dev.yaml` и встроенном непосредственно в `azds.yaml`режим. Не рекомендуется хранить секреты `values.yaml`в .

> [!NOTE]
> Следующие подходы показывают, как хранить и использовать секреты для диаграмм Helm, генерируемых инструментарием клиента. Если вы создаете свой собственный график Helm, вы можете использовать диаграмму Helm непосредственно для управления и хранения секретов.

### <a name="using-valuesdevyaml"></a>Использование значений.dev.yaml

В проекте, который вы уже подготовили с `values.dev.yaml` Azure Dev Spaces, создайте файл в той же папке, `azds.yaml` чтобы определить ваши секретные ключи и значения. Пример:

```yaml
secrets:
  redis:
    port: "6380"
    host: "contosodevredis.redis.cache.windows.net"
    key: "secretkeyhere"
```

Проверить `azds.yaml` ссылки `values.dev.yaml` файла как `?`необязательный с помощью . Пример:

```yaml
install:
  values:
  - values.dev.yaml?
  - secrets.dev.yaml?
```

Если у вас есть дополнительные секретные файлы, вы можете добавить их и здесь.

Обновление или проверка ссылок службы на ваши секреты в качестве переменных среды. Пример:

```javascript
var redisPort = process.env.REDIS_PORT
var host = process.env.REDIS_HOST
var theKey = process.env.REDIS_KEY
```
    
Запустите обновленные `azds up`сервисы с помощью .

```console
azds up
```
 
Используйте `kubectl` для проверки ваших секретов были созданы.

```console
kubectl get secret --namespace default -o yaml 
```

> [!IMPORTANT]
> Не рекомендуется хранить секреты в элементе управления исходным источником. При использовании Git `values.dev.yaml` `.gitignore` добавьте в файл, чтобы избежать совершения секретов в элементе управления исходным источником.

### <a name="using-azdsyaml"></a>Использование azds.yaml

В проекте, который вы уже подготовили с Azure Dev Spaces, добавьте секретные клавиши `azds.yaml`и ценность, используя *$PLACEHOLDER* синтаксис под *configurations.develop.install.set* в . Пример:

```yaml
configurations:
  develop:
    ...
    install:
      set:
        secrets:
          redis:
            port: "$REDIS_PORT"
            host: "$REDIS_HOST"
            key: "$REDIS_KEY"
```

> [!NOTE]
> Вы можете ввести секретные *$PLACEHOLDER* значения непосредственно `azds.yaml`без использования $PLACEHOLDER синтаксиса в . Однако этот подход не `azds.yaml` рекомендуется, так как хранится в исходном контроле.
     
Создайте `.env` файл в той `azds.yaml` же папке, чтобы определить *значения $PLACEHOLDER.* Пример:

```
REDIS_PORT=3333
REDIS_HOST=myredishost
REDIS_KEY=myrediskey
```

> [!IMPORTANT]
> Не рекомендуется хранить секреты в элементе управления исходным источником. При использовании Git `.env` `.gitignore` добавьте в файл, чтобы избежать совершения секретов в элементе управления исходным источником.

Обновление или проверка ссылок службы на ваши секреты в качестве переменных среды. Пример:

```javascript
var redisPort = process.env.REDIS_PORT
var host = process.env.REDIS_HOST
var theKey = process.env.REDIS_KEY
```
    
Запустите обновленные `azds up`сервисы с помощью .

```console
azds up
```
 
Используйте `kubectl` для проверки ваших секретов были созданы.

```console
kubectl get secret --namespace default -o yaml 
```

## <a name="using-secrets-as-build-arguments"></a>Использование секретов в качестве аргументов построения

Предыдущий раздел показал, как хранить и использовать секреты для использования во время выполнения контейнера. Вы также можете использовать любой секрет во время сборки контейнеров, например пароль для частного NuGet, используя. `azds.yaml`

В, `azds.yaml`установить время сборки секреты в *configurations.develop.build.args* с помощью синтаксиса. `<variable name>: ${secret.<secret name>.<secret key>}` Пример:

```yaml
configurations:
  develop:
    build:
      dockerfile: Dockerfile.develop
      useGitIgnore: true
      args:
        BUILD_CONFIGURATION: ${BUILD_CONFIGURATION:-Debug}
        MYTOKEN: ${secret.mynugetsecret.pattoken}
```

В приведенном выше примере, *mynugetsecret* является существующим секретом и *pattoken* является существующим ключом.

>[!NOTE]
> Секретные имена и `.` ключи могут содержать символ. Используйте `\` `.` для побега при передаче секретов в качестве аргументов сборки. Например, передать секрет под названием *foo.bar* с `MYTOKEN: ${secret.foo\.bar.token}`ключом *токена*: . Кроме того, секреты могут быть оценены с приставкой и текстом postfix. Например, `MYURL: eus-${secret.foo\.bar.token}-version1`. Кроме того, секреты, доступные в родительских и бабушки и дедушки пространства могут быть переданы в качестве аргументов сборки.

В dockerfile используйте директиву *ARG* для потребления секрета, а затем используйте ту же самую переменную позже в Dockerfile. Пример:

```dockerfile
...
ARG MYTOKEN
...
ARG NUGET_EXTERNAL_FEED_ENDPOINTS="{'endpointCredentials': [{'endpoint':'PRIVATE_NUGET_ENDPOINT', 'password':'${MYTOKEN}'}]}"
...
```

Обновите службы, выполняемые в кластере, чтобы активировать эти изменения. В командной строке запустите команду:

```
azds up
```

## <a name="next-steps"></a>Дальнейшие действия

С помощью этих методов можно безопасно подключиться к базе данных, кэшу Azure для Redis или получить защищенный доступ к службам Azure.
 
