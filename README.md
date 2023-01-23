# perfana-helm-chart

## Usage

[Helm](https://helm.sh) must be installed to use the charts.
Please refer to Helm's [documentation](https://helm.sh/docs/) to get started.

Once Helm is set up properly, add the repo as follows:

```console
helm repo add perfana https://perfana.github.io/perfana-helm-chart
```

You can then run `helm search repo perfana` to see the charts.

## Secrets

The chart expects secrets to be mounted as files in the pods. If `existingSecret` is set in the values file, the chart will mount these files from the provided secret.
The secret should have the following keys / content:

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: secrets
  namespace: perfana
type: Opaque
stringData:
  
  perfana-fe-admin-creds-secret.yaml: |
    adminUser: admin@perfana.io
    adminPassword: ${ADMIN_PASSWORD}

  perfana-api-creds-secret.yaml: |
    perfanaApiUser: perfana-api-user
    perfanaApiPassword: ${API_PASSWORD}

  perfana-fe-grafana-config-secret.yaml: |
    grafanaInstances:
    - label: "Demo"
      clientUrl: "https://grafana.demo.perfana.cloud"
      serverUrl: "http://grafana"
      orgId: "1"
      username: admin@perfana.io
      password: ${ADMIN_PASSWORD}
      snapshotInstance: true
      trendsInstance: true

  perfana-fe-authentication-config-secret.yaml: |
    authenticationServices:
      oidc:
        loginStyle: "popup"
        clientId: "perfana"
        secret: "${SECRET}"
        serverUrl: ""
        authorizationEndpoint: "https://oidc.my.host/auth?"
        tokenEndpoint: "https://oidc.my.host/token"
        idTokenWhitelistFields:
        - "groups"
        requestPermissions:
        - "openid"
        - "profile"
        - "email"
        - "groups"

  perfana-mongo-urls-secret.env: |
    MONGO_URL=mongodb://${MONGO_USER}:${MONGO_PASSWORD}@mongodb-headless:27017/perfana?authSource=perfana&replicaSet=rs0
    MONGO_OPLOG_URL=mongodb://${MONGO_USER}:${MONGO_PASSWORD}r@mongodb-headless:27017/local?authSource=admin&replicaSet=rs0

  perfana-grafana-mysql-secret.env: |
    MYSQL_HOST=mysql-headless
    MYSQL_USER=grafana
    MYSQL_PASSWORD=${MYSQL_PASSWORD}

  perfana-grafana-postgres-secret.env: |
    PG_HOST=postgres-headless
    PG_USER=grafana
    PG_PASSWORD=${PG_PASSWORD}

  perfana-check-mongodb.properties: |
    spring.data.mongodb.uri=mongodb://${MONGO_USER}:${MONGO_PASSWORD}@mongodb-headless.perfana:27017/perfana?replicaSet=rs0
```

> **Important**: make sure to end all YAML files wit a newline! 

If instead the files are mounted in the pod in some other way, the files should be mounted on `/secrets`