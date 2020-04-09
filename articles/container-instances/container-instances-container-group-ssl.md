---
title: Включить TLS с контейнером sidecar
description: Создайте конечную точку SSL или TLS для контейнерной группы, работая в контейнерных инстанциях Azure, запустив Nginx в контейнере для sidecar
ms.topic: article
ms.date: 02/14/2020
ms.openlocfilehash: b9ea9367219db694b89d6bf4a1e52efb373c71c4
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/09/2020
ms.locfileid: "80984612"
---
# <a name="enable-a-tls-endpoint-in-a-sidecar-container"></a>Включить конечную точку TLS в контейнере для sidecar

В этой статье показано, как создать [группу контейнеров](container-instances-container-groups.md) с контейнером приложений и баклажан под управлением поставщика TLS/SSL. Настраивая группу контейнеров с отдельной конечней точкой TLS, вы включите соединения TLS для приложения без изменения кода приложения.

Вы настраиваете примерную группу контейнеров, состоящую из двух контейнеров:
* Контейнер приложений, который запускает простое веб-приложение с использованием общедоступного изображения [Microsoft aci-helloworld.](https://hub.docker.com/_/microsoft-azuredocs-aci-helloworld) 
* Контейнер для sidecar, работающий с общедоступным изображением [Nginx,](https://hub.docker.com/_/nginx) настроенный на использование TLS. 

В этом примере группа контейнеров предоставляет порт 443 только для Nginx со своим общедоступным IP-адресом. Nginx маршруты HTTPS запросы на компаньон веб-приложение, которое слушает внутри порта 80. Можно адаптировать пример для контейнерных приложений, которые слушают в других портах. 

Ознакомьтесь с [другими шагами](#next-steps) по включению TLS в группу контейнеров.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Для выполнения задач этой статьи можно использовать Azure Cloud Shell или локальный экземпляр Azure CLI. Если вы хотите использовать его локально, рекомендуется использовать версию 2.0.55 или более позднюю. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0](/cli/azure/install-azure-cli).

## <a name="create-a-self-signed-certificate"></a>Создание самозаверяющего сертификата

Чтобы настроить Nginx в качестве поставщика TLS, вам нужен сертификат TLS/SSL. В этой статье показано, как создать и настроить самоподписанный сертификат TLS/SSL. Для производственных сценариев необходимо получить сертификат от сертификата.

Для создания самостоятельно подписанного сертификата TLS/SSL воспользуйтесь инструментом [OpenSSL,](https://www.openssl.org/) доступным в Azure Cloud Shell и многими дистрибутивами Linux, или используйте сопоставимый клиентский инструмент в операционной системе.

Сначала создайте запрос сертификата (.csr файл) в локальном рабочем каталоге:

```console
openssl req -new -newkey rsa:2048 -nodes -keyout ssl.key -out ssl.csr
```

Следуйте запросам, чтобы добавить идентификационную информацию. Для общего имени введите имя хоста, связанное с сертификатом. При попрошйе пароля нажмите Enter без ввода, чтобы не добавлять пароль.

Запустите следующую команду для создания самоподписного сертификата (файл.crt) из запроса сертификата. Пример:

```console
openssl x509 -req -days 365 -in ssl.csr -signkey ssl.key -out ssl.crt
```

Теперь вы должны увидеть три файла в`ssl.csr`каталоге:`ssl.key`запрос на сертификат (),`ssl.crt`частный ключ ( ), и самоподписанный сертификат (). Вы `ssl.key` используете и `ssl.crt` в более поздних шагах.

## <a name="configure-nginx-to-use-tls"></a>Нанастройка Nginx для использования TLS

### <a name="create-nginx-configuration-file"></a>Создание файла конфигурации Nginx

В этом разделе создается файл конфигурации для использования Nginx TLS. Начните с копирования следующего текста `nginx.conf`в новый файл под названием. В Azure Cloud Shell вы можете использовать Visual Studio Code для создания файла в рабочем каталоге:

```console
code nginx.conf
```

В, `location`не забудьте установить `proxy_pass` с правильным портом для вашего приложения. В этом примере мы устанавливаем `aci-helloworld` порт 80 для контейнера.

```console
# nginx Configuration File
# https://wiki.nginx.org/Configuration

# Run as a less privileged user for security reasons.
user nginx;

worker_processes auto;

events {
  worker_connections 1024;
}

pid        /var/run/nginx.pid;

http {

    #Redirect to https, using 307 instead of 301 to preserve post data

    server {
        listen [::]:443 ssl;
        listen 443 ssl;

        server_name localhost;

        # Protect against the BEAST attack by not using SSLv3 at all. If you need to support older browsers (IE6) you may need to add
        # SSLv3 to the list of protocols below.
        ssl_protocols              TLSv1.2;

        # Ciphers set to best allow protection from Beast, while providing forwarding secrecy, as defined by Mozilla - https://wiki.mozilla.org/Security/Server_Side_TLS#Nginx
        ssl_ciphers                ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES256-GCM-SHA384:ECDHE-ECDSA-AES256-GCM-SHA384:DHE-RSA-AES128-GCM-SHA256:DHE-DSS-AES128-GCM-SHA256:kEDH+AESGCM:ECDHE-RSA-AES128-SHA256:ECDHE-ECDSA-AES128-SHA256:ECDHE-RSA-AES128-SHA:ECDHE-ECDSA-AES128-SHA:ECDHE-RSA-AES256-SHA384:ECDHE-ECDSA-AES256-SHA384:ECDHE-RSA-AES256-SHA:ECDHE-ECDSA-AES256-SHA:DHE-RSA-AES128-SHA256:DHE-RSA-AES128-SHA:DHE-DSS-AES128-SHA256:DHE-RSA-AES256-SHA256:DHE-DSS-AES256-SHA:DHE-RSA-AES256-SHA:AES128-GCM-SHA256:AES256-GCM-SHA384:ECDHE-RSA-RC4-SHA:ECDHE-ECDSA-RC4-SHA:AES128:AES256:RC4-SHA:HIGH:!aNULL:!eNULL:!EXPORT:!DES:!3DES:!MD5:!PSK;
        ssl_prefer_server_ciphers  on;

        # Optimize TLS/SSL by caching session parameters for 10 minutes. This cuts down on the number of expensive TLS/SSL handshakes.
        # The handshake is the most CPU-intensive operation, and by default it is re-negotiated on every new/parallel connection.
        # By enabling a cache (of type "shared between all Nginx workers"), we tell the client to re-use the already negotiated state.
        # Further optimization can be achieved by raising keepalive_timeout, but that shouldn't be done unless you serve primarily HTTPS.
        ssl_session_cache    shared:SSL:10m; # a 1mb cache can hold about 4000 sessions, so we can hold 40000 sessions
        ssl_session_timeout  24h;


        # Use a higher keepalive timeout to reduce the need for repeated handshakes
        keepalive_timeout 300; # up from 75 secs default

        # remember the certificate for a year and automatically connect to HTTPS
        add_header Strict-Transport-Security 'max-age=31536000; includeSubDomains';

        ssl_certificate      /etc/nginx/ssl.crt;
        ssl_certificate_key  /etc/nginx/ssl.key;

        location / {
            proxy_pass http://localhost:80; # TODO: replace port if app listens on port other than 80
            
            proxy_set_header Connection "";
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $remote_addr;
        }
    }
}
```

### <a name="base64-encode-secrets-and-configuration-file"></a>Base64-кодирует секреты и файл конфигурации

Base64 кодирует файл конфигурации Nginx, сертификат TLS/SSL и ключ TLS. В следующем разделе вы вводите закодированное содержимое в файл YAML, используемый для развертывания группы контейнеров.

```console
cat nginx.conf | base64 > base64-nginx.conf
cat ssl.crt | base64 > base64-ssl.crt
cat ssl.key | base64 > base64-ssl.key
```

## <a name="deploy-container-group"></a>Развертывание контейнерной группы

Теперь разместите группу контейнеров, указав конфигурации контейнеров в [файле YAML.](container-instances-multi-container-yaml.md)

### <a name="create-yaml-file"></a>Создание файла YAML

Скопируйте следующий YAML `deploy-aci.yaml`в новый файл под названием . В Azure Cloud Shell вы можете использовать Visual Studio Code для создания файла в рабочем каталоге:

```console
code deploy-aci.yaml
```

Введите содержимое базовых64-кодированных `secret`файлов, где указано под . Например, `cat` каждый из базовых 64-кодированных файлов, чтобы увидеть его содержимое. Во время развертывания эти файлы добавляются в [секретный том](container-instances-volume-secret.md) в группе контейнеров. В этом примере секретный том устанавливается в контейнер Nginx.

```YAML
api-version: 2018-10-01
location: westus
name: app-with-ssl
properties:
  containers:
  - name: nginx-with-ssl
    properties:
      image: nginx
      ports:
      - port: 443
        protocol: TCP
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
      volumeMounts:
      - name: nginx-config
        mountPath: /etc/nginx
  - name: my-app
    properties:
      image: mcr.microsoft.com/azuredocs/aci-helloworld
      ports:
      - port: 80
        protocol: TCP
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
  volumes:
  - secret:
      ssl.crt: <Enter contents of base64-ssl.crt here>
      ssl.key: <Enter contents of base64-ssl.key here>
      nginx.conf: <Enter contents of base64-nginx.conf here>
    name: nginx-config
  ipAddress:
    ports:
    - port: 443
      protocol: TCP
    type: Public
  osType: Linux
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

### <a name="deploy-the-container-group"></a>Развертывание группы контейнеров

Создайте группу ресурсов с [группой az, создающей](/cli/azure/group#az-group-create) команду:

```azurecli-interactive
az group create --name myResourceGroup --location westus
```

Развертывание группы контейнеров с [контейнером az создать](/cli/azure/container#az-container-create) команду, передавая файл YAML в качестве аргумента.

```azurecli
az container create --resource-group <myResourceGroup> --file deploy-aci.yaml
```

### <a name="view-deployment-state"></a>Просмотр состояния развертывания

Чтобы просмотреть состояние развертывания, используйте команду [az container show](/cli/azure/container#az-container-show) ниже.

```azurecli
az container show --resource-group <myResourceGroup> --name app-with-ssl --output table
```

Для успешного развертывания выход аналогичен следующему:

```console
Name          ResourceGroup    Status    Image                                                    IP:ports             Network    CPU/Memory       OsType    Location
------------  ---------------  --------  -------------------------------------------------------  -------------------  ---------  ---------------  --------  ----------
app-with-ssl  myresourcegroup  Running   nginx, mcr.microsoft.com/azuredocs/aci-helloworld        52.157.22.76:443     Public     1.0 core/1.5 gb  Linux     westus
```

## <a name="verify-tls-connection"></a>Проверить подключение TLS

Используйте браузер для навигации по общедоступному IP-адресу контейнерной группы. IP-адрес, показанный `52.157.22.76`в этом примере, так что URL **https://52.157.22.76**. Вы должны использовать HTTPS, чтобы увидеть запущенное приложение, из-за конфигурации сервера Nginx. Попытки подключения к HTTP завершаются неудачей.

![Снимок экрана браузера: приложение, выполняющееся в экземпляре контейнера Azure](./media/container-instances-container-group-ssl/aci-app-ssl-browser.png)

> [!NOTE]
> Поскольку в этом примере используется сертификат, подписанный самостоятельно, а не сертификат, браузер отображает предупреждение о безопасности при подключении к сайту через HTTPS. Возможно, вам придется принять предупреждение или настроить настройки браузера или сертификата, чтобы перейти к странице. Это ожидаемое поведение.

>

## <a name="next-steps"></a>Дальнейшие действия

В этой статье показано, как настроить контейнер Nginx для подключения TLS к веб-приложению, работая в группе контейнеров. Вы можете адаптировать этот пример для приложений, которые слушают в портах, отличных от порта 80. Вы также можете обновить файл конфигурации Nginx, чтобы автоматически перенаправить серверные соединения на порт 80 (HTTP) для использования HTTPS.

Хотя эта статья использует Nginx в баке, вы можете использовать другой поставщик TLS, таких как [Caddy](https://caddyserver.com/).

При развертывании группы контейнеров в [виртуальной сети Azure](container-instances-vnet.md)можно рассмотреть другие варианты, позволяющие включить конечную точку TLS для экземпляра контейнеров бэкэнда, включая:

* [Прокси-функции Azure](../azure-functions/functions-proxies.md)
* [Управление API Azure](../api-management/api-management-key-concepts.md)
* [Шлюз приложения Azure](../application-gateway/overview.md) - см. [шаблон развертывания](https://github.com/Azure/azure-quickstart-templates/tree/master/201-aci-wordpress-vnet)образца .
