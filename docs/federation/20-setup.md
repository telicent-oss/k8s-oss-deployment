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
-rw-r--r--@ 1 staff  staff   3.5K 18 Sep 14:56 README.md
lrwxr-xr-x@ 1 staff  staff    22B 18 Sep 14:04 telicent -> k8s-manifests/telicent
drwxr-xr-x@ 8 staff  staff   256B 18 Sep 14:08 workload
```

<<<<<<<<<<<TODO>>>>>>>>>>>>>>>>>

client/server

The `workload/core` directory contains overlays for various elements of the Telicent
deployment. It also contains shared components to reduce boilerplate and
repetition.

```
tree -L 1 workload/core
workload/core
├── 1-namespaces
├── 2-access
├── 3-smart-cache-graph
├── 4-query
└── components
```
