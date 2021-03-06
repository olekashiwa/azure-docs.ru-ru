---
title: Часто задаваемые вопросы о Azure Dev Spaces
services: azure-dev-spaces
ms.date: 01/28/2020
ms.topic: conceptual
description: Найдите ответы на некоторые распространенные вопросы о Azure Dev Spaces
keywords: 'Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, containers, Helm, service mesh, service mesh routing, kubectl, k8s '
ms.openlocfilehash: 964fa9ec4948bf178c310af8e35913fda5f70c0f
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/01/2020
ms.locfileid: "76934181"
---
# <a name="frequently-asked-questions-about-azure-dev-spaces"></a>Часто задаваемые вопросы о Azure Dev Spaces

Здесь рассматриваются часто задаваемые вопросы о Azure Dev Spaces.

## <a name="which-azure-regions-currently-provide-azure-dev-spaces"></a>Какие регионы Azure в настоящее время предоставляют Azure Dev Spaces?

Полный список доступных регионов см. в разделе [Поддерживаемые регионы][supported-regions] .

## <a name="can-i-use-azure-dev-spaces-with-existing-dockerfiles-or-helm-charts"></a>Можно ли использовать Azure Dev Spaces с существующими диаграммами файлы dockerfile или Helm?

Да, если проект уже содержит диаграмму Dockerfile или Helm, вы можете использовать эти файлы с Azure Dev Spaces. При запуске `azds prep`используйте параметр `--chart` и укажите расположение диаграммы. Azure Dev Spaces будет по-прежнему создавать файл *аздс. YAML* и *Dockerfile. Разработка* , но он не заменит или не изменит существующую диаграмму Dockerfile или Helm. Может потребоваться изменить *аздс. YAML* и *Dockerfile. Разработка* файлов, чтобы все работало правильно с существующим приложением при выполнении `azds up`.

При использовании собственной диаграммы Dockerfile или Helm существуют следующие ограничения.
* Если используется только один Dockerfile, он должен включать все необходимое для реализации сценариев разработки, таких как языковой пакет SDK не только для среды выполнения. При использовании отдельного Dockerfile для Azure Dev Spaces, например Dockerfile. Development, все, что необходимо для включения сценариев разработки, должно быть включено в этот Dockerfile.
* Helm диаграмма должна поддерживать передачу части или всего тега Image в виде значения из Values *. YAML*.
* Если вы изменяете что-то с помощью входящего, можно также обновить диаграмму Helm, чтобы использовать входящее решение, предоставляемое Azure Dev Spaces.
* Если вы хотите использовать [возможности маршрутизации, предоставляемые Azure dev Spaces][dev-spaces-routing], все службы для отдельного проекта должны быть размещены в одном пространстве имен Kubernetes и должны быть развернуты с простым именем, например *Service-a*. В стандартных диаграммах Helm это обновление именования можно выполнить, указав значение для свойства *фуллнамеоверриде* .

Чтобы сравнить собственную диаграмму Dockerfile или Helm с существующей версией, которая работает с Azure Dev Spaces, ознакомьтесь с файлами, созданными в [кратком руководстве][quickstart-cli].


## <a name="can-i-modify-the-files-generated-by-azure-dev-spaces"></a>Можно ли изменить файлы, созданные Azure Dev Spaces?

Да, можно изменить файл *аздс. YAML* , Dockerfile и диаграмму Helm, [созданную Azure dev Spaces при подготовке проекта][dev-spaces-prep]. Изменение этих файлов изменяет процесс сборки и запуска проекта.

## <a name="can-i-use-azure-dev-spaces-without-a-public-ip-address"></a>Можно ли использовать Azure Dev Spaces без общедоступного IP-адреса?

Нет, вы не можете подготавливать Azure Dev Spaces в кластере AKS без общедоступного IP-адреса. [Azure dev Spaces для маршрутизации требуется][dev-spaces-routing]общедоступный IP-адрес.

## <a name="can-i-use-my-own-ingress-with-azure-dev-spaces"></a>Можно ли использовать собственный входной параметр с Azure Dev Spaces?

Да, можно настроить собственный входящий трафик на стороне, созданной Azure Dev Spaces. Например, можно использовать [траефик][ingress-traefik] или [nginx][ingress-nginx].

## <a name="can-i-use-https-with-azure-dev-spaces"></a>Можно ли использовать HTTPS с Azure Dev Spaces?

Да, вы можете настроить собственный входной параметр с помощью протокола HTTPS, используя [траефик][ingress-https-traefik] или [nginx][ingress-https-nginx].

## <a name="can-i-use-azure-dev-spaces-on-a-cluster-that-uses-cni-rather-than-kubenet"></a>Можно ли использовать Azure Dev Spaces в кластере, который использует CNI, а не кубенет? 

Да, можно использовать Azure Dev Spaces в кластере AKS, который использует CNI для работы в сети. Например, можно использовать Azure Dev Spaces в кластере AKS с [существующими контейнерами Windows][windows-containers], которые используют CNI для работы в сети. Дополнительные сведения об использовании CNI для работы в сети с Azure Dev Spaces см. [здесь](configure-networking.md#using-azure-cni).

## <a name="can-i-use-azure-dev-spaces-with-windows-containers"></a>Можно ли использовать Azure Dev Spaces с контейнерами Windows?

В настоящее время Azure Dev Spaces предназначен для работы только в Pod и узлах Linux, но вы можете запускать Azure Dev Spaces в кластере AKS с [существующими контейнерами Windows][windows-containers].

## <a name="can-i-use-azure-dev-spaces-on-aks-clusters-with-api-server-authorized-ip-address-ranges-enabled"></a>Можно ли использовать Azure Dev Spaces в кластерах AKS с разрешенными диапазонами IP-адресов для сервера API?

Да, можно использовать Azure Dev Spaces в кластерах AKS с разрешенными [диапазонами IP-адресов сервера API][aks-auth-range] . Дополнительные сведения об использовании кластеров AKS с разрешенными диапазонами IP-адресов сервера API с поддержкой Azure Dev Spaces доступны [здесь](configure-networking.md#using-api-server-authorized-ip-ranges).

## <a name="can-i-use-azure-dev-spaces-on-aks-clusters-with-restricted-egress-traffic-for-cluster-nodes"></a>Можно ли использовать Azure Dev Spaces в кластерах AKS с ограниченным трафиком исходящего трафика для узлов кластера?

Да, вы можете использовать Azure Dev Spaces в кластерах AKS с [ограниченным трафиком исходящего трафика для узлов кластера][aks-restrict-egress-traffic] , включенных после того, как правильные полные доменные имена разрешены. Дополнительные сведения об использовании кластеров AKS с ограниченным трафиком исходящего трафика для узлов кластера, включенных с Azure Dev Spaces, можно найти [здесь](configure-networking.md#ingress-and-egress-network-traffic-requirements).

[aks-auth-range]: ../aks/api-server-authorized-ip-ranges.md
[aks-auth-range-create]: ../aks/api-server-authorized-ip-ranges.md#create-an-aks-cluster-with-api-server-authorized-ip-ranges-enabled
[aks-auth-range-ranges]: https://github.com/Azure/dev-spaces/tree/master/public-ips
[aks-auth-range-update]: ../aks/api-server-authorized-ip-ranges.md#update-a-clusters-api-server-authorized-ip-ranges
[aks-restrict-egress-traffic]: ../aks/limit-egress-traffic.md
[dev-spaces-prep]: how-dev-spaces-works.md#prepare-your-code
[dev-spaces-routing]: how-dev-spaces-works.md#how-routing-works
[ingress-nginx]: how-to/ingress-https-nginx.md#configure-a-custom-nginx-ingress-controller
[ingress-traefik]: how-to/ingress-https-traefik.md#configure-a-custom-traefik-ingress-controller
[ingress-https-nginx]: how-to/ingress-https-nginx.md#configure-the-nginx-ingress-controller-to-use-https
[ingress-https-traefik]: how-to/ingress-https-traefik.md#configure-the-traefik-ingress-controller-to-use-https
[quickstart-cli]: quickstart-cli.md
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[windows-containers]: how-to/run-dev-spaces-windows-containers.md