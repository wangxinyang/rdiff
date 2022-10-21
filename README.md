# rdiff
The rdiff have two tools to resolve that diff HTTP response content and send HTTP request with yaml file or cli command.



- rdiff

> Command

```
rdiff add -h

Usage: rdiff add [OPTIONS] --profile <PROFILE>

Options:
  -c, --config <CONFIG>          Config file path
  -p, --profile <PROFILE>        Profile in file
  -e, --extra-args <EXTRA_ARGS>  for query params, use `-e key=value` for headers, use `-e %key=value` for body,, use `-e @key=value`
  -h, --help                     Print help information
  

```

```
rdiff parse -h

Usage: rdiff parse

Options:
  -h, --help  Print help information
```



> YAML

```request.yml
---
todo:
  request1:
    url: http://127.0.0.1:8100
    params:
      a: 100
  request2:
    url: http://127.0.0.1:8100
    params:
      c: 200
  response:
    skip_headers:
      - report
rust:
  request1:
    method: GET
    url: http://127.0.0.1:8100/req_r1
    headers:
      user-agent: Aloha
    params:
      hello: world
  request2:
    method: GET
    url: http://127.0.0.1:8100/req_r2
    params: {}
  response:
    skip_headers:
      - set-cookie
      - date



```



> Add command

Send HTTP request by parsing command line parameters or YAML files and compare the response result and display the comparison result.



**rdiff add -c yaml/diff.yml -p rust -e a=100**

```
1   1    | HTTP/1.1 200 OK
2   2    | content-type: "application/json; charset=utf-8"content-length: "58"
3   3    | {
4        |-  "status": "Hello, this is req1 response by rust command1"
    4    |+  "status": "Hello, this is req2 response by rust command2"
5   5    | }
```
<img width="1395" alt="image" src="https://s1.imagehub.cc/images/2022/10/19/1325a811388db5d27.png">



> Parse command

With the questions step by step to generate content  by yaml format. Convenient to paste the generate content to yaml file to send HTTP Request.



**rdiff parse **

```
✔ Request1 · http://127.0.0.1:8100/req_r1
✔ Request2 · http://127.0.0.1:8100/req_r2
✔ Profile name · rust1
? Select headers to skip ›
✔ content-type
✔ date
✔ content-length
```

![parse](https://s1.imagehub.cc/images/2022/10/21/24641f057cf0a7d48.png)

> Error

The error msg with color or not by tty

```
if params is not a object

rust:
  request1:
    method: GET
    url: http://127.0.0.1:8100/req_r1
    headers:
      user-agent: Aloha
    params: 1111
  request2:
    method: GET
    url: http://127.0.0.1:8100/req_r2
    params: {}
  response:
    skip_headers:
      - set-cookie
      - date
```

```
rdiff add -p rust -c yaml/diff.yml -e a=100 -e @b=2 -e %c=3 -e m=10
```

![add_error](https://s1.imagehub.cc/images/2022/10/21/348f45f9e0aba29a0.png)

```
rdiff add -p rust -c yaml/diff.yml -e a=100 -e @b=2 -e %c=3 -e m=10 2> /tmp/result

cat /tmp/result
```

![add_error_text](https://s1.imagehub.cc/images/2022/10/21/42669eff0a4e9712b.png)





- rreq

Send HTTP request by parsing command line parameters or YAML files.



> Command

```
rreq add -h

Usage: rreq add [OPTIONS] --profile <PROFILE>

Options:
  -c, --config <CONFIG>          Config file path
  -p, --profile <PROFILE>        Profile in file
  -e, --extra-args <EXTRA_ARGS>  for query params, use `-e key=value` for headers, use `-e %key=value` for body,, use `-e @key=value`
  -h, --help                     Print help information
```

```
rdiff parse -h

Usage: rreq parse

Options:
  -h, --help  Print help information
```



> YAML

```request.yml
---
todo:
  url: http://127.0.0.1:8100
  params:
    a: 100
rust:
  method: GET
  url: http://127.0.0.1:8100/req_r1
  headers:
    user-agent: Aloha
  params:
    hello: world
rust2:
  url: http://127.0.0.1:8100/req_r1
  method: GET
  params:
    a: 1
    b: 2
    c[0]: 3
    c[1]: 4
```



> Add command

Send HTTP request by parsing command line parameters or YAML files



**rreq add -c yaml/request.yml -p rust2**

```
Url: http://127.0.0.1:8100/req_r1?a=1&b=2&c%5B0%5D=3&c%5B1%5D=4

HTTP/1.1 200 OK
content-type: "application/json; charset=utf-8"
date: "Fri, 21 Oct 2022 06:12:03 GMT"
content-length: "58"

{
  "status": "Hello, this is req1 response by rust command1"
}
```

![req_add](https://s1.imagehub.cc/images/2022/10/21/50cb0fcef47208647.png)



> Parse command

With the questions step by step to generate content  by yaml format. Convenient to paste the generate content to yaml file to send HTTP Request.



**rreq parse **

```
✔ Url · http://127.0.0.1:8100/req_r1
✔ Profile name · rust2
```

![req_parse](https://s1.imagehub.cc/images/2022/10/21/6bc69ab011bbe6138.png)



> Error

The error msg with color or not by tty

```
if params is not a object

todo:
  url: http://127.0.0.1:8100
  params:
    a: 100
rust:
  method: GET
  url: http://127.0.0.1:8100/req_r1
  headers:
    user-agent: Aloha
  params: test
rust2:
  url: http://127.0.0.1:8100/req_r1
  method: GET
  params:
    a: 1
    b: 2
    c[0]: 3
    c[1]: 4
```

```
rreq add -c yaml/request.yml -p rust2
```

![add_error](https://s1.imagehub.cc/images/2022/10/21/793e44ef0fdb00ce9.png)

```
rdiff add -c yaml/request.yml -p rust2 2> /tmp/result

cat /tmp/result
```

![add_error_text](https://s1.imagehub.cc/images/2022/10/21/8b826e28f08aa50db.png)

