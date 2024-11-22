## Welcome to Locust Cloud

Thanks for choosing Locust Cloud as your load test provider!

If you have questions or get stuck, feel free to reach out to us on [support@locust.cloud](mailto:support@locust.cloud).

## Installation

locust-cloud is installed as a regular Python program:

```bash
pip install locust-cloud
```

To authenticate, locust-cloud needs the credentials you set up during registration.

In your `.zshrc`/`.bashrc` or other login script, add this:

```bash
export LOCUSTCLOUD_USERNAME='your@email.com'
export LOCUSTCLOUD_PASSWORD='yourpassword'
# us-east-1 is the default region, if you are in EU, use this:
export LOCUSTCLOUD_REGION='eu-north-1'
```

## Notes

* Remember to only direct locust-cloud at sites/services you actually own, or have permission to load test.

## Usage

### List available options

```bash
locust-cloud --help
```

### Basic way to run locust-cloud

```bash
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

```bash
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
Type     Name                                                                          # reqs      # fails |    Avg     Min     Max    Med |   req/s  failures/s
--------|----------------------------------------------------------------------------|-------|-------------|-------|-------|-------|-------|--------|-----------
--------|----------------------------------------------------------------------------|-------|-------------|-------|-------|-------|-------|--------|-----------
         Aggregated                                                                         0     0(0.00%) |      0       0       0      0 |    0.00        0.00
Type     Name                                                                          # reqs      # fails |    Avg     Min     Max    Med |   req/s  failures/s
--------|----------------------------------------------------------------------------|-------|-------------|-------|-------|-------|-------|--------|-----------
POST     /authenticate                                                                      2     0(0.00%) |   1012    1010    1014   1010 |    0.00        0.00
GET      /product/{product_id}                                                              1     0(0.00%) |    505     505     505    505 |    0.00        0.00
--------|----------------------------------------------------------------------------|-------|-------------|-------|-------|-------|-------|--------|-----------
         Aggregated                                                                         3     0(0.00%) |    843     505    1014   1000 |    0.00        0.00
Type     Name                                                                          # reqs      # fails |    Avg     Min     Max    Med |   req/s  failures/s
--------|----------------------------------------------------------------------------|-------|-------------|-------|-------|-------|-------|--------|-----------
POST     /authenticate                                                                      2     0(0.00%) |   1012    1010    1014   1010 |    0.00        0.00
GET      /product/{product_id}                                                              1     0(0.00%) |    505     505     505    505 |    0.00        0.00
--------|----------------------------------------------------------------------------|-------|-------------|-------|-------|-------|-------|--------|-----------
         Aggregated                                                                         3     0(0.00%) |    843     505    1014   1000 |    0.00        0.00
Type     Name                                                                          # reqs      # fails |    Avg     Min     Max    Med |   req/s  failures/s
--------|----------------------------------------------------------------------------|-------|-------------|-------|-------|-------|-------|--------|-----------
POST     /authenticate                                                                      5     0(0.00%) |   1015    1010    1021   1010 |    0.67        0.00
POST     /cart/add                                                                          5     0(0.00%) |   1005    1004    1007   1004 |    0.33        0.00
GET      /product/{product_id}                                                              8     0(0.00%) |    508     504     520    510 |    0.67        0.00
--------|----------------------------------------------------------------------------|-------|-------------|-------|-------|-------|-------|--------|-----------
         Aggregated                                                                        18     0(0.00%) |    787     504    1021   1000 |    1.67        0.00
Type     Name                                                                          # reqs      # fails |    Avg     Min     Max    Med |   req/s  failures/s
--------|----------------------------------------------------------------------------|-------|-------------|-------|-------|-------|-------|--------|-----------
POST     /authenticate                                                                      9     0(0.00%) |   1013    1005    1021   1005 |    0.83        0.00
POST     /cart/add                                                                         14     0(0.00%) |   1005    1003    1007   1003 |    1.17        0.00
POST     /checkout/confirm                                                                  2     0(0.00%) |   1005    1005    1005   1005 |    0.00        0.00
GET      /product/{product_id}                                                             18     0(0.00%) |    507     503     520    510 |    1.67        0.00
GET      /this_does_not_exist                                                               2   2(100.00%) |      4       3       6      3 |    0.00        0.00
...
[2024-11-22 11:50:24,019] master-lb6vv-zxp64/INFO/locust.main: --run-time limit reached, stopping test
[2024-11-22 11:50:24,020] master-lb6vv-zxp64/INFO/locust.runners: worker-xn7mv-4zkh5_3b4ee8708ea140eda830ff00364af204 (index 0) reported that it has stopped
[2024-11-22 11:50:24,021] master-lb6vv-zxp64/INFO/locust.runners: worker-xn7mv-4zkh5_3b4ee8708ea140eda830ff00364af204 (index 0) reported as ready. 1 workers connected.
[2024-11-22 11:50:24,743] master-lb6vv-zxp64/INFO/locust.runners: Worker 'worker-xn7mv-4zkh5_3b4ee8708ea140eda830ff00364af204' (index 0) quit. 0 workers ready.
[2024-11-22 11:50:24,743] master-lb6vv-zxp64/INFO/locust.runners: The last worker quit, stopping test.
Type     Name                                                                          # reqs      # fails |    Avg     Min     Max    Med |   req/s  failures/s
--------|----------------------------------------------------------------------------|-------|-------------|-------|-------|-------|-------|--------|-----------
POST     /authenticate                                                                    112     0(0.00%) |   2586    1003    4553   2500 |    2.10        0.00
POST     /cart/add                                                                        239     0(0.00%) |   2674    1003    4989   2500 |    4.30        0.00
POST     /checkout/confirm                                                                 60     0(0.00%) |   2633    1003    4521   2500 |    1.30        0.00
GET      /product/{product_id}                                                            262     0(0.00%) |   2156     503    4489   2000 |    4.70        0.00
GET      /this_does_not_exist                                                              60  60(100.00%) |      4       2       8      4 |    1.30        1.30
--------|----------------------------------------------------------------------------|-------|-------------|-------|-------|-------|-------|--------|-----------
         Aggregated                                                                       733    60(8.19%) |   2254       2    4989   2100 |   13.70        1.30
[2024-11-22 11:50:26,149] master-lb6vv-zxp64/INFO/locust.main: --autoquit time reached, shutting down
Type     Name                                                                          # reqs      # fails |    Avg     Min     Max    Med |   req/s  failures/s
--------|----------------------------------------------------------------------------|-------|-------------|-------|-------|-------|-------|--------|-----------
POST     /authenticate                                                                    112     0(0.00%) |   2586    1003    4553   2500 |    2.10        0.00
POST     /cart/add                                                                        239     0(0.00%) |   2674    1003    4989   2500 |    4.30        0.00
POST     /checkout/confirm                                                                 60     0(0.00%) |   2633    1003    4521   2500 |    1.30        0.00
GET      /product/{product_id}                                                            262     0(0.00%) |   2156     503    4489   2000 |    4.70        0.00
GET      /this_does_not_exist                                                              60  60(100.00%) |      4       2       8      4 |    1.30        1.30
--------|----------------------------------------------------------------------------|-------|-------------|-------|-------|-------|-------|--------|-----------
         Aggregated                                                                       733    60(8.19%) |   2254       2    4989   2100 |   13.70        1.30
[2024-11-22 11:50:27,151] master-lb6vv-zxp64/INFO/locust.main: Shutting down (exit code 0)
```

## Extra python packages

If your locustfile needs some additional Python package, you can instruct it to install them by specifying a [requirements.txt](https://pip.pypa.io/en/stable/reference/requirements-file-format/) file:

```bash
locust-cloud -f my_locustfile.py --requirements requirements.txt
```
