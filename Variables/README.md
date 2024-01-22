1. Define Variables:

```
variables:
  STAGING_DOMAIN: carniverouse-staging.surge.sh
  PRODUCTION_DOMAIN: carniverouse-vegintable.surge.sh
```

2. Replace string with variable name:

```
environment:
    name: staging
    url: http://$STAGING_DOMAIN


environment:
    name: production
    url: http://$PRODUCTION_DOMAIN

```