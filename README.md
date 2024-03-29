# CGA work server

This project is a dedicated work server for [CGA cryptocurrency](https://www.cgacoin.net/). See the [documentation](https://docs.cgacoin.net/integration-guides/work-generation/) for details on work generation and the current network difficulty.

**cga-work-server** supports the `work_generate`, `work_cancel`, and `work_validate` commands from the CGA RPC.
For details on these commands, see [CGA RPC documentation](https://docs.cgacoin.net/commands/rpc-protocol/).

To see available command line options, run `cga-work-server --help`.

If using more than one work peer, give the flag `--shuffle`. This makes it so that the next request is picked randomly instead of sequentially, which leads to more efficient work generation with multiple peers, especially when they are not in the same network.


## Installation

### OpenCL

Ubuntu:

```
sudo apt install ocl-icd-opencl-dev
```

Fedora:

```
sudo dnf install ocl-icd-devel
```

Windows:
- AMD GPU: [OCL-SDK](https://github.com/GPUOpen-LibrariesAndSDKs/OCL-SDK/releases/)
- Nvidia GPU: [CUDA Toolkit](https://developer.nvidia.com/cuda-toolkit)

### Rust

Linux:

```
curl https://sh.rustup.rs -sSf | sh
```

Windows: follow instructions in https://www.rust-lang.org/tools/install

### Build

```bash
git clone https://github.com/cgacurrency/cga-work-server.git
cd cga-work-server
cargo build --release
```

Depending on your system configuration and if the OpenCL library cannot be found in the `PATH`, it may be necessary to link against explicitly:

```bash
cargo rustc --release -- -l OpenCL -L "/path/to/opencl.lib"`
```






### Bonding

```
sudo ln -s /home/<user>/cga-work-server/target/release/cga-work-server /usr/local/sbin/cga-work-server
```

### Service

```
sudo touch /etc/systemd/system/cga_work.service
sudo chmod 664 /etc/systemd/system/cga_work.service
sudo nano /etc/systemd/system/cga_work.service
```

Add Lines

```
[Unit]
Description=CGA WORKER service
After=network.target

[Service]
ExecStart=/home/<user>/cga-work-server/target/release/cga-work-server -g 0:0
LimitNOFILE=65536
Restart=on-failure
User=<user>
Group=<user>

[Install]
WantedBy=multi-user.target
```

Start/Stop or...
```
sudo systemctl enable cga_work
sudo systemctl stop cga_work
sudo systemctl start cga_work
sudo systemctl status cga_work
sudo systemctl restart cga_work
```


## Using

`cga-work-server --help`

_Note_ difficulty values may be outdated in these examples.

- `work_generate` example:

    ```json
    {
        "action": "work_generate",
        "hash": "718CC2121C3E641059BC1C2CFC45666C99E8AE922F7A807B7D07B62C995D79E2",
        "difficulty": "ffffffc000000000",
        "multiplier": "1.0" // overrides difficulty
    }
    ```
    Response:

    ```json
    {
        "work": "2bf29ef00786a6bc",
        "difficulty": "ffffffd21c3933f4",
        "multiplier": "1.3946469"        
    }
    ```


- `work_validate` example:

    ```json
    {
        "action": "work_validate",
        "hash": "718CC2121C3E641059BC1C2CFC45666C99E8AE922F7A807B7D07B62C995D79E2",
        "work": "2bf29ef00786a6bc",
        "difficulty": "ffffffc000000000",
        "multiplier": "1.0" // overrides difficulty
    }
    ```
    Response:

    ```json
    {
        "valid_all": "1",
        "valid_receive": "1",
        "difficulty": "ffffffd21c3933f4",
        "multiplier": "1.3946469"
    }
    ```

- `work_cancel` example:
    ```json
    {
        "action": "work_cancel",
        "hash": "718CC2121C3E641059BC1C2CFC45666C99E8AE922F7A807B7D07B62C995D79E2"
    }
    ```
    Response:

    ```json
    {
    }
    ```
