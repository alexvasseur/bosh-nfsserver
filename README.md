# bosh-nfsserver
A NFS server bosh release for use with PCF Volume Service

## HowTo create the bosh release

To initialize the project
```
bosh2 init-release
bosh2 generate-job nfsserver

# we don't need blobs
bosh2  blobs
```

To build a release tgz
```
bosh2 create-release --force --timestamp-version --tarball nfsserver.tgz
```




