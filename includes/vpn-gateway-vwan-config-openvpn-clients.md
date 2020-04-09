---
title: включить файл
description: включить файл
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/17/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 55fa01d100c60c6411774373428ff4bbd9a56822
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/09/2020
ms.locfileid: "80986650"
---
## <a name="windows-clients"></a><a name="windows"></a>Клиенты Windows

1. Скачать и установить клиент OpenVPN (версия 2.4 или выше) с официального [веб-сайта OpenVPN](https://openvpn.net/index.php/open-source/downloads.html).
2. Загрузите профиль VPN для шлюза. Это можно сделать со вкладки конфигурации "точка — сеть" на портале Azure или с "New-AzVpnClientConfiguration" в PowerShell.
3. Распакуйте профиль. Затем откройте файл конфигурации *vpnconfig.ovpn* из папки OpenVPN в Блокноте.
4. Экспортировать созданный и загруженный в конфигурацию P2S сертификат клиента от точки к сайту на шлюзе. Используйте следующие ссылки статьи:

   * Инструкции [VPN Gateway](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md#clientexport)
   
   * [Виртуальные](../articles/virtual-wan/certificates-point-to-site.md#clientexport) инструкции WAN
5. Извлеките закрытый ключ и отпечаток base64 из *PFX-файла*. Это можно сделать несколькими способами. Это можно сделать с помощью OpenSSL на компьютере. Файл *profileinfo.txt* содержит закрытый ключ, отпечаток для ЦС и сертификат клиента. Не забудьте использовать отпечаток сертификата клиента.

   ```
   openssl pkcs12 -in "filename.pfx" -nodes -out "profileinfo.txt"
   ```
6. Откройте *profileinfo.txt* в Блокноте. Чтобы получить отпечаток сертификата клиента (дочерний), выделите текст между "-----BEGIN CERTIFICATE-----" и "-----END CERTIFICATE-----" (включая эти строки) для дочернего сертификата и скопируйте его. Дочерний сертификат можно определить, просмотрев строку subject=/.
7. Переключитесь на файл *vpnconfig.ovpn*, который вы открыли в Блокноте на шаге 3. Найдите раздел, указанный ниже, и замените весь код между cert и /cert.

   ```
   # P2S client certificate
   # please fill this field with a PEM formatted cert
   <cert>
   $CLIENTCERTIFICATE
   </cert>
   ```
8. Откройте файл *profileinfo.txt* в Блокноте. Чтобы получить закрытый ключ, выберите текст (в том числе и между) "-----BEGIN PRIVATE KEY-----" и "-----END PRIVATE KEY-----" и скопируйте его.
9. Вернитесь к файлу vpnconfig.ovpn в Блокноте и найдите этот раздел. Вставьте закрытый ключ, заменив все между и key и /key.

   ```
   # P2S client root certificate private key
   # please fill this field with a PEM formatted key
   <key>
   $PRIVATEKEY
   </key>
   ```
10. Не изменяйте остальные поля. Для подключения к VPN используйте заполненную конфигурацию на входе клиента.
11. Скопируйте файл vpnconfig.ovpn в папку C:\Program Files\OpenVPN\config.
12. Щелкните правой кнопкой мыши значок OpenVPN в области уведомлений и щелкните "Подключить".

## <a name="mac-clients"></a><a name="mac"></a>Клиенты Mac

1. Скачать и установить клиент OpenVPN, например [TunnelBlick](https://tunnelblick.net/downloads.html). 
2. Загрузите профиль VPN для шлюза. Это можно сделать со вкладки конфигурации "точка — сеть" на портале Azure или с помощью "New-AzVpnClientConfiguration" в PowerShell.
3. Распакуйте профиль. Откройте файл конфигурации vpnconfig.ovpn из папки OpenVPN в текстовом редакторе.
4. Заполните раздел сертификата клиента подключения "точка — сеть" открытым ключом сертификата клиента P2S в формате base64. В сертификате с форматированием PEM вы можете просто открыть файл CER и скопировать ключ в формате base64, находящийся между заголовками сертификата. Используйте следующие ссылки статьи для получения информации о том, как экспортировать сертификат, чтобы получить закодированный общедоступный ключ:

   * Инструкции [VPN Gateway](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md#cer) 
   
   * [Виртуальные](../articles/virtual-wan/certificates-point-to-site.md#cer) инструкции WAN
5. Заполните раздел секретного ключа закрытым ключом сертификата клиента P2S в base64. Ознакомиться с личным ключом На сайте OpenVPN можно на сайте [«Экспорт»](https://openvpn.net/community-resources/how-to/#pki) для получения информации о том, как извлечь личный ключ.
6. Не изменяйте остальные поля. Для подключения к VPN используйте заполненную конфигурацию на входе клиента.
7. Дважды щелкните файл профиля, чтобы создать профиль в Tunnelblick.
8. Запуск Tunnelblick из папки приложений.
9. Нажмите на значок Tunnelblick в лоток системы и выбрать подключиться.

> [!IMPORTANT]
>Протокол OpenVPN поддерживают только iOS 11.0 и MacOS 10.13 и их более поздние версии.
>
## <a name="ios-clients"></a><a name="iOS"></a>Клиенты iOS

1. Установите клиент OpenVPN (версия 2.4 или выше) из магазина Приложений.
2. Загрузите профиль VPN для шлюза. Это можно сделать со вкладки конфигурации "точка — сеть" на портале Azure или с помощью "New-AzVpnClientConfiguration" в PowerShell.
3. Распакуйте профиль. Откройте файл конфигурации vpnconfig.ovpn из папки OpenVPN в текстовом редакторе.
4. Заполните раздел сертификата клиента подключения "точка — сеть" открытым ключом сертификата клиента P2S в формате base64. В сертификате с форматированием PEM вы можете просто открыть файл CER и скопировать ключ в формате base64, находящийся между заголовками сертификата. Используйте следующие ссылки статьи для получения информации о том, как экспортировать сертификат, чтобы получить закодированный общедоступный ключ:

   * Инструкции [VPN Gateway](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md#cer) 
   
   * [Виртуальные](../articles/virtual-wan/certificates-point-to-site.md#cer) инструкции WAN
5. Заполните раздел секретного ключа закрытым ключом сертификата клиента P2S в base64. Ознакомиться с [личным ключом](https://openvpn.net/community-resources/how-to/#pki) на сайте OpenVPN можно получить информацию о том, как извлечь личный ключ.
6. Не изменяйте остальные поля.
7. Отправьте файл профиля (.ovpn) на свой почтовый ящик, настроенный в почтовом приложении на вашем iPhone. 
8. Откройте электронную почту в почтовом приложении на iPhone и нажмите на прикрепленный файл

    ![Открытая электронная почта](./media/vpn-gateway-vwan-config-openvpn-clients/ios2.png)

9. Нажмите на **Подробнее,** если вы не видите **опцию Copy to OpenVPN**

    ![Подробнее](./media/vpn-gateway-vwan-config-openvpn-clients/ios3.png)

10. Нажмите на **копию OpenVPN** 

    ![Копирование openVPN](./media/vpn-gateway-vwan-config-openvpn-clients/ios4.png)

11. Нажмите на **ADD** на странице **профиля импорта**

    ![Добавить](./media/vpn-gateway-vwan-config-openvpn-clients/ios5.png)

12. Нажмите на **ADD** на странице **импортного профиля**

    ![Нажмите ADD](./media/vpn-gateway-vwan-config-openvpn-clients/ios6.png)

13. Запустите приложение OpenVPN и сдвиньте переключатель на странице **профиля** для подключения

    ![Подключение](./media/vpn-gateway-vwan-config-openvpn-clients/ios8.png)


## <a name="linux-clients"></a><a name="linux"></a>Клиенты Linux

1. Запустите новый сеанс терминала. Новый сеанс можно начать, нажав CTRL+ALT+Т.
2. Для установки необходимых компонентов введите следующую команду.

   ```
   sudo apt-get install openvpn
   sudo apt-get -y install network-manager-openvpn
   sudo service network-manager restart
   ```
3. Загрузите профиль VPN для шлюза. Это можно сделать со вкладки конфигурации "точка — сеть" на портале Azure.
4. Экспортируйте сертификат клиента P2S, который вы создали и отправили в конфигурацию P2S на шлюзе. Используйте следующие ссылки статьи:

   * Инструкции [VPN Gateway](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md#clientexport) 
   
   * [Виртуальные](../articles/virtual-wan/certificates-point-to-site.md#clientexport) инструкции WAN
5. Извлеките закрытый ключ и отпечаток base64 из PFX-файла. Это можно сделать несколькими способами. Это можно сделать с помощью OpenSSL на компьютере.

    ```
    openssl pkcs12 -in "filename.pfx" -nodes -out "profileinfo.txt"
    ```
   Файл *profileinfo.txt* содержит закрытый ключ, отпечаток для ЦС и сертификат клиента. Не забудьте использовать отпечаток сертификата клиента.

6. Откройте файл *profileinfo.txt* в текстовом редакторе. Чтобы получить отпечаток сертификата клиента (дочерний), выделите текст между "-----BEGIN CERTIFICATE-----" и "-----END CERTIFICATE-----", включая эти строки, для дочернего сертификата и скопируйте его. Дочерний сертификат можно определить, просмотрев строку subject=/.

7. Откройте файл *vpnconfig.ovpn* и найдите раздел, показанный ниже. Замените весь код между cert и /cert.

   ```
   # P2S client certificate
   # please fill this field with a PEM formatted cert
   <cert>
   $CLIENTCERTIFICATE
   </cert>
   ```
8. Откройте файл profileinfo.txt в текстовом редакторе. Чтобы получить закрытый ключ, выберите текст, включаю и между "-----BEGIN PRIVATE KEY-----" и "-----END PRIVATE KEY-----" и скопируйте его.

9. Откройте файл vpnconfig.ovpn в текстовом редакторе и найдите этот раздел. Вставьте закрытый ключ, заменив все между и key и /key.

   ```
   # P2S client root certificate private key
   # please fill this field with a PEM formatted key
   <key>
   $PRIVATEKEY
   </key>
   ```

10. Не изменяйте остальные поля. Для подключения к VPN используйте заполненную конфигурацию на входе клиента.
11. Чтобы подключиться с помощью командной строки, введите следующую команду:
  
    ```
    sudo openvpn --config <name and path of your VPN profile file>&
    ```
12. Чтобы подключиться с помощью графического пользовательского интерфейса, перейдите в параметры системы.
13. Нажмите, **+** чтобы добавить новое СОЕДИНЕНИЕ VPN.
14. В разделе **Добавить VPN** выберите **Импорт из файла...**
15. Просмотрите файл профиля и дважды щелкните или выберите **Открыть**.
16. Щелкните **Добавить** в окне **Добавить VPN**.
  
    ![Импорт из файла](./media/vpn-gateway-vwan-config-openvpn-clients/import.png)
17. Вы можете подключиться, выбрав для VPN **Вкл.** на странице **Параметры сети** или под значком сети на панели задач.
