# Helm Tutorial

[Helm Get Started](https://helm.sh/docs/chart_template_guide/getting_started/)  


### Create a new chart (it's already done)
```shell
helm create mychart
```

### Remove the default templates (it's already done)
```shell
rm -rf mychart/templates/*
```

```shell
helm install full-coral ../mychart
```

```shell
helm get manifest full-coral
```

### Install helm chart with a new name
```shell
helm install clunky-serval ./mychart
```

```shell
helm get manifest full-coral
```

### Dry run
```shell
helm install --debug --dry-run goodly-guppy ./mychart
```

### Dry run for DEV

```shell
helm install --debug --dry-run goodly-guppy ./mychart --namespace dev  --values ./mychart/values-dev.yaml
```