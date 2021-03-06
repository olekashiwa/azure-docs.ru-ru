---
title: 'VPN-шлюз Azure: Общие сведения о профилях VPN-клиента P2S'
description: Это поможет вам работать с файлом клиентского профиля.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 11/04/2019
ms.author: cherylmc
ms.openlocfilehash: 5386cace7191be60534f0d2fbf4a85b592d1ecdd
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/17/2019
ms.locfileid: "74151623"
---
# <a name="about-p2s-vpn-client-profiles"></a>О профилях VPN-клиента P2S

Скачанный файл профиля содержит сведения, необходимые для настройки VPN-подключения. Эта статья поможет вам получить и понять сведения, необходимые для профиля клиента VPN.

## <a name="1-download-the-file"></a>1. Скачайте файл

Выполните команды ниже. Скопируйте полученный URL-адрес в браузер, чтобы скачать его.

```azurepowershell-interactive
$profile = New-AzVpnClientConfiguration -ResourceGroupName AADAuth -Name AADauthGW -AuthenticationMethod "EapTls"
   
$PROFILE.VpnProfileSASUrl
```

## <a name="2-extract-the-zip-file"></a>2. извлечение ZIP-файла

Извлеките ZIP-файл. Файл содержит следующие папки:

* AzureVPN
* Универсальный
* Опенвпн (если вы включили параметры проверки подлинности Опенвпн и Azure AD на шлюзе. См. раздел [Создание клиента](openvpn-azure-ad-tenant.md).)

## <a name="3-retrieve-information"></a>3. Получение сведений

В папке **AzureVPN** перейдите к файлу ***азуревпнконфиг. XML*** и откройте его в блокноте. Запишите текст между следующими тегами.

```
<audience>          </audience>
<issuer>            </issuer>
<tennant>           </tennant>
<fqdn>              </fqdn>
<serversecret>      </serversecret>
```

## <a name="profile-details"></a>Сведения о профиле

При добавлении подключения используйте сведения, собранные на предыдущем шаге, на странице сведения о профиле. Поля соответствуют следующим сведениям:

   * **Аудитория:** Определяет ресурс получателя, для которого предназначен маркер
   * **Издатель:** Определяет службу маркеров безопасности (STS), которая выдает маркер, а также клиент Azure AD.
   * **Клиент:** Содержит неизменяемый уникальный идентификатор клиента каталога, который выдал токен.
   * **Полное доменное имя:** Полное доменное имя (FQDN) VPN-шлюза Azure;
   * **Серверсекрет:** Общий ключ VPN-шлюза

## <a name="folder-contents"></a>Содержимое папки

* **Папка опенвпн** содержит профиль *ОВПН* , который необходимо изменить, чтобы включить ключ и сертификат. Дополнительные сведения см. в статье [Настройка клиентов опенвпн для VPN-шлюза Azure](vpn-gateway-howto-openvpn-clients.md#windows).

* **Общая папка** содержит общедоступный сертификат сервера и файл файл vpnsettings. XML. Файл файл vpnsettings. XML содержит сведения, необходимые для настройки универсального клиента.

* Скачанный ZIP-файл также может содержать папки **WindowsAmd64** и **WindowsX86** . Эти папки содержат установщик для клиентов SSTP и IKEv2 для Windows. Для их установки требуются права администратора на клиенте.

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о "точка — сеть" см. в разделе [сведения о точках на](point-to-site-about.md)узел.
