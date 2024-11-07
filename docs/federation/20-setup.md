# Set up

## Gather relevant configuration values:

| Value | Notes |
|-|-|
| Hostname | The domain the federation server |
| Client password |  |
| Client password salt | |
| Hashed client password | |

## Generating a client password and hash

The client is identified by a password. To store the password securely on the
server it requires to hashed with a client specific salt.

Note. This is an example. Your local security policy may dictate how to securely
generate passwords and salts.

```
export CLIENT_PASSWORD=$(openssl rand -base64 30)
export CLIENT_SALT=$(openssl rand -base64 15)
export CLIENT_HASH=$(printf $CLIENT_PASSWORD$CLIENT_SALT | openssl dgst -sha3-256 | cut -d' ' -f2)
echo "hash: $CLIENT_HASH"
echo "salt: $CLIENT_SALT"
echo "password: $CLIENT_PASSWORD"
```

## Directory structure

The example deployment uses relative paths to the Telicent manifests. The
manifests are distributed as a tarball. Untar the manifests in a different
location and symlink to the `telicent` directory of the Kubernetes manifests.
E.G:

```
$ ll -l
total 48
-rw-r--r--@ 1 staff  staff   809B 26 Sep 15:17 README.md
drwxr-xr-x@ 5 staff  staff   160B 26 Sep 15:13 docs
lrwxr-xr-x@ 1 staff  staff    44B 26 Sep 15:30 telicent -> ../telicent-k8s-manifests-oss-v0.0.0/telicent
drwxr-xr-x@ 3 staff  staff    96B 26 Sep 15:13 utils
drwxr-xr-x@ 4 staff  staff   128B 26 Sep 15:13 workload
```

client/server

The `workload/federation` directory contains overlays for various elements of
the Telicent federation deployment.

```
workload/federation
├── 1-server
│   ├── kustomization.yaml
│   └── patches
├── 2-client
│   ├── kustomization.yaml
│   └── patches
├── 3-outgoing-filter
│   ├── kustomization.yaml
│   └── patches
└── 4-incoming-filter
    ├── kustomization.yaml
    └── patches
```
