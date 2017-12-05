# minio-manta

Docker+ContainerPilot service for running Minio Manta Gateway.

This currently requires a custom Minio [build package](https://us-east.manta.joyent.com/justin.reagor/public/minio/minio/releases/minio-manta-af39390-linux.tar.gz) hosted off [Justin Reagor](https://github.com/cheapRoc)'s Manta account. This will change once [our PR](https://github.com/minio/minio/pull/5025) has been merged.

## Build

### compose

```sh
$ make build
$ make tag
$ cd examples/compose
$ vim _env
$ docker-compose up -d --scale consul=3
```

### triton

```sh
$ cd examples/triton
$ vim _env
$ vim _consul_env
$ docker-compose up -d --scale consul=3
```

Follow the instructions on how to [setup the `mc` client](https://github.com/minio/mc#add-a-cloud-storage-service) using your Manta credentials.

## Environment

You need to pass in the following env vars, which include a private key :(

- `MINIO_ACCESS_KEY` is your `MANTA_USER` and passed to Minio.
- `MINIO_SECRET_KEY` is your `MANTA_KEY_ID` and passed to Minio.
- `MINIO_KEY_MATERIAL` is the private key attached to your Triton account that you use to connect Minio to Manta. This is used to generate a key file in the file system.
- `MANTA_KEY_MATERIAL` is the location inside the container where the Manta key will be stored. This is passed directly to Minio. You can use `/etc/minio/manta_key`.

These values can be placed inside `examples/compose/_env` and `examples/triton/_env` to be used by both examples setups.

Key material is expected to be input as `cat $KEY_FILE | tr '\n' '#' > key_file` and output in reverse.

When launching onto Triton you'll want to configure a second env file under `examples/triton`. Create `examples/triton/_consul_env` and include a similar value to connect your Consul cluster using CNS.

```sh
CONSUL=consul.svc.${TRITON_USER_UUID}.${TRITON_REGION}.cns.joyent.com
```

The example Triton configuration uses a prebuilt container image as well since I was unable to build on Triton itself.

## Future

- ~~Triton example setup support.~~
- Currently, sub user support is not known. Product-eng needs to test and make sure sub users are accepted properly by `triton-go` in terms of authentication and HTTP request signing.
- Use proper secrets management ala Vault.



