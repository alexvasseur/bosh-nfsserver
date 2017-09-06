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

## HowTo deploy
```
bosh2 -e pcf upload-release
bosh2 -e pcf releases | grep nfs
```

customize local.yml and deploy

```
bosh2 -e pcf interpolate -l local.yml manifest.yml > manifest-real.yml
bosh2 -e pcf deploy manifest-real.yml -d alex-nfsserver
```

explore with

```
bosh2 -e pcf ssh -d alex-nfsserver
sudo su -
monit summary
df -h
cd /var/vcap/store/nfsserver/
```

use with a volume service app and pora




