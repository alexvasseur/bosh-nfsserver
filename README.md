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

You will note that the user vcap, group vcap exists in BOSH deployed VM with uid 1000 and gid 1000.
The persistent volume /export/vol1 (for example - see the jobs/ folder to change) is rw for vcap.
Note that this is different uid/gid from the Diego vcap container user which is uid 2000 gid 2000.
The nfsserver self-mount into /export/vol1 as test (for example - see the jobs/ folder to change)

## Sample app

You can explore pora but this application is also a good demo.
https://github.com/azwickey-pivotal/volume-demo

```
# After it is enabled in ERT with also the NFS service broker deployed thru the errand
# with configuration in OpsManager

# as cf admin
cf enable-service-access nfs

# as user
wget https://github.com/azwickey-pivotal/volume-demo/releases/download/4.0.1/volume-demo-4.0.1.jar

# as a user I must know the address and info for my persistent volume NFS server and path
cf cs volume-service -c '{"share": "10.0.19.17/export/vol1"}'

cf push -p volume-demo-4.0.1.jar -m 750m volume-demo --no-start

# We use the NFS uid/gid here
cf bs volume-demo volume-service -c '{"uid":"1000","gid":"1000"}'

cf start volume-demo

cf ssh volume-demo
   df -h
   ...
   (or use the application http route and access its page)


```


