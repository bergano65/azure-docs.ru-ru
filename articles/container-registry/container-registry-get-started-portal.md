---
title: Краткое руководство. Создание реестра на портале
description: Быстрый способ изучить создание частного реестра Docker в Реестре контейнеров Azure на портале Azure.
ms.topic: quickstart
ms.date: 03/03/2020
ms.custom: seodec18, mvc
ms.openlocfilehash: 6fe6358655f50ab783b4017efa8ee1db351cd018
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/26/2020
ms.locfileid: "79409294"
---
# <a name="quickstart-create-a-private-container-registry-using-the-azure-portal"></a>Краткое руководство. Создание частного реестра контейнеров с помощью портала Azure

Реестр контейнеров Azure — это частный реестр Docker в Azure, где можно хранить частные образы и связанные артефакты контейнеров Docker и управлять ими. В этом кратком руководстве вы создадите реестр контейнеров с помощью портала Azure. Затем используйте команды Docker, чтобы отправить образ контейнера в реестр, после чего извлеките образ из контейнера и запустите его.

Чтобы войти в реестр для работы с образами контейнеров, в этом кратком руководстве требуется использовать Azure CLI (рекомендуется версия 2.0.55 или более поздняя). Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0][azure-cli].

Также необходим локально установленный модуль Docker. Docker содержит пакеты, которые позволяют быстро настроить Docker в любой системе [Mac][docker-mac], [Windows][docker-windows] или [Linux][docker-linux].

## <a name="sign-in-to-azure"></a>Вход в Azure

Войдите на портал Azure по адресу https://portal.azure.com.

## <a name="create-a-container-registry"></a>Создание реестра контейнеров

Последовательно выберите **Создать ресурс** > **Контейнеры** > **Реестр контейнеров**.

![Создание реестра контейнеров на портале Azure][qs-portal-01]

На вкладке **Основные сведения** введите значения в поле **Группа ресурсов** и **Имя реестра**. Имя реестра должно быть уникальным в пределах Azure и содержать от 5 до 50 буквенно-цифровых символов. В целях этого руководства создайте группу ресурсов в регионе `West US` в расположении с именем `myResourceGroup`. Для **SKU** задайте значение "Базовый". 

![Создание реестра контейнеров на портале Azure][qs-portal-03]

Примите значения по умолчанию для остальных параметров. Щелкните **Просмотр и создание**. Проверив параметры, нажмите кнопку **Создать**.

В этом кратком руководстве описано, как создать реестр ценовой категории *Базовый*. Это оптимальный (недорогой) вариант для разработчиков, которые знакомятся с Реестром контейнеров Azure. Дополнительные сведения об уровнях служб см. в статье [Номера SKU реестра контейнеров][container-registry-skus].

Когда появится сообщение **Развертывание прошло успешно**, выберите реестр контейнеров на портале. 

![Страница обзора реестра контейнеров на портале Azure][qs-portal-05]

Запишите значение **сервера входа**. Это значение используется в следующих шагах при отправке и извлечении изображения с помощью Docker.

## <a name="log-in-to-registry"></a>Вход в раздел реестра

Перед отправкой и извлечением образов контейнеров необходимо войти в экземпляр ACR. Откройте командную оболочку в операционной системе и используйте команду [az acr login][az-acr-login] в Azure CLI. (При входе в систему укажите только имя реестра. Не добавляйте суффикс "azurecr.io".)

```azurecli
az acr login --name <acrName>
```

По завершении команда возвращает `Login Succeeded`. 

[!INCLUDE [container-registry-quickstart-docker-push](../../includes/container-registry-quickstart-docker-push.md)]

## <a name="list-container-images"></a>Список образов контейнеров

Чтобы вывести список образов в вашем реестре, перейдите в реестр на портале и выберите **Репозитории**, а затем выберите репозиторий, созданный с помощью `docker push`.

В этом примере мы выбираем репозиторий **hello-world** и видим образ с тегом `v1` в разделе **Теги**.

![Список образов контейнеров на портале Azure][qs-portal-09]

[!INCLUDE [container-registry-quickstart-docker-pull](../../includes/container-registry-quickstart-docker-pull.md)]

## <a name="clean-up-resources"></a>Очистка ресурсов

Чтобы очистить ресурсы, перейдите к группе ресурсов **myResourceGroup** на портале. После загрузки группы ресурсов щелкните пункт **Удалить группу ресурсов** для удаления группы ресурсов, реестра контейнеров и сохраненных образов контейнеров.

![Удаление группы ресурсов на портале Azure][qs-portal-08]

## <a name="next-steps"></a>Дальнейшие действия

С помощью этого краткого руководства вы создали Реестр контейнеров Azure с использованием портала Azure, отправили образ контейнера, а затем извлекли этот образ оттуда и запустили его. Чтобы продолжить работу с Реестром контейнеров Azure, перейдите к следующим руководствам.

> [!div class="nextstepaction"]
> [Руководства по использованию Реестра контейнеров Azure][container-registry-tutorial-quick-task]

<!-- IMAGES -->
[qs-portal-01]: ./media/container-registry-get-started-portal/qs-portal-01.png
[qs-portal-02]: ./media/container-registry-get-started-portal/qs-portal-02.png
[qs-portal-03]: ./media/container-registry-get-started-portal/qs-portal-03.png
[qs-portal-05]: ./media/container-registry-get-started-portal/qs-portal-05.png
[qs-portal-08]: ./media/container-registry-get-started-portal/qs-portal-08.png
[qs-portal-09]: ./media/container-registry-get-started-portal/qs-portal-09.png

<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-pull]: https://docs.docker.com/engine/reference/commandline/pull/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-rmi]: https://docs.docker.com/engine/reference/commandline/rmi/
[docker-run]: https://docs.docker.com/engine/reference/commandline/run/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - internal -->
[container-registry-tutorial-quick-task]: container-registry-tutorial-quick-task.md
[container-registry-skus]: container-registry-skus.md
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-login]: /cli/azure/acr#az-acr-login
