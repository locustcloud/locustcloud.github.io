<span style="font-size: 24px; font-weight: bold;">Welcome to Locust Cloud</span>

Thank you for choosing Locust Cloud as your load test provider!

* toc
{:toc}

If you have questions or get stuck, feel free to reach out to us at [support@locust.cloud](mailto:support@locust.cloud).

## Installation

locust-cloud is installed as a regular Python package:

```
pip install locust-cloud
```

## Authenticate

To authenticate, locust-cloud needs the credentials you set up during registration.
If you've just installed locust-cloud and try to run some command you'll be prompted to log in

```
$ locust-cloud -f locustfile.py
You need to authenticate before proceeding. Please run:
    locust-cloud --login
```

When running that command you will get to choose a region and a webpage will be opened in your browser where you will perform your login.

```
$ locust-cloud --login
Enter the number for the region to authenticate against

  1. us-east-1
  2. eu-north-1

> 1

Attempting to automatically open the SSO authorization page in your default browser.
If the browser does not open or you wish to use a different device to authorize this request, open the following URL:

https://auth.us-east-1.locust.cloud/login?<login parameters>
```

Once you have performed the login on the webpage credentials will be stored for you by the locust-cloud CLI and you will not
have to log in again for quite a while.

### Using locust-cloud in a CI/CD environment

If you want to run locust-cloud in a CI/CD environment an interactive login procedure is obviously not optimal.
For that usecase you can run locust-cloud with the `--non-interactive` flag where locust-cloud will expect to
find credentials in environment variables instead.

```
$ locust-cloud --non-interactive
Running with --non-interactive requires that LOCUSTCLOUD_USERNAME, LOCUSTCLOUD_PASSWORD and LOCUSTCLOUD_REGION environment variables are set.
```

Simply make sure to export the corrent environment variables before running the command and things will work.

```
export LOCUSTCLOUD_USERNAME='your@email.com'
export LOCUSTCLOUD_PASSWORD='yourpassword'
export LOCUSTCLOUD_REGION='us-east-1'
```

## Notes

* Remember to only direct locust-cloud at sites/services you actually own, or have permission to load test.

## Usage

### List available options

```
$ locust-cloud --help
usage: locust-cloud [-f <filename>] [-u USERS] [--loglevel LOGLEVEL] [--requirements REQUIREMENTS] [--login] [--non-interactive] [--workers WORKERS] [--delete] [--mock-server] [--profile PROFILE] [--extra-files [EXTRA_FILES ...]]

Launches a distributed Locust runs on locust.cloud infrastructure.

Example: locust-cloud -f my_locustfile.py --users 1000 ...

options:
  -f <filename>, --locustfile <filename>
                        The Python file that contains your test. Defaults to 'locustfile.py'.
  -u USERS, --users USERS
                        Number of users to launch. This is the same as the regular Locust argument, but also affects how many workers to launch.
  --workers WORKERS     Number of workers to use for the deployment. Defaults to number of users divided by 500, but the default may be customized for your account.
  --delete              Delete a running cluster. Useful if locust-cloud was killed/disconnected or if there was an error.
  --mock-server         Start a demo mock service and set --host parameter to point Locust towards it
  --profile PROFILE     Set a profile to group the testruns together
  --extra-files [EXTRA_FILES ...]
                        A list of extra files or directories to upload. Space-separated, e.g. --extra-files testdata.csv *.py my-directory/

advanced:
  --loglevel LOGLEVEL, -L LOGLEVEL
                        Set --loglevel DEBUG for extra info.
  --requirements REQUIREMENTS
                        Optional requirements.txt file that contains your external libraries.
  --login               Launch an interactive session to authenticate your user.
                        Once completed your credentials will be stored and automatically refreshed for quite a long time.
                        Once those expires you will be prompted to perform another login.
  --non-interactive     This can be set when, for example, running in a CI/CD environment to ensure no interactive steps while executing.
                        Requires that LOCUSTCLOUD_USERNAME, LOCUSTCLOUD_PASSWORD and LOCUSTCLOUD_REGION environment variables are set.

Any parameters not listed here are forwarded to locust master unmodified, so go ahead and use things like --users, --host, --run-time, ...
Locust config can also be set using config file (~/.locust.conf, locust.conf, pyproject.toml, ~/.cloud.conf or cloud.conf).
Parameters specified on command line override env vars, which in turn override config files.
```

### Basic way to run locust-cloud

```
locust-cloud -f my_locustfile.py --users 100 # ... other regular locust parameters
```

### Complete example

If you're learning Locust and want to try it out, Locust Cloud has its own demo mock server you can test against, using the locustfile below. Pass `--mock-server` to spawn the mock next to your load generators and point Locust towards it.

```bash
locust-cloud -f my_locustfile.py --users 100 --mock-server
```

```python
from locust import HttpUser, task


class MyUser(HttpUser):
    @task
    def t(self) -> None:
        self.client.post("/authenticate", json={"username": "foo", "password": "bar"})
        for product_id in [1, 2, 42]:
            self.client.get(f"/product/{product_id}", name="/product/{product_id}")
            self.client.post("/cart/add", json={"productId": product_id})
        self.client.post("/checkout/confirm")
        self.client.get("/this_does_not_exist")
```

Your results should be something like this (the mock intentionally has some concurrency issues, so you'll get steadily rising response times as the load increases):

![Locust Cloud](screenshot.png)

## Advanced example

Here's a more complex example showing how to pass additional parameters to Locust.

It will run a 100 user test, start immediately, run for 60 seconds,
quit 1s after finishing and print statistics to the console while running:

```
locust-cloud --mock-server --users 100 --autostart --run-time 60s --autoquit 1 --print-stats
```

Output:

```
[LOCUST-CLOUD] INFO: Authenticating (eu-north-1, v1.11.2.dev23)
[LOCUST-CLOUD] INFO: Deploying load generators
[LOCUST-CLOUD] INFO: Waiting for pods to be ready...
[2024-11-22 11:49:20,833] master-lb6vv-zxp64/INFO/locust.main: Starting Locust 2.32.4.dev14 (locust-cloud 1.11.2.dev23)
[2024-11-22 11:49:20,835] master-lb6vv-zxp64/INFO/locust.main: Starting web interface at https://locust.webui.locust.cloud/702ca98c-60e1-706c-9523-77d5c10ba5d4-free
[2024-11-22 11:49:23,515] master-lb6vv-zxp64/INFO/locust.runners: worker-xn7mv-4zkh5_3b4ee8708ea140eda830ff00364af204 (index 0) reported as ready. 1 workers connected.
[2024-11-22 11:49:24,018] master-lb6vv-zxp64/INFO/locust.main: Run time limit set to 60 seconds
[2024-11-22 11:49:24,019] master-lb6vv-zxp64/INFO/locust.runners: Sending spawn jobs of 100 users at 1.00 spawn rate to 1 ready workers
Type     Name                                         # reqs      # fails |    Avg     Min     Max    Med |   req/s  failures/s
--------|-------------------------------------------|-------|-------------|-------|-------|-------|-------|--------|-----------
POST     /authenticate                                     2     0(0.00%) |   1012    1010    1014   1010 |    0.00        0.00
GET      /product/{product_id}                             1     0(0.00%) |    505     505     505    505 |    0.00        0.00
--------|-------------------------------------------|-------|-------------|-------|-------|-------|-------|--------|-----------
         Aggregated                                        3     0(0.00%) |    843     505    1014   1000 |    0.00        0.00
Type     Name                                         # reqs      # fails |    Avg     Min     Max    Med |   req/s  failures/s
--------|-------------------------------------------|-------|-------------|-------|-------|-------|-------|--------|-----------
POST     /authenticate                                     5     0(0.00%) |   1015    1010    1021   1010 |    0.67        0.00
POST     /cart/add                                         5     0(0.00%) |   1005    1004    1007   1004 |    0.33        0.00
GET      /product/{product_id}                             8     0(0.00%) |    508     504     520    510 |    0.67        0.00
--------|-------------------------------------------|-------|-------------|-------|-------|-------|-------|--------|-----------
         Aggregated                                       18     0(0.00%) |    787     504    1021   1000 |    1.67        0.00
Type     Name                                         # reqs      # fails |    Avg     Min     Max    Med |   req/s  failures/s
--------|-------------------------------------------|-------|-------------|-------|-------|-------|-------|--------|-----------
POST     /authenticate                                     9     0(0.00%) |   1013    1005    1021   1005 |    0.83        0.00
POST     /cart/add                                        14     0(0.00%) |   1005    1003    1007   1003 |    1.17        0.00
POST     /checkout/confirm                                 2     0(0.00%) |   1005    1005    1005   1005 |    0.00        0.00
GET      /product/{product_id}                            18     0(0.00%) |    507     503     520    510 |    1.67        0.00
GET      /this_does_not_exist                              2   2(100.00%) |      4       3       6      3 |    0.00        0.00
...
[2024-11-22 11:50:24,019] master-lb6vv-zxp64/INFO/locust.main: --run-time limit reached, stopping test
[2024-11-22 11:50:27,151] master-lb6vv-zxp64/INFO/locust.main: Shutting down (exit code 0)
Type     Name                                         # reqs      # fails |    Avg     Min     Max    Med |   req/s  failures/s
--------|-------------------------------------------|-------|-------------|-------|-------|-------|-------|--------|-----------
POST     /authenticate                                   112     0(0.00%) |   2586    1003    4553   2500 |    1.89        0.00
POST     /cart/add                                       239     0(0.00%) |   2674    1003    4989   2500 |    4.03        0.00
POST     /checkout/confirm                                60     0(0.00%) |   2633    1003    4521   2500 |    1.01        0.00
GET      /product/{product_id}                           262     0(0.00%) |   2156     503    4489   2000 |    4.41        0.00
GET      /this_does_not_exist                             60  60(100.00%) |      4       2       8      4 |    1.01        1.01
--------|-------------------------------------------|-------|-------------|-------|-------|-------|-------|--------|-----------
         Aggregated                                      733    60(8.19%) |   2254       2    4989   2100 |   12.35        1.01
...
Error report
# occurrences      Error
------------------|------------------------------------------------------------------------------------------------------------
60                 GET /this_does_not_exist: HTTPError('404 Client Error: NOT FOUND for url: /this_does_not_exist')
------------------|------------------------------------------------------------------------------------------------------------
[LOCUST-CLOUD] INFO: Tearing down Locust cloud...
[LOCUST-CLOUD] INFO: Done! ✨
```

## Extra python packages

If your locustfile needs some additional Python package, you can instruct it to install them by specifying a [requirements.txt](https://pip.pypa.io/en/stable/reference/requirements-file-format/) file:

```
locust-cloud -f my_locustfile.py --requirements requirements.txt
```
