## Installation

```bash
pip install locust-cloud
```

```bash
# in your .bashrc or other login script
export LOCUSTCLOUD_USERNAME='your@email.com'
export LOCUSTCLOUD_PASSWORD='yourpassword'
export AWS_DEFAULT_REGION="us-east-1"
```

## Usage

List options specific to locust-cloud:

```bash
locust-cloud --help
```

Basic way to run locust-cloud:

```bash
locust-cloud -f my_locustfile.py --users 1000 # and any other regular locust parameters
```

<!-- Spawn a basic mock service for testing:

```bash
locust-cloud -f my_locustfile.py --users 1000 --mock-server
```
-->

## Extra python packages

If your locustfile needs some additional Python package, you can instruct it to install them by specifying a [requirements.txt](https://pip.pypa.io/en/stable/reference/requirements-file-format/) file:

```bash
locust-cloud -f my_locustfile.py --requirements requirements.txt
```

## Notes

* Remember to only direct locust-cloud at sites/services you actually own, or have permission to load test.
