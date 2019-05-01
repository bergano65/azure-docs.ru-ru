---
title: Включение SSL в экземпляры контейнеров Azure
description: Создайте конечную точку SSL или TLS для группы контейнеров в экземплярах контейнера Azure
services: container-instances
author: dlepow
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 04/03/2019
ms.author: danlep
ms.custom: ''
ms.openlocfilehash: 10c015a9aee4ed8be54805f7adaae5bb4b5c422f
ms.sourcegitcommit: e7d4881105ef17e6f10e8e11043a31262cfcf3b7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/29/2019
ms.locfileid: "64870392"
---
# <a name="enable-an-ssl-endpoint-in-a-container-group"></a>Включение конечной точки SSL в группе контейнеров

В этой статье показано, как создать [группы контейнеров](container-instances-container-groups.md) с контейнер приложения и сопроводительным контейнером под управлением поставщика SSL. Настроив группу контейнеров с отдельной конечной точки SSL, как разрешить SSL-подключения для вашего приложения без изменения кода приложения.

Настройка группы контейнеров, состоящее из двух контейнеров.
* Контейнер приложения, который запускает простое веб-приложение, с помощью общедоступного Microsoft [aci-helloworld](https://hub.docker.com/_/microsoft-azuredocs-aci-helloworld) изображения. 
* Под управлением открытый сопроводительным контейнером [Nginx](https://hub.docker.com/_/nginx) изображения, настроено использование протокола SSL. 

В этом примере группе контейнеров предоставляет только порт 443 для Nginx его общедоступный IP-адрес. Nginx направляет HTTPS-запросы дополнительное веб-приложение, который внутренне прослушивает порт 80. Вы можете адаптировать пример для приложения-контейнеры, которые прослушивание других портов.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Для выполнения задач этой статьи можно использовать Azure Cloud Shell или локальный экземпляр Azure CLI. Если вы хотите использовать его локально, рекомендуется использовать версию 2.0.55 или более позднюю. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0](/cli/azure/install-azure-cli).

## <a name="create-a-self-signed-certificate"></a>Создание самозаверяющего сертификата

Чтобы настроить Nginx как поставщик SSL, вам потребуется сертификат SSL. В этой статье показано, как создать и установить самозаверяющий SSL-сертификат. В рабочей среде следует получить сертификат от центра сертификации.

Чтобы создать самозаверяющий SSL-сертификат, используйте [OpenSSL](https://www.openssl.org/) средство доступно в Azure Cloud Shell и многих дистрибутивов Linux, или использовать инструмент сопоставимых клиента в операционной системе.

Создаете запрос сертификата (CSR-файл) в локальный рабочий каталог:

```console
openssl req -new -newkey rsa:2048 -nodes -keyout ssl.key -out ssl.csr
```

Следуйте инструкциям на экране, чтобы добавить идентификационные данные. Для общего имени введите имя узла, связанный с сертификатом. При появлении запроса ввести пароль, нажмите клавишу ВВОД без ввода, чтобы пропустить добавление пароля.

Выполните следующую команду для создания самозаверяющего сертификата (.crt файл) из запроса сертификата. Например: 

```console
openssl x509 -req -days 365 -in ssl.csr -signkey ssl.key -out ssl.crt
```

Теперь вы увидите три файла в каталоге: запрос на сертификат (`ssl.csr`), закрытый ключ (`ssl.key`) и самостоятельно подписанный сертификат (`ssl.crt`). Использовании `ssl.key` и `ssl.crt` в последующих шагах.

## <a name="configure-nginx-to-use-ssl"></a>Настройка Nginx, чтобы использовать SSL

### <a name="create-nginx-configuration-file"></a>Создайте файл конфигурации Nginx

В этом разделе создайте файл конфигурации Nginx, чтобы использовать SSL. Начала скопируйте следующий текст в новый файл с именем`nginx.conf`. В Azure Cloud Shell можно использовать Visual Studio Code для создания файла в рабочем каталоге:

```console
code nginx.conf
```

В `location`, не забудьте задать `proxy_pass` на правильный порт для приложения. В этом примере задается порт 80 для `aci-helloworld` контейнера.

```console
# nginx Configuration File
# http://wiki.nginx.org/Configuration

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
        ssl_protocols              TLSv1 TLSv1.1 TLSv1.2;

        # Ciphers set to best allow protection from Beast, while providing forwarding secrecy, as defined by Mozilla - https://wiki.mozilla.org/Security/Server_Side_TLS#Nginx
        ssl_ciphers                ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES256-GCM-SHA384:ECDHE-ECDSA-AES256-GCM-SHA384:DHE-RSA-AES128-GCM-SHA256:DHE-DSS-AES128-GCM-SHA256:kEDH+AESGCM:ECDHE-RSA-AES128-SHA256:ECDHE-ECDSA-AES128-SHA256:ECDHE-RSA-AES128-SHA:ECDHE-ECDSA-AES128-SHA:ECDHE-RSA-AES256-SHA384:ECDHE-ECDSA-AES256-SHA384:ECDHE-RSA-AES256-SHA:ECDHE-ECDSA-AES256-SHA:DHE-RSA-AES128-SHA256:DHE-RSA-AES128-SHA:DHE-DSS-AES128-SHA256:DHE-RSA-AES256-SHA256:DHE-DSS-AES256-SHA:DHE-RSA-AES256-SHA:AES128-GCM-SHA256:AES256-GCM-SHA384:ECDHE-RSA-RC4-SHA:ECDHE-ECDSA-RC4-SHA:AES128:AES256:RC4-SHA:HIGH:!aNULL:!eNULL:!EXPORT:!DES:!3DES:!MD5:!PSK;
        ssl_prefer_server_ciphers  on;

        # Optimize SSL by caching session parameters for 10 minutes. This cuts down on the number of expensive SSL handshakes.
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

### <a name="base64-encode-secrets-and-configuration-file"></a>Секреты кодировку Base64 и файл конфигурации

Кодировку Base64 в файл конфигурации Nginx, SSL-сертификат и ключ SSL. Закодированное содержимое позволяет настроить контейнер Nginx.

```console
cat nginx.conf | base64 -w 0 > base64-nginx.conf
cat ssl.crt | base64 -w 0 > base64-ssl.crt
cat ssl.key | base64 -w 0 > base64-ssl.key
```

## <a name="deploy-container-group"></a>Развертывание группы контейнеров

Теперь развертывание группы контейнеров, указав контейнера конфигурации в [yaml-файл](container-instances-multi-container-yaml.md).

### <a name="create-yaml-file"></a>Создание yaml-файла

Скопируйте следующий код YAML в новый файл с именем `deploy-aci.yaml`. В Azure Cloud Shell можно использовать Visual Studio Code для создания файла в рабочем каталоге:

```console
code deploy-aci.yaml
```

Введите в кодировке base64 содержимое файлов, как показано в разделе `secret`. Во время развертывания, эти файлы будут добавляться к [том secret](container-instances-volume-secret.md) в группе контейнеров. В этом примере секретный том монтируется в контейнер Nginx.

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
      ssl.crt: <base64-ssl.crt>
      ssl.key: <base64-ssl.key>
      nginx.conf: <base64-nginx.conf>
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

Создайте группу ресурсов с помощью [Создание группы az](/cli/azure/group#az-group-create) команды:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Развертывание группы контейнеров с [создать контейнер az](/cli/azure/container#az-container-create) команды, передачи yaml-файл в качестве аргумента.

```azurecli
az container create --resource-group <myResourceGroup> --file deploy-aci.yaml
```

### <a name="view-deployment-state"></a>Просмотр состояния развертывания

Чтобы просмотреть состояние развертывания, воспользуйтесь следующим [az контейнера show](/cli/azure/container#az-container-show) команды:

```azurecli
az container show --resource-group <myResourceGroup> --name app-with-ssl --output table
```

Для успешного развертывания результат аналогичен приведенному ниже:

```console
Name          ResourceGroup    Status    Image                                                    IP:ports             Network    CPU/Memory       OsType    Location
------------  ---------------  --------  -------------------------------------------------------  -------------------  ---------  ---------------  --------  ----------
app-with-ssl  myresourcegroup  Running   mcr.microsoft.com/azuredocs/nginx, aci-helloworld        52.157.22.76:443     Public     1.0 core/1.5 gb  Linux     westus
```

## <a name="verify-ssl-connection"></a>Проверка SSL-соединения

Чтобы просмотреть выполняющееся приложение, перейдите к его IP-адрес в браузере. Например, IP-адрес, показанный в этом примере является `52.157.22.76`. Необходимо использовать `https://<IP-ADDRESS>` чтобы увидеть работающее приложение, из-за конфигурации сервера Nginx. Пытается подключиться с помощью `http://<IP-ADDRESS>` ошибкой.

![Снимок экрана браузера: приложение, выполняющееся в экземпляре контейнера Azure](./media/container-instances-container-group-ssl/aci-app-ssl-browser.png)

> [!NOTE]
> Так как в этом примере используется самозаверяющий сертификат, а не из центра сертификации, в браузере отобразится предупреждение системы безопасности при подключении к сайту по протоколу HTTPS. Это ожидаемое поведение.
>

## <a name="next-steps"></a>Дальнейшие действия

В этой статье показано, как настроить контейнер Nginx, чтобы включить SSL-подключения к веб-приложения, работающего в группе контейнеров. Вы можете адаптировать этот пример для приложения, прослушивающие порты, отличные от порта 80. Можно также обновить файл конфигурации Nginx для автоматического перенаправления подключения через порт 80 (HTTP) для использования протокола HTTPS.

Хотя в этой статье используются Nginx в расширение, можно использовать другого поставщика SSL, такие как [Caddy](https://caddyserver.com/).

Другой способ включения SSL в группе контейнеров — развертывание группы в [виртуальной сети Azure](container-instances-vnet.md) с [шлюза приложений Azure](../application-gateway/overview.md). Шлюз можно настроить как конечную точку SSL. Пример см. в разделе [шаблон развертывания](https://github.com/Azure/azure-quickstart-templates/tree/master/201-aci-wordpress-vnet) можно адаптировать для включения завершение запросов SSL на шлюзе.
