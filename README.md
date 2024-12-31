# pico-w-webserver-template

This example is built upon the pico-w-webserver-template file found on github.
I included it in this repo, so no extra git magic is required for this
sample. https://github.com/LearnEmbeddedSystems/pico-w-webserver-template

I am trying to build a pico-w IOT device. One of the biggest problems is
initializing a IOT system in a random wifi environment. So after other
projects here on github to init to my local wifi. I wish to find my many iot
devices by name, once they are initialized on my LAN. To do this I use MDNS
AKA avahi AKA bounjour. Even windows can use mdns - I think the easiest way
is to install an HP printer which does the windows magic to install mdns.  I
do assume the existence of a working wifi environment.

The first step was to do a generic, small footprint, target somewhat
independent (easily ported?), flash file system handler. See
https://github.com/nospamcalfee/ringbuffer

The second step is this project, do a bluetooth initialization project.
https://github.com/nospamcalfee/spp_in_out

## this project goal "MDNS" AKA avahi AKA bounjour

I want to have a stand alone directory containing my project and its
configuration, but using the sdk for the big packages. I started with a
standalone website project. Then I hacked around build errors until I got a
working mdns solution. Note changes to lwipopts.h and CMakeLists. This
project uses nothing from the pico-examples repo during its build.


## Build and Install

This build assumes you have the pico-sdk installed and have defined in your
environment PICO_SDK_PATH. This package works as a native Linux shell build,
but should be easy to people who wish to further complicate the build using
an ide environment. SSID and PASSWORD are case sensitive!

There is no requirement for any pico-examples source code (I hope)

```bash
cd yourprojectdirectory
mkdir build; cd build
cmake ..  -DWIFI_SSID="Your Network" -DWIFI_PASSWORD="Your Password" -DHOSTNAME="fred"
make
```

This example assumes you are sitting in the build subdirectory. The definition
of HOSTNAME becomes the name of the server, used like "fred.local" as a URL
in your favorite browser. This assumes your host OS such as linux, Mac, or
Windows (if mdns is installed) supports the .local dynamic dns name.

```bash
rm CMakeCache.txt ; cmake ..  -DWIFI_SSID="Your Network" -DWIFI_PASSWORD="Your Password" -DHOSTNAME="fred"; make clean; make -j 8
```

If you want to see the very verbose build command lines add "make VERBOSE"
instead of "make" above.

Once built, the `picow_mdns_webserver.uf2` file can be dragged and dropped onto your
Raspberry Pi Pico to install and run the example.
