---
title: Ограничение доступа к kubeconfig в Службе Azure Kubernetes (AKS)
description: Узнайте, как управлять доступом к файлу конфигурации Kubernetes (kubeconfig) для администраторов и пользователей кластера.
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 01/28/2020
ms.author: mlearned
ms.openlocfilehash: 8b9b1bf2a24002d606659c81e6fc426614fd5fd6
ms.sourcegitcommit: d12880206cf9926af6aaf3bfafda1bc5b0ec7151
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/10/2020
ms.locfileid: "77114202"
---
# <a name="use-azure-role-based-access-controls-to-define-access-to-the-kubernetes-configuration-file-in-azure-kubernetes-service-aks"></a>Чтобы определить доступ к файлу конфигурации Kubernetes в службе Azure Kubernetes (AKS), используйте элементы управления доступом на основе ролей.

Вы можете взаимодействовать с кластерами Kubernetes с помощью инструмента `kubectl`. Azure CLI позволяет без труда получить учетные данные для доступа и сведения о конфигурации, чтобы подключиться к кластерам AKS с помощью `kubectl`. Чтобы ограничить доступ к сведениям о конфигурации Kubernetes (*kubeconfig*) и ограничить разрешения, можно использовать элементы управления доступом Azure на основе ролей (RBAC).

В этой статье показано, как назначить роли RBAC, чтобы ограничить доступ к сведениям о конфигурации для кластера AKS.

## <a name="before-you-begin"></a>Перед началом

В этой статье предполагается, что у вас есть кластер AKS. Если вам нужен кластер AKS, ознакомьтесь с кратким руководством по AKS, [используя Azure CLI][aks-quickstart-cli] или [с помощью портал Azure][aks-quickstart-portal].

В этой статье также предполагается, что вы используете Azure CLI версии 2.0.65 или более поздней. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0][azure-cli-install].

## <a name="available-cluster-roles-permissions"></a>Доступные разрешения ролей кластера

При взаимодействии с кластером AKS с помощью инструмента `kubectl` используется файл конфигурации, который определяет сведения о подключении кластера. Этот файл конфигурации обычно хранится в *~/.KUBE/config.* . В этом файле *kubeconfig* можно определить несколько кластеров. Переключение между кластерами выполняется с помощью команды [kubectl config use-context][kubectl-config-use-context] .

Команда [AZ AKS Get-Credential][az-aks-get-credentials] позволяет получить учетные данные для доступа к кластеру AKS и объединяет их в файл *kubeconfig* . Доступ к этим учетным данным также можно контролировать с помощью управления доступом Azure на основе ролей (RBAC). Эти роли Azure RBAC позволяют определить, кто может получить файл *kubeconfig* и какие он получит разрешения в пределах кластера.

Ниже описаны две встроенные роли:

* **Роль администратора кластера в Службе Azure Kubernetes**  
  * Разрешение доступа к вызову API *Microsoft.ContainerService/managedClusters/listClusterAdminCredential/action*. Этот вызов API [перечисляет учетные данные администратора кластера][api-cluster-admin].
  * Скачивание файла *kubeconfig* для роли *clusterAdmin*.
* **Роль пользователя кластера в Службе Azure Kubernetes**
  * Разрешение доступа к вызову API *Microsoft.ContainerService/managedClusters/listClusterUserCredential/action*. Этот вызов API [перечисляет учетные данные пользователя кластера][api-cluster-user].
  * Скачивание файла *kubeconfig* для роли *clusterUser*.

Эти роли RBAC можно применять к пользователю или группе Azure Active Directory (AD).

> ! МЕТИМ В кластерах, использующих Azure AD, пользователи с ролью *клустерусер* имеют пустой файл *kubeconfig* , который запрашивает вход в систему. После входа пользователи получают доступ на основе параметров пользователя или группы Azure AD. Пользователи с ролью *клустерадмин* имеют доступ администратора.
>
> Кластеры, не использующие Azure AD, используют только роль *клустерадмин* .

## <a name="assign-role-permissions-to-a-user-or-group"></a>Назначение разрешений роли пользователю или группе

Чтобы назначить одну из доступных ролей, необходимо получить идентификатор ресурса кластера AKS и идентификатор учетной записи пользователя или группы Azure AD. В следующих примерах команд:

* Получите идентификатор ресурса кластера с помощью команды [AZ AKS показывать][az-aks-show] для кластера с именем *myAKSCluster* в группе ресурсов *myResourceGroup* . При необходимости укажите имя вашей группы ресурсов и кластера.
* Используйте команды [AZ Account шоу][az-account-show] и [AZ AD user демонстрация][az-ad-user-show] , чтобы получить идентификатор пользователя.
* Наконец, назначьте роль с помощью команды [AZ Role назначение Create][az-role-assignment-create] .

В следующем примере *роль администратора кластера Azure Kubernetes* назначается отдельной учетной записи пользователя:

```azurecli-interactive
# Get the resource ID of your AKS cluster
AKS_CLUSTER=$(az aks show --resource-group myResourceGroup --name myAKSCluster --query id -o tsv)

# Get the account credentials for the logged in user
ACCOUNT_UPN=$(az account show --query user.name -o tsv)
ACCOUNT_ID=$(az ad user show --id $ACCOUNT_UPN --query objectId -o tsv)

# Assign the 'Cluster Admin' role to the user
az role assignment create \
    --assignee $ACCOUNT_ID \
    --scope $AKS_CLUSTER \
    --role "Azure Kubernetes Service Cluster Admin Role"
```

> [!TIP]
> Если вы хотите назначить разрешения группе Azure AD, обновите параметр `--assignee`, показанный в предыдущем примере, с ИДЕНТИФИКАТОРом объекта для *группы* , а не с *пользователем*. Чтобы получить идентификатор объекта для группы, используйте команду [AZ AD Group показ][az-ad-group-show] . В следующем примере возвращается идентификатор объекта для группы Azure AD с именем *AppDev*: `az ad group show --group appdev --query objectId -o tsv`

При необходимости можно изменить предыдущее назначение на *роль пользователя кластера*.

В следующем примере выходных данных показано, что назначение ролей выполнено успешно:

```
{
  "canDelegate": null,
  "id": "/subscriptions/<guid>/resourcegroups/myResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster/providers/Microsoft.Authorization/roleAssignments/b2712174-5a41-4ecb-82c5-12b8ad43d4fb",
  "name": "b2712174-5a41-4ecb-82c5-12b8ad43d4fb",
  "principalId": "946016dd-9362-4183-b17d-4c416d1f8f61",
  "resourceGroup": "myResourceGroup",
  "roleDefinitionId": "/subscriptions/<guid>/providers/Microsoft.Authorization/roleDefinitions/0ab01a8-8aac-4efd-b8c2-3ee1fb270be8",
  "scope": "/subscriptions/<guid>/resourcegroups/myResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster",
  "type": "Microsoft.Authorization/roleAssignments"
}
```

## <a name="get-and-verify-the-configuration-information"></a>Получение и проверка сведений о конфигурации

С назначенными ролями RBAC используйте команду [AZ AKS Get-Credential][az-aks-get-credentials] , чтобы получить определение *KUBECONFIG* для кластера AKS. В приведенном ниже примере возвращаются учетные данные *--admin*, которые будут работать правильно при наличии *роли администратора кластера*:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --admin
```

Затем можно использовать команду [kubectl config View][kubectl-config-view] , чтобы убедиться, что *контекст* кластера показывает, что были применены сведения о конфигурации администратора.

```
$ kubectl config view

apiVersion: v1
clusters:
- cluster:
    certificate-authority-data: DATA+OMITTED
    server: https://myaksclust-myresourcegroup-19da35-4839be06.hcp.eastus.azmk8s.io:443
  name: myAKSCluster
contexts:
- context:
    cluster: myAKSCluster
    user: clusterAdmin_myResourceGroup_myAKSCluster
  name: myAKSCluster-admin
current-context: myAKSCluster-admin
kind: Config
preferences: {}
users:
- name: clusterAdmin_myResourceGroup_myAKSCluster
  user:
    client-certificate-data: REDACTED
    client-key-data: REDACTED
    token: e9f2f819a4496538b02cefff94e61d35
```

## <a name="remove-role-permissions"></a>Удаление разрешений ролей

Чтобы удалить назначения ролей, используйте команду [AZ Role назначение Delete][az-role-assignment-delete] . Укажите идентификатор учетной записи и идентификатор ресурса кластера, полученные в предыдущих командах. Если роль назначается группе, а не пользователю, укажите соответствующий идентификатор объекта группы, а не идентификатор объекта учетной записи для параметра `--assignee`.

```azurecli-interactive
az role assignment delete --assignee $ACCOUNT_ID --scope $AKS_CLUSTER
```

## <a name="next-steps"></a>Дальнейшие действия

Чтобы обеспечить повышенную безопасность доступа к кластерам AKS, [Интегрируйте проверку подлинности Azure Active Directory][aad-integration].

<!-- LINKS - external -->
[kubectl-config-use-context]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#config
[kubectl-config-view]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#config

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[azure-rbac]: ../role-based-access-control/overview.md
[api-cluster-admin]: /rest/api/aks/managedclusters/listclusteradmincredentials
[api-cluster-user]: /rest/api/aks/managedclusters/listclusterusercredentials
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-account-show]: /cli/azure/account#az-account-show
[az-ad-user-show]: /cli/azure/ad/user#az-ad-user-show
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-role-assignment-delete]: /cli/azure/role/assignment#az-role-assignment-delete
[aad-integration]: azure-ad-integration.md
[az-ad-group-show]: /cli/azure/ad/group#az-ad-group-show
