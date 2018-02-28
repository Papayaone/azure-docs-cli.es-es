---
title: "Administración de varias nubes con la CLI de Azure 2.0"
description: "Cree, inicie sesión y administre varias nubes con la CLI de Azure 2.0."
author: sptramer
manager: routlaw
ms.author: sttramer
ms.date: 10/20/2017
ms.topic: article
ms.prod: azure
ms.technology: azure
ms.devlang: azurecli
ms.openlocfilehash: ee6b1b6b1e611229ba6e6e0c4b2ca2de6f7ceaee
ms.sourcegitcommit: b93a19222e116d5880bbe64c03507c64e190331e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/15/2018
---
# <a name="managing-multiple-clouds-with-azure-cli-20"></a><span data-ttu-id="9066f-103">Administración de varias nubes con la CLI de Azure 2.0</span><span class="sxs-lookup"><span data-stu-id="9066f-103">Managing multiple clouds with Azure CLI 2.0</span></span>

<span data-ttu-id="9066f-104">Si trabaja en diferentes regiones o usa [Azure Stack](https://docs.microsoft.com/azure/azure-stack/user/), puede que necesite utilizar más de una nube.</span><span class="sxs-lookup"><span data-stu-id="9066f-104">If you work across different regions or use [Azure Stack](https://docs.microsoft.com/azure/azure-stack/user/), you may need to use more than one cloud.</span></span> <span data-ttu-id="9066f-105">Microsoft pone a su disposición nubes para el cumplimiento de las leyes locales.</span><span class="sxs-lookup"><span data-stu-id="9066f-105">Microsoft provides clouds for compliance with regional laws, which are available for your use.</span></span> <span data-ttu-id="9066f-106">En este artículo se muestra cómo obtener información sobre las nubes disponibles para su cuenta, cómo cambiar la nube actual y cómo registrar o anular el registro de nuevas nubes para su uso con Azure Stack.</span><span class="sxs-lookup"><span data-stu-id="9066f-106">This article shows you how to get information on clouds available to your account, change the current cloud, and register or unregister new clouds for use with Azure Stack.</span></span>

## <a name="listing-clouds"></a><span data-ttu-id="9066f-107">Listas de nubes</span><span class="sxs-lookup"><span data-stu-id="9066f-107">Listing clouds</span></span>

<span data-ttu-id="9066f-108">Para enumerar las nubes disponibles, use el comando [cloud list](/cli/azure/cloud#list).</span><span class="sxs-lookup"><span data-stu-id="9066f-108">You can list available clouds with the [cloud list](/cli/azure/cloud#list) command.</span></span> <span data-ttu-id="9066f-109">Le indicará qué nube está actualmente activa y cuál es su perfil actual, así como información sobre los nombres de host y los sufijos regionales.</span><span class="sxs-lookup"><span data-stu-id="9066f-109">This tells you which cloud is currently active, what its current profile is, and information on regional suffixes and host names.</span></span>

<span data-ttu-id="9066f-110">Para obtener la nube activa y una lista de todas las nubes disponibles:</span><span class="sxs-lookup"><span data-stu-id="9066f-110">To get the active cloud and a list of all the available clouds:</span></span>

```azurecli
az cloud list --output table
```

```output
IsActive    Name               Profile
----------  -----------------  ---------
True        AzureCloud         latest
            AzureChinaCloud    latest
            AzureUSGovernment  latest
            AzureGermanCloud   latest
```

<span data-ttu-id="9066f-111">La nube actualmente activa tiene `True` en la columna `IsActive`.</span><span class="sxs-lookup"><span data-stu-id="9066f-111">The currently active cloud has `True` in the `IsActive` column.</span></span> <span data-ttu-id="9066f-112">Solo puede haber una nube activa en un momento dado.</span><span class="sxs-lookup"><span data-stu-id="9066f-112">Only one cloud can be active at any time.</span></span> <span data-ttu-id="9066f-113">Para obtener información más detallada acerca de una nube, incluidos los puntos de conexión que usa para los servicios de Azure, use el comando `cloud show`:</span><span class="sxs-lookup"><span data-stu-id="9066f-113">To get more detailed information on a cloud, including the endpoints that it uses for Azure services, use the `cloud show` command:</span></span>

```azurecli
az cloud show --name AzureChinaCloud --output json
```

```output
{
  "endpoints": {
    "activeDirectory": "https://login.chinacloudapi.cn",
    "activeDirectoryDataLakeResourceId": null,
    "activeDirectoryGraphResourceId": "https://graph.chinacloudapi.cn/",
    "activeDirectoryResourceId": "https://management.core.chinacloudapi.cn/",
    "batchResourceId": "https://batch.chinacloudapi.cn/",
    "gallery": "https://gallery.chinacloudapi.cn/",
    "management": "https://management.core.chinacloudapi.cn/",
    "resourceManager": "https://management.chinacloudapi.cn",
    "sqlManagement": "https://management.core.chinacloudapi.cn:8443/",
    "vmImageAliasDoc": "https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json"
  },
  "isActive": false,
  "name": "AzureChinaCloud",
  "profile": "latest",
  "suffixes": {
    "azureDatalakeAnalyticsCatalogAndJobEndpoint": null,
    "azureDatalakeStoreFileSystemEndpoint": null,
    "keyvaultDns": ".vault.azure.cn",
    "sqlServerHostname": ".database.chinacloudapi.cn",
    "storageEndpoint": "core.chinacloudapi.cn"
  }
}
```

## <a name="switching-the-active-cloud"></a><span data-ttu-id="9066f-114">Cambio de la nube activa</span><span class="sxs-lookup"><span data-stu-id="9066f-114">Switching the active cloud</span></span>

<span data-ttu-id="9066f-115">Para cambiar la nube actualmente activa, ejecute el comando [cloud set](/cli/azure/cloud#set).</span><span class="sxs-lookup"><span data-stu-id="9066f-115">To switch the currently active cloud, run the [cloud set](/cli/azure/cloud#set) command.</span></span> <span data-ttu-id="9066f-116">Este comando toma un argumento necesario, el nombre de la nube.</span><span class="sxs-lookup"><span data-stu-id="9066f-116">This command takes one required argument, the name of the cloud.</span></span>

```azurecli
az cloud set --name AzureChinaCloud
```

> [!IMPORTANT]
> <span data-ttu-id="9066f-117">Si la autenticación de la nube activada ha expirado, debe volver a autenticarse antes de realizar otras tareas con la CLI.</span><span class="sxs-lookup"><span data-stu-id="9066f-117">If your authentication for the activated cloud has expired, you need to re-authenticate before performing any other CLI tasks.</span></span> <span data-ttu-id="9066f-118">Si es la primera vez que cambia a la nueva nube, debe establecer la suscripción activa.</span><span class="sxs-lookup"><span data-stu-id="9066f-118">If this is your first time switching to the new cloud, you also need to set the active subscription.</span></span>
> <span data-ttu-id="9066f-119">Para obtener instrucciones sobre la autenticación, consulte [Inicio de sesión con la CLI de Azure 2.0](authenticate-azure-cli.md).</span><span class="sxs-lookup"><span data-stu-id="9066f-119">For instructions on authenticating, see [Log in with Azure CLI 2.0](authenticate-azure-cli.md).</span></span> <span data-ttu-id="9066f-120">Para más información sobre la administración de suscripciones, vea [Administración de suscripciones de Azure con la CLI de Azure 2.0](manage-azure-subscriptions-azure-cli.md)</span><span class="sxs-lookup"><span data-stu-id="9066f-120">For information on subscription management, see [Manage Azure subscriptions with Azure CLI 2.0](manage-azure-subscriptions-azure-cli.md)</span></span>

## <a name="register-a-cloud"></a><span data-ttu-id="9066f-121">Registro de una nube</span><span class="sxs-lookup"><span data-stu-id="9066f-121">Register a cloud</span></span>

<span data-ttu-id="9066f-122">Registre una nube nueva si tiene sus propios puntos de conexión para Azure Stack.</span><span class="sxs-lookup"><span data-stu-id="9066f-122">Register a new cloud if you have your own endpoints for Azure Stack.</span></span> <span data-ttu-id="9066f-123">Las nubes se crean con el comando [cloud register](/cli/azure/cloud#register).</span><span class="sxs-lookup"><span data-stu-id="9066f-123">Creating a cloud is done with the [cloud register](/cli/azure/cloud#register) command.</span></span> <span data-ttu-id="9066f-124">Este comando requiere un nombre y un conjunto de funcionalidades con puntos de conexión asociados.</span><span class="sxs-lookup"><span data-stu-id="9066f-124">This command requires a name and a set of capabilities with associated endpoints.</span></span> <span data-ttu-id="9066f-125">Para más información sobre cómo registrar una nube para su uso con Azure Stack, consulte [Instalación y configuración de la CLI para su uso con Azure Stack](/azure/azure-stack/user/azure-stack-connect-cli#connect-to-azure-stack).</span><span class="sxs-lookup"><span data-stu-id="9066f-125">To learn how to register a cloud for use with Azure Stack, see [Install and configure CLI for use with Azure Stack](/azure/azure-stack/user/azure-stack-connect-cli#connect-to-azure-stack).</span></span>

<span data-ttu-id="9066f-126">No es necesario registrar su propia nube para las regiones de China, Gobierno de EE. UU. o Alemania.</span><span class="sxs-lookup"><span data-stu-id="9066f-126">You do not need to register your own cloud for China, US Government, or German regions.</span></span> <span data-ttu-id="9066f-127">Microsoft las administra y están disponibles de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="9066f-127">These are managed by Microsoft and available by default.</span></span>  <span data-ttu-id="9066f-128">Para más información sobre la configuración de todos los puntos de conexión disponibles, consulte la [documentación de `az cloud register`](/cli/azure/cloud?view=azure-cli-latest#az_cloud_register).</span><span class="sxs-lookup"><span data-stu-id="9066f-128">For more information on all of the available endpoint settings, see the [documentation for `az cloud register`](/cli/azure/cloud?view=azure-cli-latest#az_cloud_register).</span></span>

<span data-ttu-id="9066f-129">Al registrar una nube no se cambia automáticamente a ella.</span><span class="sxs-lookup"><span data-stu-id="9066f-129">Registering a cloud does not automatically switch to it.</span></span> <span data-ttu-id="9066f-130">Use el comando `az cloud set` para seleccionar la nube recién creada, tal y como se describió anteriormente.</span><span class="sxs-lookup"><span data-stu-id="9066f-130">Use the `az cloud set` command to select the newly created cloud as described above.</span></span>

## <a name="update-an-existing-cloud"></a><span data-ttu-id="9066f-131">Actualización de una nube existente</span><span class="sxs-lookup"><span data-stu-id="9066f-131">Update an existing cloud</span></span>

<span data-ttu-id="9066f-132">Si tiene permisos, también puede actualizar una nube existente.</span><span class="sxs-lookup"><span data-stu-id="9066f-132">If you have permissions, you can also update an existing cloud.</span></span> <span data-ttu-id="9066f-133">Hágalo cuando necesita cambiar a un perfil de Azure diferente o para agregar o cambiar un punto de conexión.</span><span class="sxs-lookup"><span data-stu-id="9066f-133">Do this when you need to switch to a different Azure profile, add an endpoint, or change an endpoint.</span></span>
<span data-ttu-id="9066f-134">Para ello, se usa el comando `az cloud update`, que tiene los mismos argumentos que `az cloud register`.</span><span class="sxs-lookup"><span data-stu-id="9066f-134">You do this with the `az cloud update` command, which takes the same arguments as `az cloud register`.</span></span> <span data-ttu-id="9066f-135">Consulte [la documentación de `az cloud update`](/cli/azure/cloud?view=azure-cli-latest#az_cloud_update) para más información.</span><span class="sxs-lookup"><span data-stu-id="9066f-135">For more information, see the [documentation for `az cloud update`](/cli/azure/cloud?view=azure-cli-latest#az_cloud_update).</span></span>

## <a name="unregister-a-cloud"></a><span data-ttu-id="9066f-136">Anulación del registro de una nube</span><span class="sxs-lookup"><span data-stu-id="9066f-136">Unregister a cloud</span></span>

<span data-ttu-id="9066f-137">Si ya no necesita una nube registrada, puede anular el registro con el comando [cloud unregister](/cli/azure/cloud#unregister):</span><span class="sxs-lookup"><span data-stu-id="9066f-137">If you no longer require a registered cloud, it can be unregistered with the [cloud unregister](/cli/azure/cloud#unregister) command:</span></span>

```azurecli
az cloud unregister --name MyCloud
```