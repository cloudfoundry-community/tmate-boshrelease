# Bionic Support

Bumped the following packages for Bionic support:

 - libssh 0.9.6
 - libevent 2.1.12
 - msgpack 3.3.0

Modified the webcfg scripting for the use of `nc -l`.  In xenial and older stemcells the output sends a shutdown after the configuration is emitted.  `nc -lN` is needed to support the same behavior on bionic and newer stemcells.