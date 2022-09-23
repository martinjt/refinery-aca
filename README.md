## Azure Container App for Honeycomb Refinery

This repository provides an easy way to get an single instance Refinery cluster running on Azure Container Apps.

It uses the standard `honeycombio/refinery` image, and is currently locked to the 1.36 version

In addition, this repository uses the Azure Storage Account FileShare functionality to host the config and rules files, and therefore having that config file persist between restarts, and meaning that a custom image and ACR aren't required.

Finally, the process uses Azure Container Apps secrets for the Honeycomb API key, so you can safely store it!

This is all done in a new Resource Group, so you can cleanup the resources at the end pretty easily.

## usage

Make sure you're logged into azure and have a subscription selected that you have permission use Azure Container Apps on.

You'll also need the preview extension for Azure Container Apps and the Azure CLI

```bash
az extension add -n containerapp --upgrade
az provider register --namespace Microsoft.App
az provider register --namespace Microsoft.OperationalInsights
```

```bash
chmod +x make_collector_app.sh
./make_collector_app.sh <honeycomb api key>
```

## Testing

***Note** this section doesn't include the Honeycomb API key header required, therefore need update*

Using the `otel-cli` application (Install [here](https://github.com/equinix-labs/otel-cli)) you can hit the new API endpoint.

```bash
export OTEL_EXPORTER_OTLP_ENDPOINT=<endpoint from Container app>/v1/traces
```

e.g.

```bash
export OTEL_EXPORTER_OTLP_ENDPOINT=https://refinery.blueislan-3d4basfgfe9.uksouth.azurecontainerapps.io/v1/traces
```

***Note:** The CLI assumes that any URI provided is absolute, and therfore `v1/traces` is required *

You can then push span like this

```bash
otel-cli span --service "CLI" \
  --name "Refinery In Azure Container Apps" \
  --start $(date +%s.%N) \
  --end $(date +%s.%N) \
  --verbose
```

