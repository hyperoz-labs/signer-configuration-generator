# signer-configuration-generator

Utility to generate a large number of Web3Signer configuration files with random keys.
 - Web3Signer encrypted, raw files and hashicorp loading (BLS Keys).

## build application:
~~~
./gradlew clean build installdist
~~~

## cd to install distribution
~~~
cd ./build/install/signer-configuration-generator/bin
~~~

## run application

### Web3Signer Raw configuration files generation
~~~
./signer-configuration-generator raw --count=10000
~~~

### Web3Signer Hashicorp configuration files generation
- Note: Run Hashicorp vault in dev mode (via Docker)
~~~
docker pull vault
docker run --rm --cap-add=IPC_LOCK -e 'VAULT_DEV_ROOT_TOKEN_ID=myroot' -p 8200:8200 --name=dev-vault vault
~~~
~~~
./signer-configuration-generator hashicorp --count=10000 --token=myroot
~~~

## run via Docker image

Images are published to GHCR on every merge to `main` (`:latest`, `:main`, `:sha-<short-sha>`) and on version tags (`:X.Y.Z`, `:X.Y`, `:X`):
~~~
docker pull ghcr.io/hyperoz-labs/signer-configuration-generator:latest
~~~

The entrypoint is the CLI binary itself, so pass subcommands/flags directly. `--output` defaults to `./keys` relative to the container's working directory (`/app`), so mount a volume to get the generated files back on the host:

### Raw configuration files generation
~~~
docker run --rm -v "$(pwd)/keys:/app/keys" \
  ghcr.io/hyperoz-labs/signer-configuration-generator:latest \
  raw --count=10000
~~~

### Hashicorp configuration files generation
- Note: Run Hashicorp vault in dev mode (via Docker), then run the generator on the same network so it can reach vault at `--url`:
~~~
docker pull vault
docker run --rm --cap-add=IPC_LOCK -e 'VAULT_DEV_ROOT_TOKEN_ID=myroot' -p 8200:8200 --name=dev-vault vault
~~~
~~~
docker run --rm --network host -v "$(pwd)/keys:/app/keys" \
  ghcr.io/hyperoz-labs/signer-configuration-generator:latest \
  hashicorp --count=10000 --token-file=/dev/stdin <<< "myroot"
~~~

