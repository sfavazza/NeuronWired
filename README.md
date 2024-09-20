# Dygma Defy Wired Neuron Firmware #

The default firmware for the Wired Dygma Defy.

# Build #

The whole setup could be checked in the docker configuration file, which allow to create an isolated environment
for the firmware development.

Be sure to populate all git sub-module (via `git submodule update`).

## doker workflow ##

1. start the containerized docker environment with: `docker compose up -d`
1. open a shell in it via: `docker exec -ti <top-folder-name>-defy-dev-1 bash`
1. build the design using `PlatformIO`: `pio run`

## host flow ##

1. prepare the development environment as performed in the `docker/Dockerfile` configuration file.

# Deploy #

The built image is stored at `.pio/build/rpipico/firmware.uf2`. It can be manually deployed to the target
following the [guide mentioned in this
issue](https://github.com/Dygmalab/Bazecor/issues/643#issuecomment-1884763670).
