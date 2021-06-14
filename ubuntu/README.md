# Introduction

This guide is intended for Ubuntu only (tested on Ubuntu 18.04.5 LTS).

# Table of Contents
**[Install Dependencies](#install-dependencies)**<br>
**[Install tpm2-tss](#install-tpm2-tss)**<br>
**[Install tpm2-tools](#install-tpm2-tools)**<br>
**[Setup Microsoft TPM 2.0 Simulator](#setup-microsoft-tpm-20-simulator)**<br>
**[Behaviour of Microsoft Simulator](#behaviour-of-microsoft-simulator)**<br>
**[Install TPM OpenSSL Engine](#install-tpm-openssl-engine)**<br>
**[Exercise 1](#exercise-1)**<br>
**[Assessment](#assessment)**<br>

# Install Dependencies

Update the package list.
```
$ sudo apt -y update
```

Dependencies of tpm2-tss.
```
$ sudo apt -y install \
  autoconf-archive \
  libcmocka0 \
  libcmocka-dev \
  procps \
  iproute2 \
  build-essential \
  git \
  pkg-config \
  gcc \
  libtool \
  automake \
  libssl-dev \
  uthash-dev \
  autoconf \
  doxygen \
  libjson-c-dev \
  libini-config-dev \
  libcurl4-openssl-dev
```

Additional dependencies of tpm2-tools.
```
$ sudo apt -y install \
  libcurl4-gnutls-dev \
  python-yaml \
  uuid-dev \
  pandoc
```

# Install tpm2-tss

Download tpm2-tss.
```
$ git clone https://github.com/tpm2-software/tpm2-tss ~/tpm2-tss
$ cd ~/tpm2-tss
$ git checkout 3.0.4
```

Build tpm2-tss.
```
$ ./bootstrap
$ ./configure
$ make -j$(nproc)
```

Install tpm2-tss.
```
$ sudo make install
$ sudo ldconfig
```

Check installation.
```
$ ls /usr/local/lib/
```

# Install tpm2-tools

Download tpm2-tools.
```
$ git clone https://github.com/tpm2-software/tpm2-tools ~/tpm2-tools
$ cd ~/tpm2-tools
$ git checkout 5.1
```

Build tpm2-tools.
```
$ ./bootstrap
$ ./configure
$ make -j$(nproc)
```

Install tpm2-tools.
```
$ sudo make install
$ sudo ldconfig
```

Check installation.
```
$ ls /usr/local/bin/
```

Execute any `tpm2_` command without a TPM connected.
```
$ tpm2_getrandom --hex 1
ERROR:tcti:src/tss2-tcti/tcti-device.c:442:Tss2_Tcti_Device_Init() Failed to open specified TCTI device file /dev/tpmrm0: No such file or directory 
WARNING:tcti:src/tss2-tcti/tctildr.c:79:tcti_from_init() TCTI init for function 0x7f11e5712010 failed with a000a 
WARNING:tcti:src/tss2-tcti/tctildr.c:109:tcti_from_info() Could not initialize TCTI named: tcti-device 
ERROR:tcti:src/tss2-tcti/tctildr-dl.c:154:tcti_from_file() Could not initialize TCTI file: libtss2-tcti-device.so.0 
ERROR:tcti:src/tss2-tcti/tcti-device.c:442:Tss2_Tcti_Device_Init() Failed to open specified TCTI device file /dev/tpm0: No such file or directory 
WARNING:tcti:src/tss2-tcti/tctildr.c:79:tcti_from_init() TCTI init for function 0x7f11e5712010 failed with a000a 
WARNING:tcti:src/tss2-tcti/tctildr.c:109:tcti_from_info() Could not initialize TCTI named: tcti-device 
ERROR:tcti:src/tss2-tcti/tctildr-dl.c:154:tcti_from_file() Could not initialize TCTI file: libtss2-tcti-device.so.0 
WARNING:tcti:src/util/io.c:253:socket_connect() Failed to connect to host 127.0.0.1, port 2321: errno 111: Connection refused 
ERROR:tcti:src/tss2-tcti/tcti-swtpm.c:591:Tss2_Tcti_Swtpm_Init() Cannot connect to swtpm TPM socket 
WARNING:tcti:src/tss2-tcti/tctildr.c:79:tcti_from_init() TCTI init for function 0x7f11e5712440 failed with a000a 
WARNING:tcti:src/tss2-tcti/tctildr.c:109:tcti_from_info() Could not initialize TCTI named: tcti-swtpm 
ERROR:tcti:src/tss2-tcti/tctildr-dl.c:154:tcti_from_file() Could not initialize TCTI file: libtss2-tcti-swtpm.so.0 
WARNING:tcti:src/util/io.c:253:socket_connect() Failed to connect to host 127.0.0.1, port 2321: errno 111: Connection refused 
WARNING:tcti:src/tss2-tcti/tctildr.c:79:tcti_from_init() TCTI init for function 0x7f11e5712320 failed with a000a 
WARNING:tcti:src/tss2-tcti/tctildr.c:109:tcti_from_info() Could not initialize TCTI named: tcti-socket 
ERROR:tcti:src/tss2-tcti/tctildr-dl.c:154:tcti_from_file() Could not initialize TCTI file: libtss2-tcti-mssim.so.0 
ERROR:tcti:src/tss2-tcti/tctildr-dl.c:254:tctildr_get_default() No standard TCTI could be loaded 
ERROR:tcti:src/tss2-tcti/tctildr.c:416:Tss2_TctiLdr_Initialize_Ex() Failed to instantiate TCTI 
ERROR: Could not load tcti, got: "(null)"
```

# Setup Microsoft TPM 2.0 Simulator

Download ms-tpm-20-ref.
```
$ git clone https://github.com/microsoft/ms-tpm-20-ref ~/ms-tpm-20-ref
```

Build ms-tpm-20-ref.
```
$ cd ~/ms-tpm-20-ref/TPMCmd
$ ./bootstrap
$ ./configure
$ make -j$(nproc)
```

Install ms-tpm-20-ref.
```
$ sudo make install
```

Run the simulator.
```
$ cd ~
$ tpm2-simulator
LIBRARY_COMPATIBILITY_CHECK is ON
Manufacturing NV state...
Size of OBJECT = 1204
Size of components in TPMT_SENSITIVE = 744
    TPMI_ALG_PUBLIC                 2
    TPM2B_AUTH                      50
    TPM2B_DIGEST                    50
    TPMU_SENSITIVE_COMPOSITE        642
MAX_CONTEXT_SIZE can be reduced to 1264 (1344)
TPM command server listening on port 2321
Platform server listening on port 2322
```
A file `NVChip` will be created at your current directory to store the status of the simulator. To stop the simulator use the shortcut key `Ctrl+C`.

Execute any `tpm2_` command with the tcti option set to simulator.
```
# Option 1
$ tpm2_getrandom -T mssim:host=localhost,port=2321 --hex 1

# Option 2
$ export TPM2TOOLS_TCTI="mssim:host=localhost,port=2321"
$ tpm2_getrandom --hex 1
```

# Behaviour of Microsoft Simulator

Perform TPM startup immediately after launching a simulator. Otherwise, all subsequent commands will fail with the error code 0x100 (TPM not initialized by TPM2_Startup)
```
$ tpm2_startup -T mssim:host=localhost,port=2321 -c
```

Keep an eye on the TPM transient memory.
```
$ tpm2_getcap -T mssim:host=localhost,port=2321 handles-transient
```

Once it hit 3 handles, the next command will fail with the error code 0x902 (out of memory for object contexts). Clear the transient memory.
```
$ tpm2_flushcontext -T mssim:host=localhost,port=2321 -t
```

# Install TPM OpenSSL Engine

Download tpm2-tss-engine.
```
$ git clone https://github.com/tpm2-software/tpm2-tss-engine ~/tpm2-tss-engine
$ cd ~/tpm2-tss-engine
$ git checkout v1.1.0
```

Build tpm2-tss-engine.
```
$ ./bootstrap
$ ./configure
$ make -j$(nproc)
```

Install tpm2-tss-engine.
```
$ sudo make install
$ sudo ldconfig
```

Check if the TPM engine can be located.
```
$ openssl engine -t -c tpm2tss
(tpm2tss) TPM2-TSS engine for OpenSSL
 [RSA, RAND]
     [ available ]
```

Generate random value without specifying the transmission interface (default to `/dev/tpmrm0`).
```
$ openssl rand -engine tpm2tss -hex 10
engine "tpm2tss" set.
5c2b7b419f8145197c3f
```

Generate random value with simulator.
```
$ export TPM2TSSENGINE_TCTI="mssim:host=localhost,port=2321"
$ openssl rand -engine tpm2tss -hex 10
engine "tpm2tss" set.
6e33ff4b64fc9b4bb376
```

# Exercise 1

This is an exercise using the OpenSSL command-line interface to perform client-server TLS communication. Download the repository:
```
$ git clone https://github.com/wxleong/optiga-tpm-2021-e2-training-day1 ~/optiga-tpm-2021-e2-training-day1
```

<p align="center">
    <img src="https://github.com/wxleong/optiga-tpm-2021-e2-training-day1/blob/master/media/client-server-tls.jpg" width="80%">
</p>

| Directory  | Description |
| ------------- | ------------- |
| [exercise-1-mssim-openssl-cli](https://github.com/wxleong/optiga-tpm-2021-e2-training-day1/tree/master/ubuntu/exercise-1-mssim-openssl-cli) | Using Microsoft TPM 2.0 simulator.<br>`$ cd ~/optiga-tpm-2021-e2-training-day1/ubuntu/exercise-1-mssim-openssl-cli` |

# Assessment

Objectives:
- To connect an OpenSSL client to the remote server (`ifapcoc-demo.com:5555`)
- Insert your name into the subject field of a client certificate (in the `CN` attribute short for CommonName) so you can be identified by the server
- Use the given CA keypair (assessment-remote-server/remote-ca.key) and certificate (assessment-remote-server/remote-ca.crt) to generate a client certificate

| Directory  | Description |
| ------------- | ------------- |
| [assessment-remote-server](https://github.com/wxleong/optiga-tpm-2021-e2-training-day1/tree/master/ubuntu/assessment-remote-server) | Private key and certificate of the remote server.<br>`$ cd ~/optiga-tpm-2021-e2-training-day1/ubuntu/assessment-remote-server` |
