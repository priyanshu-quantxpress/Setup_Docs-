This guide explains how to install **Python 3.10.14** on a Linux server that **does not have internet access**. The installation is performed by downloading the required files on a Windows machine with internet connectivity and then transferring them to the Linux server.
 
 Phase 1 — Download on Windows (Internet Machine)

1. Open your browser.
2. Go to the official Python downloads page:
    
    [https://www.python.org/downloads/source/](https://www.python.org/downloads/source/)
    
3. Under **Stable Releases**, download:

```
Python 3.10.14 → Gzipped source tarball
```

1. The downloaded file will be:

```
Python-3.10.14.tgz
```



### Phase 2 — Transfer Files to the Linux Server

Using **WinSCP** (or similar file transfer tool):

1. Connect to the Linux server.
2. Copy the downloaded file to:

```
/opt/apps
```


### Phase 3 — Build and Install Python on Linux

Login to the Linux server using **PuTTY** (SSH).

---

### Step 1 — Navigate to the Software Directory

```bash
cd /home/dev/softwares
ls
```

Expected output:

```
Python-3.10.14.tgz
```

---

### Step 2 — Extract the Source Code

```bash
tar -xvf Python-3.10.14.tgz
cd Python-3.10.14
```



### Step 3 — Verify Build Tools

Ensure the required build tools are installed:

```bash
gcc --version
make --version
```

Both commands should return version information.

---

### Step 4 — Configure the Build

Run the configuration script:

```bash
./configure --enable-optimizations
```

This prepares the environment and checks system dependencies before compilation.



### Step 5 — Compile the Source

```bash
make -j4
```

This compiles Python using multiple CPU cores for faster build time.

---

### Step 6 — Install Safely (Recommended)

```bash
sudo make altinstall
```

`altinstall` ensures that the system’s default Python version is **not overwritten**, which is critical for Linux system stability.


## Installation Locations

After successful installation:

```
/usr/local/bin/python3.10
/usr/local/bin/pip3.10
```

System Python remains unchanged:

```
/usr/bin/python3
```

---

## Verify Installation

```bash
/usr/local/bin/python3.10 --version
```

Expected output:

```
Python 3.10.14
```

### (Optional) Add Python to PATH

To make Python easily accessible:

```bash
echo 'export PATH=/usr/local/bin:$PATH' >> ~/.bashrc
source ~/.bashrc
```

Now you can verify using:

```bash
python3.10 --version
```

## Summary

This offline installation approach allows Python to be installed safely on internet-restricted Linux servers while preserving system stability. By building from source and using `altinstall`, you ensure compatibility and avoid conflicts with the system’s default Python environment.
