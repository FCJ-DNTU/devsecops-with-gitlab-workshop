+++
title = "Triển khai Performance Testing với Grafana(k6)"
date = "`r Sys.Date()`" 
weight = 1
chapter = false
pre = "<b>11.1 </b>"
+++

### 1. Cài đặt công cụ

Bạn có tham khảo cách cài đặt ở đây: [Install k6 | Grafana k6 documentation](https://grafana.com/docs/k6/latest/set-up/install-k6/)

Chúng ta sẽ cài đặt k6 ở Build Instance.

```bash
sudo gpg -k
sudo gpg --no-default-keyring --keyring /usr/share/keyrings/k6-archive-keyring.gpg --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys C5AD17C747E3415A3642D57D77C6C491D6AC1D69
echo "deb [signed-by=/usr/share/keyrings/k6-archive-keyring.gpg] https://dl.k6.io/deb stable main" | sudo tee /etc/apt/sources.list.d/k6.list
sudo apt-get update
sudo apt-get install k6
k6 --version
```

![image.png](/images/11-performancetesting/11.1-k6/image.png?featherlight=false&width=60pc)

### 2. Tạo thư mục làm việc và tạo kịch bản

Ở những mục trước chúng ta đang có hai project frontend và backend đang được chạy 

![image.png](/images/11-performancetesting/11.1-k6/image%201.png?featherlight=false&width=60pc)

![image.png](/images/11-performancetesting/11.1-k6/image%202.png?featherlight=false&width=60pc)

Chúng ta sẽ tạo ra một thư mục làm việc và tạo file load-test.js để thực hiện kiểm tra hiệu suất của hai dự án này

```bash
mkdir -p tools/test-k6 && cd tools/test-k6 && vi load-test.js
```

Ví dụ chúng ta sẽ tạo một kịch bản như sau: chúng ta sẽ giả lập số lượng người dùng ảo truy cập đồng thời là 100 truy cập vào website trong 20s, 95% các http hoàn thành yêu cầu dưới 500ms.

Nhập lệnh sau đây:

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
Bạn phải chỉnh sửa ipv4 dự án frontend đang chạy của bạn.
{{% /notice %}}

Chạy k6 để mô phỏng tình huống

```bash
k6 run load-test.js
```

Sau đây là kết quả trả về:

![image.png](/images/11-performancetesting/11.1-k6/image%203.png?featherlight=false&width=60pc)

Giải thích:

**Tổng quan**:

- **checks:** Cho thấy phần trăm các kiểm tra (checks) đã thành công và tổng số kiểm tra đã thực hiện. (`100.00% ✓ 1000` nghĩa là tất cả 1000 kiểm tra đều thành công).
- **data_received:** Tổng dung lượng dữ liệu đã nhận trong quá trình kiểm tra (`963 kB` với tốc độ `95 kB/s`).
- **data_sent:** Tổng dung lượng dữ liệu đã gửi trong quá trình kiểm tra (`83 kB` với tốc độ `8.2 kB/s`).

**Các thông số liên quan đến yêu cầu HTTP**:

- **http_req_blocked:** là thời gian mà yêu cầu HTTP bị chặn trước khi thực sự được gửi đi.
    - `avg=724.51µs`: Thời gian trung bình mà các yêu cầu bị chặn trước khi được gửi.
    - `min=2.17µs`: Thời gian ngắn nhất mà yêu cầu bị chặn.
    - `med=2.91µs`: Thời gian trung bình (thời gian ở giữa nếu sắp xếp các giá trị từ thấp đến cao) của thời gian bị chặn.
    - `max=12.76ms`: Thời gian dài nhất mà yêu cầu bị chặn.
    - `p(90)=2.86ms`: 90% yêu cầu có thời gian bị chặn ít hơn hoặc bằng giá trị này.
    - `p(95)=6.76ms`: 95% yêu cầu có thời gian bị chặn ít hơn hoặc bằng giá trị này.
- **http_req_connecting:** là thời gian mà ứng dụng của bạn dành để thiết lập một kết nối TCP đến máy chủ
    - `avg=713.64µs`: Thời gian trung bình dành cho việc kết nối đến máy chủ.
    - `min=0s`: Thời gian kết nối ngắn nhất.
    - `med=0s`: Thời gian trung bình cho việc kết nối
    - `max=12.72ms`: Thời gian kết nối dài nhất.
    - `p(90)=282.96µs`: 90% yêu cầu có thời gian kết nối ít hơn hoặc bằng giá trị này.
    - `p(95)=6.73ms`: 95% yêu cầu có thời gian kết nối ít hơn hoặc bằng giá trị này.
- **http_req_duration:** Thời gian để hoàn tất một yêu cầu HTTP, bao gồm cả thời gian gửi, nhận, và chờ đợi.
    - `avg=6.23ms`: Thời gian trung bình  cho một yêu cầu.
    - `min=423.68µs`: Thời gian hoàn tất nhanh nhất cho một yêu cầu.
    - `med=6.36ms`: Thời gian trung bình để hoàn tất một yêu cầu.
    - `max=23.84ms`: Thời gian hoàn tất lâu nhất cho một yêu cầu.
    - `p(90)=11.38ms`: 90% yêu cầu hoàn tất trong thời gian này hoặc ít hơn.
    - `p(95)=13.49ms`: 95% yêu cầu hoàn tất trong thời gian này hoặc ít hơn.
- **http_req_failed:** Tỷ lệ yêu cầu thất bại, ở đây là `0.00%`, nghĩa là không có yêu cầu nào bị thất bại.
- **http_req_receiving:** Thời gian dành cho việc nhận phản hồi từ server
    - `avg=79.87µs`: Thời gian trung bình để nhận phản hồi.
    - `min=11.5µs`: Thời gian ngắn nhất để nhận phản hồi.
    - `med=17.99µs`: Thời gian trung bình cho việc nhận phản hồi.
    - `max=10.67ms`: Thời gian dài nhất để nhận phản hồi.
    - `p(90)=55.61µs`: 90% yêu cầu nhận phản hồi trong thời gian này hoặc ít hơn.
    - `p(95)=145.16µs`: 95% yêu cầu nhận phản hồi trong thời gian này hoặc ít hơn.
- **http_req_sending:**
    - `avg=232.81µs`: Thời gian trung bình cần thiết để gửi yêu cầu đến máy chủ.
    - `min=4.75µs`: Thời gian ngắn nhất để gửi yêu cầu.
    - `med=11.63µs`: Thời gian trung bình để gửi yêu cầu.
    - `max=6.6ms`: Thời gian dài nhất để gửi yêu cầu.
    - `p(90)=837.36µs`: 90% yêu cầu được gửi trong thời gian này hoặc ít hơn.
    - `p(95)=1.17ms`: 95% yêu cầu được gửi trong thời gian này hoặc ít hơn.
- **http_req_tls_handshaking:** Thời gian dành cho việc bắt tay TLS, ở đây là `0s`, nghĩa là không có quá trình bắt tay TLS xảy ra bởi vì chúng ta chưa đăng ký chứng chỉ cho http.
- **http_req_waiting:** Thời gian chờ đợi phản hồi từ server sau khi gửi yêu cầu.
    - `avg=5.91ms`: Thời gian trung bình chờ đợi.
    - `min=386.46µs`: Thời gian chờ đợi ngắn nhất.
    - `med=6.09ms`: Thời gian trung bình của thời gian chờ.
    - `max=21.92ms`: Thời gian chờ đợi dài nhất.
    - `p(90)=10.59ms`: 90% yêu cầu chờ đợi trong thời gian này hoặc ít hơn.
    - `p(95)=12.38ms`: 95% yêu cầu chờ đợi trong thời gian này hoặc ít hơn.

**Các thông số khác**:

- **http_reqs:** Tổng số yêu cầu HTTP đã thực hiện, ở đây là `1000` với tốc độ `99.023682/s`.
- **iteration_duration:** là thời gian trung bình để hoàn thành một lần lặp (iteration) của một kịch bản kiểm tra (test script) trong quá trình kiểm tra tải. Một lần lặp bao gồm tất cả các bước và yêu cầu mà kịch bản kiểm tra thực hiện từ đầu đến cuối.
    - `avg=1s`: Thời gian trung bình cho mỗi lần lặp (iteration).
    - `med=1s`: Thời gian trung bình cho mỗi lần lặp.
    - `max=1.03s`: Thời gian dài nhất cho mỗi lần lặp.
    - `p(90)=1.01s`: 90% lần lặp hoàn thành trong thời gian này hoặc ít hơn.
    - `p(95)=1.01s`: 95% lần lặp hoàn thành trong thời gian này hoặc ít hơn.
- **iterations:** Số lần lặp của kiểm tra, cũng là `1000`.
- **vus:** Số lượng người dùng ảo (virtual users), ở đây là `100`.
- **vus_max:** Số lượng người dùng ảo tối đa đã sử dụng, ở đây cũng là `100`.

**Kết luận**:

- **Hiệu suất tổng thể:** Hệ thống xử lý các yêu cầu HTTP một cách nhanh chóng và ổn định với thời gian phản hồi rất thấp. Không có yêu cầu nào bị thất bại, điều này chứng tỏ hệ thống hoạt động tốt dưới tải.
- **Khả năng mở rộng:** Với 100 người dùng ảo và 1000 yêu cầu, hệ thống dường như có khả năng xử lý tải này mà không gặp bất kỳ vấn đề nào đáng kể. Tuy nhiên, để đánh giá khả năng mở rộng tốt hơn, cần thực hiện thêm các bài kiểm tra với tải cao hơn.
- **Thời gian phản hồi và xử lý:** Tất cả các yêu cầu đều được xử lý với thời gian phản hồi rất tốt, cho thấy hệ thống có thể đáp ứng nhanh chóng ngay cả trong môi trường tải.

Chúng ta sẽ tạo ra một file login-test.js để kiểm tra hiệu suất của backend

```bash
vi login-test.js 
```

Nhập câu lệnh sau đây:

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

chạy câu lệnh k6

```bash
k6 run -u 100 -d 20s login-test.js
```
{{% notice info %}}
Nếu bạn chạy fail 100% bạn có thể xem lại Build Instance có kết nối được đến backend trong Deploy Instance không.
![image.png](/images/11-performancetesting/11.1-k6/image%204.png?featherlight=false&width=60pc)
{{% /notice %}}

Kết quả:

![image.png](/images/11-performancetesting/11.1-k6/image%205.png?featherlight=false&width=60pc)

Bây giờ chúng ta sẽ kiểm tra theo kịch bản số lượng user sẽ thay đổi bất thường 

```bash
vi s**pike-test.js**
```

Nhập dòng lệnh sau:

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

Chạy mô phỏng:

```bash
k6 run spike-test.js
```

Xem kết quả, một phút đầu tiên sẽ có 50 user, 10 giây tiếp theo sẽ tăng đột biến lên 500 user, 40 giây tiếp theo sẽ giảm xuống còn 50 user và 20 giây cuối giảm còn 10 người dùng.

![image.png](/images/11-performancetesting/11.1-k6/image%206.png?featherlight=false&width=60pc)

### 3. Triển khai Performmance Testing trong frontend

Đầu tiên chúng ta sẽ tạo nhánh mới pipeline-fe-11.1-k6 từ nhánh main

![image.png](/images/11-performancetesting/11.1-k6/image%207.png?featherlight=false&width=60pc)

Chúng ta sẽ tạo một thư mục mới 

![image.png](/images/11-performancetesting/11.1-k6/image%208.png?featherlight=false&width=60pc)

Đặt tên thư mục là performance_test_script

![image.png](/images/11-performancetesting/11.1-k6/image%209.png?featherlight=false&width=60pc)

Nhấn vào thư mục mới vừa tạo

![image.png](/images/11-performancetesting/11.1-k6/image%2010.png?featherlight=false&width=60pc)

tạo file mới trong thư mục vừa tạo

![image.png](/images/11-performancetesting/11.1-k6/image%2011.png?featherlight=false&width=60pc)

Đặt tên file là smoke-test.js và nhập dòng lệnh sau đây:

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

Chúng ta sẽ mô phỏng một kịch bản kiểm tra với số lượng người dùng ít gọi là smoke-test để kiểm tra tính khả thi của quá trình deploy. Trong giai đoạn chưa lên production thì các tài nguyên của server sẽ bị hạn chế nên sử dụng smoke-test trong CI/CD là phù hợp.

Vào pipeline edittor để chỉnh sửa

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

Chúng ta tạo tags và xem kết quả của pipeline 

![image.png](/images/11-performancetesting/11.1-k6/image%2013.png?featherlight=false&width=60pc)

Đây là kết quả của file .csv

![image.png](/images/11-performancetesting/11.1-k6/image%2014.png?featherlight=false&width=60pc)

Bây giờ chúng ta sẽ chuyển file csv sang html. Chúng ta thực hiện điều này trong branch mới tên là **pipeline-fe-11.1-k6-v2** từ **pipeline-fe-11.1-k6**

Bạn có thể tham khảo thêm tại đây: [benc-uk/k6-reporter: Output K6 test run results as formatted & easy to read HTML reports (github.com)](https://github.com/benc-uk/k6-reporter)

![image.png](/images/11-performancetesting/11.1-k6/image%2015.png?featherlight=false&width=60pc)

Sửa file performance_test_script/smoke-test.js

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

Sửa file gitlab-ci.yml

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

Kết quả chạy thành công

![image.png](/images/11-performancetesting/11.1-k6/image%2017.png?featherlight=false&width=60pc)

Đối với file html thì chúng ta thấy chúng sinh động và trực quan hơn 

![image.png](/images/11-performancetesting/11.1-k6/image%2018.png?featherlight=false&width=60pc)

### 4. Triển khai Performmance Testing trong backend

Đầu tiên chúng ta sẽ tạo nhánh mới pipeline-be-11.1-k6 từ nhánh main

Chúng ta sẽ tạo một thư mục mới là performance_test_script, tạo file mới trong thư mục vừa tạo là login-test.js

Nhập dòng lệnh sau đây:

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

Vào pipeline edittor để chỉnh sửa

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

Kết quả chạy thành công

![image.png](/images/11-performancetesting/11.1-k6/image%2020.png?featherlight=false&width=60pc)

Kết quả trả về của login test

![image.png](/images/11-performancetesting/11.1-k6/image%2021.png?featherlight=false&width=60pc)