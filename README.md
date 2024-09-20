## Installation

```bash
pip install locust-cloud
```

## Usage

```bash
# in your .bashrc or other login script
export LOCUST_CLOUD_USERNAME='your@email.com'
export LOCUST_CLOUD_PASSWORD='yourpassword'
```

```bash
locust-cloud -f my_locust_file.py --users 1000 ... # any other regular locust parameters
```

## Extra python packages

If your locust file needs some additional Python package, you can instruct it to install them by specifying a [requirements.txt](https://pip.pypa.io/en/stable/reference/requirements-file-format/) file:

```bash
locust-cloud -f my_locust_file.py --requirements requirements.txt
```

## Notes

* Remember to only direct locust-cloud at sites/services you actually own, or have permission to load test.
