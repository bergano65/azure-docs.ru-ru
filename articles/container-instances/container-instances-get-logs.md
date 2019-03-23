---
title: Получение журналов и событий контейнеров в службе "Экземпляры контейнеров Azure"
description: Узнайте, как отладить журналы и события контейнеров в службе "Экземпляры контейнеров Azure"
services: container-instances
author: dlepow
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 03/21/2019
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: f286e2136b12a88e65e40f8fb956542233f71715
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/22/2019
ms.locfileid: "58369009"
---
# <a name="retrieve-container-logs-and-events-in-azure-container-instances"></a>Получение журналов и событий контейнеров в службе "Экземпляры контейнеров Azure"

Если у вас есть контейнер, который работает некорректно, начните с просмотра журналов с помощью команды [az container logs][az-container-logs]. Затем перенаправьте его стандартные выходные данные и ошибки с помощью [az container attach][az-container-attach].

## <a name="view-logs"></a>Просмотр журналов

Для просмотра журналов из кода приложения в контейнере можно использовать команду [az container logs][az-container-logs].

Ниже приводятся выходные данные журнала, созданные контейнером на основе задач, описанным в статье [Выполнение задачи-контейнера в службе "Экземпляры контейнеров Azure"](container-instances-restart-policy.md), которому на обработку передан недопустимый URL-адрес.

```console
$ az container logs --resource-group myResourceGroup --name mycontainer
Traceback (most recent call last):
  File "wordcount.py", line 11, in <module>
    urllib.request.urlretrieve (sys.argv[1], "foo.txt")
  File "/usr/local/lib/python3.6/urllib/request.py", line 248, in urlretrieve
    with contextlib.closing(urlopen(url, data)) as fp:
  File "/usr/local/lib/python3.6/urllib/request.py", line 223, in urlopen
    return opener.open(url, data, timeout)
  File "/usr/local/lib/python3.6/urllib/request.py", line 532, in open
    response = meth(req, response)
  File "/usr/local/lib/python3.6/urllib/request.py", line 642, in http_response
    'http', request, response, code, msg, hdrs)
  File "/usr/local/lib/python3.6/urllib/request.py", line 570, in error
    return self._call_chain(*args)
  File "/usr/local/lib/python3.6/urllib/request.py", line 504, in _call_chain
    result = func(*args)
  File "/usr/local/lib/python3.6/urllib/request.py", line 650, in http_error_default
    raise HTTPError(req.full_url, code, msg, hdrs, fp)
urllib.error.HTTPError: HTTP Error 404: Not Found
```

## <a name="attach-output-streams"></a>Присоединение выходных потоков

Команда [az container attach][az-container-attach] предоставляет диагностические сведения во время запуска контейнера. Она направляет потоки STDOUT и STDERR запущенного контейнера в локальную консоль.

Ниже приводятся выходные данные журнала, созданные контейнером на основе задач, описанным в статье [Выполнение задачи-контейнера в службе "Экземпляры контейнеров Azure"](container-instances-restart-policy.md), которому предоставлен действительный URL-адрес большого текстового файла для обработки.

```console
$ az container attach --resource-group myResourceGroup --name mycontainer
Container 'mycontainer' is in state 'Unknown'...
Container 'mycontainer' is in state 'Waiting'...
Container 'mycontainer' is in state 'Running'...
(count: 1) (last timestamp: 2019-03-21 19:42:39+00:00) pulling image "mcr.microsoft.com/azuredocs/aci-wordcount:latest"
Container 'mycontainer1' is in state 'Running'...
(count: 1) (last timestamp: 2019-03-21 19:42:39+00:00) pulling image "mcr.microsoft.com/azuredocs/aci-wordcount:latest"
(count: 1) (last timestamp: 2019-03-21 19:42:52+00:00) Successfully pulled image "mcr.microsoft.com/azuredocs/aci-wordcount:latest"
(count: 1) (last timestamp: 2019-03-21 19:42:55+00:00) Created container
(count: 1) (last timestamp: 2019-03-21 19:42:55+00:00) Started container

Start streaming logs:
[('the', 22979),
 ('I', 20003),
 ('and', 18373),
 ('to', 15651),
 ('of', 15558),
 ('a', 12500),
 ('you', 11818),
 ('my', 10651),
 ('in', 9707),
 ('is', 8195)]
```

## <a name="get-diagnostic-events"></a>Получение диагностических событий

Если контейнер не может развернуться успешно, просмотрите диагностические сведения, предоставляемые поставщиком ресурсов службы "Экземпляры контейнеров Azure". Чтобы просмотреть события для контейнера, выполните команду [az container show][az-container-show]:

```azurecli-interactive
az container show --resource-group myResourceGroup --name mycontainer
```

Выходные данные содержат основные свойства контейнера, а также события развертывания (здесь показаны в усеченном виде):

```JSON
{
  "containers": [
    {
      "command": null,
      "environmentVariables": [],
      "image": "mcr.microsoft.com/azuredocs/aci-helloworld",
      ...
        "events": [
          {
            "count": 1,
            "firstTimestamp": "2019-03-21T19:46:22+00:00",
            "lastTimestamp": "2019-03-21T19:46:22+00:00",
            "message": "pulling image \"mcr.microsoft.com/azuredocs/aci-helloworld\"",
            "name": "Pulling",
            "type": "Normal"
          },
          {
            "count": 1,
            "firstTimestamp": "2019-03-21T19:46:28+00:00",
            "lastTimestamp": "2019-03-21T19:46:28+00:00",
            "message": "Successfully pulled image \"mcr.microsoft.com/azuredocs/aci-helloworld\"",
            "name": "Pulled",
            "type": "Normal"
          },
          {
            "count": 1,
            "firstTimestamp": "2019-03-21T19:46:31+00:00",
            "lastTimestamp": "2019-03-21T19:46:31+00:00",
            "message": "Created container",
            "name": "Created",
            "type": "Normal"
          },
          {
            "count": 1,
            "firstTimestamp": "2019-03-21T19:46:31+00:00",
            "lastTimestamp": "2019-03-21T19:46:31+00:00",
            "message": "Started container",
            "name": "Started",
            "type": "Normal"
          }
        ],
        "previousState": null,
        "restartCount": 0
      },
      "name": "mycontainer",
      "ports": [
        {
          "port": 80,
          "protocol": null
        }
      ],
      ...
    }
  ],
  ...
}
```
## <a name="next-steps"></a>Дальнейшие действия
Узнайте, как [устранить неполадки контейнера и развертывания](container-instances-troubleshooting.md) для службы "Экземпляры контейнеров Azure".

<!-- LINKS - Internal -->
[az-container-attach]: /cli/azure/container#az-container-attach
[az-container-logs]: /cli/azure/container#az-container-logs
