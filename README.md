# tinyApache
A containerized HTTP server using Alpine and Apache.

## Prerequisites packages
```bash
* buildah
* podman
```

## Configure container storage (Optional)
```bash
su - root
vgcreate vgDATA /dev/vdb
lvcreate -n lvLIB -L 5G /dev/vdb
lvcreate -n lvRUN -L 5G /dev/vdb
mkfs -t xfs -n ftype=1 /dev/vgDATA/lvLIB
mkfs -t xfs -n ftype=1 /dev/vgDATA/lvRUN
mount /dev/vgDATA/lvLIB /var/lib/containers
mount /dev/vgDATA/lvRUN /var/run/containers
```

## Usage
### Open firewall port
```bash
firewall-cmd --add-service=http --permanent
firewall-cmd --reload
```

### Clone git repository
```bash
git clone https:github.com/kevydotvinu/tinyApache
cd tinyApache
```

### Build container image
```bash
buildah bud --security-opt label=disable --tag localhost/kevydotvinu/tinyapache:v1 .
```

### Run container image
```bash
cat << EOF > htdocs/index.html
I am Alpine + Apache!
EOF

podman run --rm \
           --interactive \
           --tty \
	   --privileged \
           --net host \
           --volume "$(pwd)/htdocs:/var/www/localhost/htdocs" \
           --security-opt label=disable \
           --name=tinyapache localhost/kevydotvinu/tinyapache:v1
```
To run the pod in background, replace --rm, --interactive and --tty with --detach.

## Boot from iPXE boot script
Press Ctrl+b to get the iPXE prompt and type in the following commands:
```bash
iPXE> dhcp
iPXE> chain http://192.168.X.X:8080/boot.ipxe
```
