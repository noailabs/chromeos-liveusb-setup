# crome os live usb setup
```bash
wget --inet4-only https://gitlab.com/api/v4/projects/26210301/packages/generic/ncurses/6.3-20211106_x86_64/ncurses-6.3-20211106-chromeos-x86_64.tpxz
wget --inet4-only https://gitlab.com/api/v4/projects/26210301/packages/generic/parted/3.4_x86_64/parted-3.4-chromeos-x86_64.tar.xz
tar xvf ncurses-6.3-20211106-chromeos-x86_64.tpxz 
tar xvf parted-3.4-chromeos-x86_64.tar.xz 
export LD_LIBRARY_PATH=/usr/local/opt/usr/local/lib64:$LD_LIBRARY_PATH
cd usr/local/sbin

sudo ./parted
print free
resizepart 1
quit

resize2fs /dev/sda1

```
```bash
apt-get install aria2

youtube-dl --external-downloader=aria2c --external-downloader-args '--min-split-size=1M --max-connection-per-server=16 --max-concurrent-downloads=16 --split=16' $URL
https://www.reddit.com/r/youtubedl/comments/qf2uqv/fix_for_60kbs_throttled_downloads_2021oct/
```
```bash
NODE_OPTIONS=--dns-result-order=ipv4first npm ping
```
```bash
sudo nano /etc/sysctl.conf

net.ipv6.conf.all.disable_ipv6 = 1
#or for enp0s3 only net.ipv6.conf.enp0s3.disable_ipv6 = 1

sudo sysctl -p

gsettings set org.gnome.desktop.interface scaling-factor 1.5
gsettings set org.gnome.desktop.interface text-scaling-factor 1.75
```

```bash
cd /mnt
find . -name *.cfg -exec sudo sed -i 's,\(cros_legacy\|cros_efi\),\1 cros_debug,g' {} \;
```
```js
var CacheStorageSaved={}
function collectCaches(){
	window.caches.keys().then(function(cacheNames) {
	 for(var cacheName of cacheNames){
	 console.log(cacheName);	 
      window.caches.open(cacheName).then(function(cache) {
        return cache.keys();
      }).then(function(requests) {
		  if(!CacheStorageSaved[cacheName])CacheStorageSaved[cacheName]=[]
		  for(var request of requests){
		  	//console.log(request)
			CacheStorageSaved[cacheName].push(request.url)
		  }
      });
    }	 
  });
}


function saveDataToFile(data, filename) {
  const blob = new Blob([data], { type: 'text/plain' });
  const anchorElement = document.createElement('a');
  anchorElement.href = URL.createObjectURL(blob);
  anchorElement.download = filename;
  anchorElement.click();
}


//saveDataToFile(JSON.stringify(localStorage),"ls.json");
//saveDataToFile(JSON.stringify(CacheStorageSaved),"cache.json");

const fileInput = document.createElement('input');
fileInput.type = 'file';
fileInput.onchange = function (event) {
  const file = event.target.files[0];
  const reader = new FileReader();
  reader.onload = function (event) {
    const data = event.target.result;
    console.log('Loaded data:', data);
	 const ls=JSON.parse(data);
	 localStorage.clear();
	 for(var i in ls){
	 localStorage.setItem(i,ls[i]);
	 }
  };

  reader.readAsText(file);
};

document.body.appendChild(fileInput);
//fileInput.click();

// Create a button to trigger the file picker
var button = document.createElement('button');
button.textContent = 'Select File';

// Add a click event listener to the button
button.addEventListener('click', function() {
    fileInput.click(); // Trigger the file picker
});

// Append the file input and button to the body of the current page
document.body.appendChild(button);


const fileInput2 = document.createElement('input');
fileInput2.type = 'file';
fileInput2.onchange = function (event) {
  const file = event.target.files[0];
  const reader = new FileReader();
  reader.onload = function (event) {
    const data = event.target.result;
    //console.log('Loaded data:', data);
	 const savedcache=JSON.parse(data);
	 
	 for(let cacheName in savedcache){
      window.caches.open(cacheName).then(function(cache) {
		  cache.addAll(savedcache[cacheName])
		})
	 }
  };
  reader.readAsText(file);
};
//fileInput2.click();



```

```bash
pandoc input.txt -o output.pdf
apt-get install libreoffice
soffice --convert-to pdf /file.txt
apt-get install cups
cupsfilter /file.txt > /file.pdf
```

```bash
wget https://dl.bintray.com/chromebrew/chromebrew/parted-3.2-chromeos-x86_64.tar.xz
wget https://dl.bintray.com/chromebrew/chromebrew/ncurses-6.2-chromeos-x86_64.tar.xz
tar xvf ncurses-6.2-chromeos-x86_64.tar.xz
tar xvf parted-3.2-chromeos-x86_64.tar.xz
export LD_LIBRARY_PATH=/usr/local/opt/usr/local/lib64:$LD_LIBRARY_PATH
cd usr/local/sbin

sudo ./parted
print free
resizepart 16
quit

resize2fs /dev/sda16 
```
### docker setup
```bash
sudo start docker
sleep 2
sudo docker run --rm alpine echo "hello"
sudo bash -c "echo 0-3>/sys/fs/cgroup/cpuset/docker/cpuset.cpus"
sudo docker run --rm alpine echo "hello"

# docker-compose setup https://github.com/docker/compose/releases
sudo curl -L https://github.com/docker/compose/releases/download/1.25.4/docker-compose-`uname -s`-`uname -m` -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose
# sudo TMPDIR=/usr/local/opt/tmp docker-compose up
```
### chroot setup
```bash
mkdir bin  dev  etc  home  lib  lib64  opt  proc  root  run  sbin  sys  tmp  usr  usrlocal  var

mount --rbind /dev ./dev
mount -t proc /proc ./proc -o nosuid,nodev,noexec
mount --rbind /sys ./sys -o nosuid,nodev,noexec,ro
mount --rbind /dev/pts ./dev/pts -o nosuid,noexec
mount --rbind /run ./run

mount --rbind /bin ./bin
mount --rbind /sbin ./sbin
mount --rbind /lib ./lib
mount --rbind /lib64 ./lib64
mount --rbind /usr ./usr
mount --rbind /root ./root
mount --rbind /home ./home
mount --rbind /etc ./etc

mount --rbind ./usrlocal ./usr/local/
mount --rbind /tmp ./tmp


umount ./dev
umount ./sys
umount ./dev/pts
umount ./run
umount ./proc

umount ./bin
umount ./sbin
umount ./lib
umount ./lib64
umount ./usr/local/
umount ./usr
umount ./root
umount ./home
umount ./etc
umount ./tmp
```
#crouton
```bash
#run crouton till fail
#cp /usr/local/bin/enter-chroot ~/Downloads/
#comment all rdm
#cp ~/Downloads/enter-chroot /usr/local/bin/enter-chroot
# run enter-chroot
#
# Allow X server running as normal user to set/drop DRM master
#drm_relax_file="/sys/kernel/debug/dri/drm_master_relax"
#if [ -f "$drm_relax_file" ]; then
#    echo 'Y' > "$drm_relax_file"
#fi

# Modify chroot's /sys/class/drm and /dev/dri to avoid vgem/mfgsys
#varrundrm="$(fixabslinks '/var/run/drm')"
#varrundri="$(fixabslinks '/var/run/dri')"
#sysclassdrm="$(fixabslinks '/sys/class/drm')"
#devdri="$(fixabslinks '/dev/dri')"
#if [ ! -d "$varrundrm" -a -d "$sysclassdrm" -a -d "$devdri" ]; then
#    cp -Ta "$sysclassdrm" "$varrundrm"
#    cp -Ta "$devdri" "$varrundri"
#    for f in "$varrundrm"/*; do
#        if [ -h "$f" ] && readlink -- "$f" | grep -qF -e /vgem/ -e mfgsys; then
#            rm -f "$f" "$varrundri/${f##*/}"
#        fi
#    done
#    # Scanning of /dev/dri is done sequentially, so make sure there's a card0
#    for f in "$varrundri/card"*; do
#        [ -e "$varrundri/card0" ] || mv -f "$f" "$varrundri/card0"
#    done
#    mount --bind "$varrundrm" "$sysclassdrm"
#    mount --bind "$varrundri" "$devdri"
#fi


```
# flet
```bash
# https://github.com/flet-dev/flet/issues/2823
In Debian 12 - Debian GNU/Linux 12 (bookworm)
Being root:
apt install libmpv-dev mpv 
dpkg -L libmpv-dev
the output is:

/usr
/usr/include
/usr/include/mpv
/usr/include/mpv/client.h
/usr/include/mpv/render.h
/usr/include/mpv/render_gl.h
/usr/include/mpv/stream_cb.h
/usr/lib
/usr/lib/x86_64-linux-gnu
/usr/lib/x86_64-linux-gnu/pkgconfig
/usr/lib/x86_64-linux-gnu/pkgconfig/mpv.pc
/usr/share
/usr/share/doc
/usr/share/doc/libmpv-dev
/usr/share/doc/libmpv-dev/changelog.Debian.gz
/usr/share/doc/libmpv-dev/changelog.gz
/usr/share/doc/libmpv-dev/copyright
/usr/lib/x86_64-linux-gnu/libmpv.so

Then I've applied the symbol:
ln -s /usr/lib/x86_64-linux-gnu/libmpv.so  /usr/lib/libmpv.so.1 

and it worked for me.
```

```bash
# https://github.com/ptorre/crostini-lxc-cloud-containers
# https://qmacro.org/blog/posts/2024/08/24/new-source-for-lxd-images-on-crostini/
lxc remote set-url images https://images.lxd.canonical.com/
```
