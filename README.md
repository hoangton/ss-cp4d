# SingleStore DB Helm Chart for IBM Cloud Pak for Data

This Helm chart is to be used for deploying SingleStore DB in the IBM Cloud Pak for Data environment.

## Documentation

Please visit [SingleStore DB Helm Chart for IBM Cloud Pak for Data](https://svtech.azurecr.io/ss-cp4d).

## Sample values-override.yaml file
Below is a sample values-override.yaml file that shows how to override 
configurable parameters of this Helm Chart.

```yaml
# fsGroup is the only parameter that you have to set. Set it to the starting value of 
# the namespace's openshift.io/sa.scc.supplemental-groups annotation
fsGroup: "100065000"

state: "enabled"

# Ideally, choose a StorageClass that supports volume expension and WaitForFirstConsumer
# binding mode and that uses a block level storage.
storageClassName: ibm-spectrum-scale-sc

operatorImage: singlestore/operator:3.0.60-c818b3a1

# nodeImageVersion should be consistent with nodeImageTag
nodeImageVersion: 7.8.19
nodeImageTag: alma-7.8.19-4263b2d130
nodeImageRepo: singlestore/node

# Make sure to use string for the following three values
coresPerUnit: "8"
memoryPerUnit" "32"
overpackFactor: "0"

memsqlOperatorDeployment:
  serviceAccountName: memsql-operator
  name: memsql-operator
  replicas: 1
  additionalArgs:
  - "--priority-class-name"
  - "my-priority-class"

memsqlCluster:
  name: memsql-cluster

  # Must include either license or licenseSecret, but not both.
  # If both are specified, license will take precedence.
  license: BDAwMDA...Hbi/AA==
  licenseSecret:
    # The name of the secret that contains the license
    name: license-secret
    # The key name in the secret whose value is contains the license
    key: license

  # Must include either adminHashedPassword or adminHashedPasswordSecret, but not both.
  # If both are specified, adminHashedPassword will take precedence.

  # How to generate hassed password in python 2.x
  # from hashlib import sha1
  # print("*" + sha1(sha1('Welcome#SVT1').digest()).hexdigest().upper())
  # *815F3ECCBDD29521D1EBCA955D2F09B41EE12F47
  adminHashedPassword: "*815F3ECCBDD29521D1EBCA955D2F09B41EE12F47"

  adminHashedPasswordSecret:
    # The name of the secret that contains the hashed admin password
    name: admin-password
    # The key name in the secret whose value is contains the hashed admin password
    key: admin-password-key

  redundancyLevel: 2

  aggregatorSpec:
    count: 2
    height: 0.5
    storageGB: 10

  leafSpec:
    count: 2
    height: 0.5
    storageGB: 20

  # See https://docs.singlestore.com/v7.3/reference/memsql-operator-reference/memsql-operator-reference-overview/
  # For additional configurations, such as serviceSpec, backupSpec, etc.
```

