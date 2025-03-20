# SGX Prover

This application is an SGX enclave with Rust. It builds a Prover client inside the enclave using [Automata SGX SDK](https://github.com/automata-network/automata-sgx-sdk).

## Project Structure
<pre>
├── <a href="./app/">app</a>: The main application
│ ├── <a href="./app/sgx">sgx</a>: Configurations for the enclave
│ │ ├── <a href="./app/sgx/config.xml">config.xml</a>: Defined parameters of the enclave
│ │ ├── <a href="./app/sgx/enclave.edl">enclave.edl</a>: Enclave Definition Language file defining the enclave interface
│ │ ├── <a href="./app/sgx/enclave.lds">enclave.lds</a>: Linker script for the enclave
│ │ └── <a href="./app/sgx/private.pem">private.pem</a>: Key used to sign the enclave
│ ├── <a href="./app/src/main.rs">src/main.rs</a>: Main entrypoint for the application
│ └── <a href="./app/build.rs">build.rs</a>: Builder code used to build the application
├── <a href="./enclave/">enclave</a>: The SGX enclave implementation
│ └── <a href="./enclave/src/lib.rs">lib.rs</a>: Main library file for the enclave implementation
├── <a href="./mock-lib/">mock-lib</a>: A mock library which is called by the enclave via OCALL
│ └── <a href="./mock-lib/src/lib.rs">lib.rs</a>: Main library file for the mock library implementation
</pre>

## Development

1. `enclave/src/lib.rs` contains the business logic.
2. Update the `app/sgx/enclave.edl` file if you need to change the ECALL interface or add new ECALLs.
3. Refer to the usage `mock-lib` if you want to use libraries via OCALL. For example, you need to use a library that use instructions not allowed(such as CPUID or GETSEC) in enclave.

## Building the Enclave
### Prerequisites
In order to build the enclave, you need to have a sgx-supported machine.

If you have a machine with SGX support, please check the version of your SGX and DCAP SDK. The latest version supported by Automata SGX SDK can be found [here](https://github.com/automata-network/automata-sgx-sdk/tree/main).

If you don't have a machine with SGX support, we recommend you to create a [`DCsv3`](https://learn.microsoft.com/en-us/azure/virtual-machines/sizes/general-purpose/dcsv3-series?tabs=sizebasic) instance in Azure. Please refer to the [docker](./docker/) folder for the list of supported systems and create the instance using one of these systems. You can either install the SGX and DCAP SDK manually by following the steps outlined in the Dockerfile, or alternatively, you can use Docker to build and run the enclave directly.

### Build manually
> You need to have a sgx-supported machine with SGX and DCAP SDK installed to build the enclave manually.
#### Clone the repository
```bash
git clone https://github.com/denniswon/sgx-prover.git
cd sgx-prover
```

#### Install cargo-sgx
```bash
cargo install cargo-sgx
```

#### Generate new signing key

```bash
cargo sgx gen-key app/sgx/private.pem
```

#### Build the Enclave

```bash
cargo sgx build
```
or you can run the enclave directly
```bash
cargo sgx run
```
You can find the executable file in `./target/debug` or `./target/release` directory.

### Build with Docker

> You need to have a sgx-supported machine to build the enclave with docker. Make sure you got the docker and docker-compose installed.

Build image for ubuntu 20.04
```bash
$ cd docker/ubuntu-20.04
$ docker compose build
```

Build image for ubuntu 22.04
```bash
$ cd docker/ubuntu-22.04
$ docker compose build
```

#### Run with Docker

Run image for ubuntu 20.04
```bash
$ cd docker/ubuntu-20.04
$ docker compose run sgx-prover
```

Run image for ubuntu 22.04
```bash
$ cd docker/ubuntu-22.04
$ docker compose run sgx-prover
```
