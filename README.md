## Installation

```bash
pip install locust-cloud
```

```bash
# in your .bashrc or other login script
export LOCUST_CLOUD_USERNAME='your@email.com'
export LOCUST_CLOUD_PASSWORD='yourpassword'
```

## Usage

```bash
locust-cloud -f my_locustfile.py --users 1000 # and any other regular locust parameters
```

## Extra python packages

If your locustfile needs some additional Python package, you can instruct it to install them by specifying a [requirements.txt](https://pip.pypa.io/en/stable/reference/requirements-file-format/) file:

```bash
locust-cloud -f my_locustfile.py --requirements requirements.txt
```

## Notes

* Remember to only direct locust-cloud at sites/services you actually own, or have permission to load test.
