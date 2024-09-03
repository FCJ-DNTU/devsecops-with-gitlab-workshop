+++
title = "Deploy Performance Testing with Grafana (k6)"
date = "`r Sys.Date()`"
weight = 1
chapter = false
pre = "<b>11.1 </b>"
+++

### 1. Install the Tool

You can refer to the installation guide here: [Install k6 | Grafana k6 documentation](https://grafana.com/docs/k6/latest/set-up/install-k6/)

We will install k6 on the Build Instance.

```bash
sudo gpg -k
sudo gpg --no-default-keyring --keyring /usr/share/keyrings/k6-archive-keyring.gpg --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys C5AD17C747E3415A3642D57D77C6C491D6AC1D69
echo "deb [signed-by=/usr/share/keyrings/k6-archive-keyring.gpg] https://dl.k6.io/deb stable main" | sudo tee /etc/apt/sources.list.d/k6.list
sudo apt-get update
sudo apt-get install k6
k6 --version
```

![image.png](/images/11-performancetesting/11.1-k6/image.png?featherlight=false&width=60pc)

### 2. Create a Working Directory and Script

In the previous sections, we have two projects, frontend and backend, running.

![image.png](/images/11-performancetesting/11.1-k6/image%201.png?featherlight=false&width=60pc)

![image.png](/images/11-performancetesting/11.1-k6/image%202.png?featherlight=false&width=60pc)

We will create a working directory and a `load-test.js` file to perform performance testing on these two projects.

```bash
mkdir -p tools/test-k6 && cd tools/test-k6 && vi load-test.js
```

For example, we will create a script that simulates 100 concurrent users accessing the website for 20 seconds, with 95% of HTTP requests completing in under 500ms.

Enter the following command:

```bash
import http from 'k6/http';
import {check, sleep} from 'k6';

export let options = {
	vus: 100,
	duration: '10s',
	thresholds: {
		http_req_duration: ['p(95)<500']
	}
}

export default function () {
	let res = http.get('http://3.106.228.72:3000/');
	check(res, {'status was 200': (r) => r.status === 200});
	sleep(1);
}

```

{{% notice info %}}
You must adjust the IPv4 address of your running frontend project.
{{% /notice %}}

Run k6 to simulate the scenario

```bash
k6 run load-test.js
```

Here are the results returned:

![image.png](/images/11-performancetesting/11.1-k6/image%203.png?featherlight=false&width=60pc)

Explanation:

**Overview**:

- **checks:** Shows the percentage of successful checks and the total number of checks performed. (`100.00% ✓ 1000` means all 1000 checks were successful).
- **data_received:** Total amount of data received during the test (`963 kB` with a rate of `95 kB/s`).
- **data_sent:** Total amount of data sent during the test (`83 kB` with a rate of `8.2 kB/s`).

**HTTP Request Metrics**:

- **http_req_blocked:** Time the HTTP request was blocked before it was actually sent.
    - `avg=724.51µs`: Average time requests were blocked before being sent.
    - `min=2.17µs`: Shortest time requests were blocked.
    - `med=2.91µs`: Median time requests were blocked.
    - `max=12.76ms`: Longest time requests were blocked.
    - `p(90)=2.86ms`: 90% of requests were blocked for this duration or less.
    - `p(95)=6.76ms`: 95% of requests were blocked for this duration or less.
- **http_req_connecting:** Time spent establishing a TCP connection to the server.
    - `avg=713.64µs`: Average time spent connecting to the server.
    - `min=0s`: Shortest connection time.
    - `med=0s`: Median connection time.
    - `max=12.72ms`: Longest connection time.
    - `p(90)=282.96µs`: 90% of connection times were this duration or less.
    - `p(95)=6.73ms`: 95% of connection times were this duration or less.
- **http_req_duration:** Time to complete an HTTP request, including sending, receiving, and waiting.
    - `avg=6.23ms`: Average time for a request.
    - `min=423.68µs`: Shortest request duration.
    - `med=6.36ms`: Median time to complete a request.
    - `max=23.84ms`: Longest request duration.
    - `p(90)=11.38ms`: 90% of requests completed within this duration or less.
    - `p(95)=13.49ms`: 95% of requests completed within this duration or less.
- **http_req_failed:** Failure rate of requests, here `0.00%`, meaning no requests failed.
- **http_req_receiving:** Time to receive a response from the server.
    - `avg=79.87µs`: Average time to receive a response.
    - `min=11.5µs`: Shortest response time.
    - `med=17.99µs`: Median response time.
    - `max=10.67ms`: Longest response time.
    - `p(90)=55.61µs`: 90% of responses received within this duration or less.
    - `p(95)=145.16µs`: 95% of responses received within this duration or less.
- **http_req_sending:**
    - `avg=232.81µs`: Average time needed to send a request to the server.
    - `min=4.75µs`: Shortest time to send a request.
    - `med=11.63µs`: Median time to send a request.
    - `max=6.6ms`: Longest time to send a request.
    - `p(90)=837.36µs`: 90% of requests sent within this duration or less.
    - `p(95)=1.17ms`: 95% of requests sent within this duration or less.
- **http_req_tls_handshaking:** Time spent on TLS handshake, here `0s`, meaning no TLS handshake occurred since we haven’t registered a certificate for http.
- **http_req_waiting:** Time waiting for a server response after sending a request.
    - `avg=5.91ms`: Average wait time.
    - `min=386.46µs`: Shortest wait time.
    - `med=6.09ms`: Median wait time.
    - `max=21.92ms`: Longest wait time.
    - `p(90)=10.59ms`: 90% of waits were within this duration or less.
    - `p(95)=12.38ms`: 95% of waits were within this duration or less.

**Other Metrics**:

- **http_reqs:** Total number of HTTP requests made, here `1000` with a rate of `99.023682/s`.
- **iteration_duration:** Average time to complete one iteration of a test script.
    - `avg=1s`: Average time for each iteration.
    - `med=1s`: Median time for each iteration.
    - `max=1.03s`: Longest time for each iteration.
    - `p(90)=1.01s`: 90% of iterations completed within this duration or less.
    - `p(95)=1.01s`: 95% of iterations completed within this duration or less.
- **iterations:** Number of test iterations, also `1000`.
- **vus:** Number of virtual users, here `100`.
- **vus_max:** Maximum number of virtual users used, also `100`.

**Conclusion**:

- **Overall Performance:** The system handles HTTP requests quickly and consistently with very low response times. No requests failed, indicating good system performance under load.
- **Scalability:** With 100 virtual users and 1000 requests, the system appears to handle this load without significant issues. Further testing with higher loads is needed to assess scalability better.
- **Response and Processing Times:** All requests are processed with very good response times, indicating the system can respond quickly even under load.

Next, we will create a `login-test.js` file to test the performance of the backend.

```bash
vi login-test.js 
```

Enter the following command:

```bash
import http from 'k6/http';
import {sleep} from 'k6';

export default function () {
	const url = "http://3.106.228.72:5214/api/User/login";
	
	const payload = JSON.stringify({
		email: "elroydevops@gmail.com",
		password: "0900000009"
	});
	
	const headers = {
		'Content-Type': 'application/json'
	};
	
	const res = http.post(url, payload, {headers});
	
	if (res.status === 200 || res.status === 201) {
		console.log('Signin successfully!');
	} else {
			console.error('Signin fail. Error code:', res.status);
	}
	
	sleep(1);
}
```

Run the command `k6`

```bash
k6 run -u 100 -d 20s login-test.js
```

{{% notice info %}}
If you encounter a 100% failure rate, you may want to check if your Build Instance is able to connect to the backend in the Deploy Instance.
![image.png](/images/11-performancetesting/11.1-k6/image%204.png?featherlight=false&width=60pc)
{{% /notice %}}

Results:

![image.png](/images/11-performancetesting/11.1-k6/image%205.png?featherlight=false&width=60pc)

Now we will test with a script where the number of users will change irregularly.

```bash
vi spike-test.js
```

Enter the following commands:

```bash
import http from 'k6/http';
import { sleep, check } from 'k6';

export let options = {
    stages: [
        {duration: '1m', target: 50},
        {duration: '10s', target: 500},
        {duration: '40s', target: 50},
        {duration: '20s', target: 10},
    ]
};

export default function () {
    const res = http.get('http://3.106.228.72:3000/');
    check(res, {'status is 200': (r) => r.status ===200});
    sleep(1);
}
```

Run the simulation:

```bash
k6 run spike-test.js
```

View the results. For the first minute, there will be 50 users, then for the next 10 seconds, it will spike to 500 users, then drop back to 50 users for the next 40 seconds, and finally reduce to 10 users in the last 20 seconds.

![image.png](/images/11-performancetesting/11.1-k6/image%206.png?featherlight=false&width=60pc)

### 3. Implementing Performance Testing in Frontend

First, we will create a new branch `pipeline-fe-11.1-k6` from the `main` branch.

![image.png](/images/11-performancetesting/11.1-k6/image%207.png?featherlight=false&width=60pc)

We will create a new folder.

![image.png](/images/11-performancetesting/11.1-k6/image%208.png?featherlight=false&width=60pc)

Name the folder `performance_test_script`.

![image.png](/images/11-performancetesting/11.1-k6/image%209.png?featherlight=false&width=60pc)

Click into the newly created folder.

![image.png](/images/11-performancetesting/11.1-k6/image%2010.png?featherlight=false&width=60pc)

Create a new file in the folder.

![image.png](/images/11-performancetesting/11.1-k6/image%2011.png?featherlight=false&width=60pc)

Name the file `smoke-test.js` and enter the following commands:

```bash
import http from 'k6/http';
import {check, sleep} from 'k6';

export let options = {
    vus: 1,
    duration: '10s'
};

export default function () {
    const res = http.get('http://3.106.228.72:3000/');
    check(res, {'status is 200': (r) => r.status ===200});
}
```

![image.png](/images/11-performancetesting/11.1-k6/image%2012.png?featherlight=false&width=60pc)

We will simulate a test script with a low number of users, called `smoke-test`, to check the feasibility of the deployment process. During the pre-production phase, server resources will be limited, so using `smoke-test` in CI/CD is appropriate.

Go to the pipeline editor to make modifications.

```bash
variables:
    USER_PROJECT: "ecommerce"
    PATH_PROJECT: "/home/${USER_PROJECT}/${CI_PROJECT_NAME}"
    IMAGE_VERSION: "${CI_REGISTRY_USER}/${CI_PROJECT_NAME}-${USER_PROJECT}:${CI_COMMIT_REF_NAME}_${CI_COMMIT_SHORT_SHA}"
    K6_PERFORMANCE_TEST_REPORT: "performance_test_${CI_PROJECT_NAME}_${CI_COMMIT_REF_NAME}_${CI_COMMIT_SHORT_SHA}_report"

stages:
    - clone
    - SAST
    - SCA
    - build
    - push registry
    - image scan
    - deploy
    - performance test

before_script:
    - sudo mkdir -p $PATH_PROJECT

clone repository:
    stage: clone
    script:
        - echo "Repository cloned."
    tags:
        - group-ecommerce-shell-runner-build

build:
    stage: build
    variables:
        GIT_STRATEGY: clone
    before_script:
        - sudo docker login -u $CI_REGISTRY_USER -p $CI_REGISTRY_PWD
    script:
        - sudo docker build -t $IMAGE_VERSION .
    after_script:
        - sudo docker logout
    tags:
        - group-ecommerce-shell-runner-build
    when: manual
    only:
        - tags

dockerhub pushing:
    stage: push registry
    variables:
        GIT_STRATEGY: none
    before_script:
        - sudo docker login -u $CI_REGISTRY_USER -p $CI_REGISTRY_PWD
    script:
        - sudo docker push $IMAGE_VERSION
    after_script:
        - sudo docker logout
    tags:
        - group-ecommerce-shell-runner-build
    needs:
        - job: build
    only:
        - tags

deploy:
    stage: deploy
    variables:
        GIT_STRATEGY: none
    before_script:
        - sudo docker login -u $CI_REGISTRY_USER -p $CI_REGISTRY_PWD
    script:
        - sudo docker pull $IMAGE_VERSION
        - sudo su ${USER_PROJECT} -c "
            container_exists=\$(sudo docker ps -a -q -f name=${CI_PROJECT_NAME});
            if [ ! -z \"\$container_exists\" ]; then
                sudo docker rm -f ${CI_PROJECT_NAME};
            fi;
            sudo docker run --name ${CI_PROJECT_NAME} -dp ${FRONTEND_PORT}:80 ${IMAGE_VERSION}"
    after_script:
        - sudo docker logout
    tags:
        - group-ecommerce-shell-runner
    when: manual
    only:
        - tags

k6 testing performance:
    stage: performance test
    variables:
        GIT_STRATEGY: clone
    script:
        - echo "$(pwd)"
        - |
            if ! command -v jq &> /dev/null; then
                echo "jq is not installed. Installing jq..."
                sudo apt-get update -y && sudo apt-get install -y jq
            else
                echo "jq is already installed"
            fi
        - chmod -R 777 ./performance_test_script/
        - docker run --rm -v $(pwd)/performance_test_script:/performance_test_script loadimpact/k6 run -e RESULTS_PATH=/performance_test_script --summary-export=/performance_test_script/summary_perf.json /performance_test_script/smoke-test.js
        - cat ./performance_test_script/summary_perf.json | jq -r '["metric", "avg", "min", "med", "max", "p(90)", "p(95)"], (.metrics | to_entries[] | [.key, .value.avg, .value.min, .value.med, .value.max, .value["p(90)"], .value["p(95)"]]) | @csv' > $K6_PERFORMANCE_TEST_REPORT.csv
    after_script:
        - sudo chown -R gitlab-runner $(pwd)
    tags:
        - group-ecommerce-shell-runner-build
    only: 
        - tags
    artifacts:
        paths:
            - $K6_PERFORMANCE_TEST_REPORT.csv
        expire_in: 1 day
```

We create tags and view the pipeline results.

![image.png](/images/11-performancetesting/11.1-k6/image%2013.png?featherlight=false&width=60pc)

Here is the result of the .csv file.

![image.png](/images/11-performancetesting/11.1-k6/image%2014.png?featherlight=false&width=60pc)

Now we will convert the CSV file to HTML. We will do this in a new branch named **pipeline-fe-11.1-k6-v2** from **pipeline-fe-11.1-k6**.

You can refer to: [benc-uk/k6-reporter: Output K6 test run results as formatted & easy to read HTML reports (github.com)](https://github.com/benc-uk/k6-reporter)

![image.png](/images/11-performancetesting/11.1-k6/image%2015.png?featherlight=false&width=60pc)

Modify the file `performance_test_script/smoke-test.js`

```bash
import http from 'k6/http';
import { check, sleep } from 'k6';
import { htmlReport } from "https://raw.githubusercontent.com/benc-uk/k6-reporter/main/dist/bundle.js";

export let options = {
    vus: 1,
    duration: '10s',
};

export default function () {
    const BASE_URL = 'http://3.106.228.72:3000/';
    let res = http.get(BASE_URL);
    check(res, {
        'homepage status is 200': (r) => r.status === 200,
    });
    sleep(1);
}

export function handleSummary(data) {
    return {
        "/performance_test_script/summary.html": htmlReport(data),
    };
}
```

![image.png](/images/11-performancetesting/11.1-k6/image%2016.png?featherlight=false&width=60pc)

Modify the `gitlab-ci.yml` file

```bash
variables:
    USER_PROJECT: "ecommerce"
    PATH_PROJECT: "/home/${USER_PROJECT}/${CI_PROJECT_NAME}"
    IMAGE_VERSION: "${CI_REGISTRY_USER}/${CI_PROJECT_NAME}-${USER_PROJECT}:${CI_COMMIT_REF_NAME}_${CI_COMMIT_SHORT_SHA}"
    K6_PERFORMANCE_TEST_REPORT: "performance_test_${CI_PROJECT_NAME}_${CI_COMMIT_REF_NAME}_${CI_COMMIT_SHORT_SHA}_report"

stages:
    - clone
    - SAST
    - SCA
    - build
    - push registry
    - image scan
    - deploy
    - performance test

before_script:
    - sudo mkdir -p $PATH_PROJECT

clone repository:
    stage: clone
    script:
        - echo "Repository cloned."
    tags:
        - group-ecommerce-shell-runner-build

build:
    stage: build
    variables:
        GIT_STRATEGY: clone
    before_script:
        - sudo docker login -u $CI_REGISTRY_USER -p $CI_REGISTRY_PWD
    script:
        - sudo docker build -t $IMAGE_VERSION .
    after_script:
        - sudo docker logout
    tags:
        - group-ecommerce-shell-runner-build
    when: manual
    only:
        - tags

dockerhub pushing:
    stage: push registry
    variables:
        GIT_STRATEGY: none
    before_script:
        - sudo docker login -u $CI_REGISTRY_USER -p $CI_REGISTRY_PWD
    script:
        - sudo docker push $IMAGE_VERSION
    after_script:
        - sudo docker logout
    tags:
        - group-ecommerce-shell-runner-build
    needs:
        - job: build
    only:
        - tags

deploy:
    stage: deploy
    variables:
        GIT_STRATEGY: none
    before_script:
        - sudo docker login -u $CI_REGISTRY_USER -p $CI_REGISTRY_PWD
    script:
        - sudo docker pull $IMAGE_VERSION
        - sudo su ${USER_PROJECT} -c "
            container_exists=\$(sudo docker ps -a -q -f name=${CI_PROJECT_NAME});
            if [ ! -z \"\$container_exists\" ]; then
                sudo docker rm -f ${CI_PROJECT_NAME};
            fi;
            sudo docker run --name ${CI_PROJECT_NAME} -dp ${FRONTEND_PORT}:80 ${IMAGE_VERSION}"
    after_script:
        - sudo docker logout
    tags:
        - group-ecommerce-shell-runner
    when: manual
    only:
        - tags

k6 testing performance:
    stage: performance test
    variables:
        GIT_STRATEGY: clone
    script:
        - echo "$(pwd)"
        - chmod -R 777 ./performance_test_script/
        - docker run --rm -v $(pwd)/performance_test_script:/performance_test_script loadimpact/k6 run -e RESULTS_PATH=/performance_test_script --summary-export=/performance_test_script/summary_perf.json /performance_test_script/smoke-test.js
        - mv ./performance_test_script/summary.html $(pwd)/$K6_PERFORMANCE_TEST_REPORT.html
        - sudo chown -R gitlab-runner $(pwd)
    tags:
        - group-ecommerce-shell-runner-build
    only: 
        - tags
    artifacts:
        paths:
            - $K6_PERFORMANCE_TEST_REPORT.html
        expire_in: 1 day
```

Successful run results.

![image.png](/images/11-performancetesting/11.1-k6/image%2017.png?featherlight=false&width=60pc)

The HTML file is more dynamic and visual.

![image.png](/images/11-performancetesting/11.1-k6/image%2018.png?featherlight=false&width=60pc)

### 4. Implementing Performance Testing in Backend

First, we will create a new branch `pipeline-be-11.1-k6` from the `main` branch.

We will create a new folder named `performance_test_script` and create a new file in the folder named `login-test.js`.

Enter the following commands:

```bash
import http from 'k6/http';
import {sleep} from 'k6';
import { htmlReport } from "https://raw.githubusercontent.com/benc-uk/k6-reporter/main/dist/bundle.js";

export default function () {
    const url = "http://3.106.228.72:5214/api/User/login";
    
    const payload = JSON.stringify({
        email: "elroydevops@gmail.com",
        password: "0900000009"
    });
    
    const headers = {
        'Content-Type': 'application/json'
    };
    
    const res = http.post(url, payload, {headers});
    
    if (res.status === 200 || res.status === 201) {
        console.log('Signin successfully!');
    } else {
        console.error('Signin fail. Error code:', res.status);
    }
    
    sleep(1);
}

export function handleSummary(data) {
    return {
        "/performance_test_script/summary.html": htmlReport(data),
    };
}
```

![image.png](/images/11-performancetesting/11.1-k6/image%2019.png?featherlight=false&width=60pc)

Go to the pipeline editor to make modifications.

```bash
variables:
    USER_PROJECT: "ecommerce"
    PATH_PROJECT: "/home/${USER_PROJECT}/${CI_PROJECT_NAME}"
    IMAGE_VERSION: "${CI_REGISTRY_USER}/${CI_PROJECT_NAME}-${USER_PROJECT}:${CI_COMMIT_REF_NAME}_${CI_COMMIT_SHORT_SHA}"
    K6_PERFORMANCE_TEST_REPORT: "performance_test_${CI_PROJECT_NAME}_${CI_COMMIT_REF_NAME}_${CI_COMMIT_SHORT_SHA}_report"

stages:
    - clone
    - SAST
    - SCA
    - build
    - push registry
    - image scan
    - deploy
    - performance test

before_script:
    - sudo mkdir -p $PATH_PROJECT

clone repository:
    stage: clone
    script:
        - echo "Repository cloned."
    tags:
        - group-ecommerce-shell-runner-build

build:
    stage: build
    variables:
        GIT_STRATEGY: clone
    before_script:
        - sudo docker login -u $CI_REGISTRY_USER -p $CI_REGISTRY_PWD
    script:
        - sudo docker build -t $IMAGE_VERSION .
    after_script:
        - sudo docker logout
    tags:
        - group-ecommerce-shell-runner-build
    when: manual
    only:
        - tags

dockerhub pushing:
    stage: push registry
    variables:
        GIT_STRATEGY: none
    before_script:
        - sudo docker login -u $CI_REGISTRY_USER -p $CI_REGISTRY_PWD
    script:
        - sudo docker push $IMAGE_VERSION
    after_script:
        - sudo docker logout
    tags:
        - group-ecommerce-shell-runner-build
    needs:
        - job: build
    only:
        - tags

deploy:
    stage: deploy
    variables:
        GIT_STRATEGY: none
    before_script:
        - sudo docker login -u $CI_REGISTRY_USER -p $CI_REGISTRY_PWD
    script:
        - sudo docker pull $IMAGE_VERSION
        - sudo su ${USER_PROJECT} -c "
            container_exists=\$(sudo docker ps -a -q -f name=${CI_PROJECT_NAME});
            if [ ! -z \"\$container_exists\" ]; then
                sudo docker rm -f ${CI_PROJECT_NAME};
            fi;
            sudo docker run --name ${CI_PROJECT_NAME} -dp ${FRONTEND_PORT}:80 ${IMAGE_VERSION}"
    after_script:
        - sudo docker logout
    tags:
        - group-ecommerce-shell-runner
    when: manual
    only:
        - tags

k6 testing performance:
    stage: performance test
    variables:
        GIT_STRATEGY: clone
    script:
        - echo "$(pwd)"
        - chmod -R 777 ./performance_test_script/
        - docker run --rm -v $(pwd)/performance_test_script:/performance_test_script loadimpact/k6 run -e RESULTS_PATH=/performance_test_script --summary-export=/performance_test_script/summary_perf.json --duration 10s --vus 5 /performance_test_script/login-test.js
        - mv ./performance_test_script/summary.html $(pwd)/$K6_PERFORMANCE_TEST_REPORT.html
        - sudo chown -R gitlab-runner $(pwd)
    tags:
        - group-ecommerce-shell-runner-build
    only: 
        - tags
    artifacts:
        paths:
            - $K6_PERFORMANCE_TEST_REPORT.html
        expire_in: 1 day
```

Successful run results.

![image.png](/images/11-performancetesting/11.1-k6/image%2020.png?featherlight=false&width=60pc)

The results of the login test.

![image.png](/images/11-performancetesting/11.1-k6/image%2021.png?featherlight=false&width=60pc)
