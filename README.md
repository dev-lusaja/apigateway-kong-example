APIgateway example with Kong
============================

Requirements for example
------------------------

* Docker version 17.05.0-ce, build 89658be
* docker-compose version 1.11.1, build 7c5d5e4

Commands
--------


* Install the project
~~~~
$ make install
~~~~

**note:** this command run the kong migrations

**note:** the kong app wait 60 sec for cassandra db service

* Uninstall the project
~~~~
$ make uninstall
~~~~

* Start stopped containers
~~~~
$ make start
~~~~
**note:** the kong app wait 60 sec for cassandra db service

* Stop all containers
~~~~
$ make stop
~~~~

* Registry a new application without oauth
~~~~
$ make add_resource NAME=app_a HOST=app_a.dev UPSTREAM=http://app_a/
$ make add_resource NAME=app_b HOST=app_b.dev UPSTREAM=http://app_b/
~~~~
**note:** "app_a & app_b" is the service name in docker-compose for the applications

~~~~
// response

HTTP/1.1 201 Created
Date: Tue, 21 Nov 2017 17:37:24 GMT
Content-Type: application/json; charset=utf-8
Transfer-Encoding: chunked
Connection: keep-alive
Access-Control-Allow-Origin: *
Server: kong/0.11.1

{"created_at":1511285843994,"strip_uri":true,"id":"8bbcbc3a-877a-49da-80e0-28949c2d043c","hosts":["app_a.dev"],"name":"app_a","http_if_terminated":false,"https_only":false,"retries":5,"upstream_url":"http:\/\/app_a\/","upstream_send_timeout":60000,"upstream_read_timeout":60000,"upstream_connect_timeout":60000,"preserve_host":false}
~~~~

* Test the kong forward using host header
~~~~
$ make test HOST=app_a.dev

// response:

HTTP/1.1 200 OK
Content-Type: text/html; charset=UTF-8
Transfer-Encoding: chunked
Connection: keep-alive
Server: nginx/1.8.0
Date: Tue, 21 Nov 2017 17:37:50 GMT
X-Powered-By: PHP/5.6.14
X-Kong-Upstream-Latency: 256
X-Kong-Proxy-Latency: 155
Via: kong/0.11.1

<html>
<head>
	<title>Hello world!</title>
	<link href='http://fonts.googleapis.com/css?family=Open+Sans:400,700' rel='stylesheet' type='text/css'>
	<style>
	body {
		background-color: white;
		text-align: center;
		padding: 50px;
		font-family: "Open Sans","Helvetica Neue",Helvetica,Arial,sans-serif;
	}

	#logo {
		margin-bottom: 40px;
	}
	</style>
</head>
<body>
	<img id="logo" src="logo.png" />
	<h1>Hello world!</h1>
	<h3>My hostname is 5017d131170f</h3>	</body>
</html>

~~~~

* Enable auth plugin
~~~~
$ make enable_auth API_NAME=app_a

// response:

{"created_at":1511544935876,"config":{"key_in_body":false,"run_on_preflight":true,"anonymous":"","hide_credentials":false,"key_names":["apikey"]},"id":"85178a0e-0ab5-4283-8247-a7359dc46879","enabled":true,"name":"key-auth","api_id":"8bbcbc3a-877a-49da-80e0-28949c2d043c"}
~~~~

**note:** an API with the active authentication plug-in will display this message in the response for the "make test" command
~~~~
$ make test HOST=app_a.dev

// response:

{"message":"No API key found in request"}
~~~~

* Create a customer for authenticate API
~~~~
$ make create_customer USER_NAME=dev API_KEY=dev

// responses:

{"created_at":1511544434570,"username":"dev","id":"581228fe-2343-4751-99ad-f917efae7bee"}
{"id":"2e5823b8-916e-4821-bff0-d61c48d65c65","created_at":1511544434586,"key":"dev","consumer_id":"581228fe-2343-4751-99ad-f917efae7bee"}
~~~~

* Test with api key
~~~~
$ make test_auth HOST=app_a.dev API_KEY=dev

// response:

HTTP/1.1 200 OK
Content-Type: text/html; charset=UTF-8
Transfer-Encoding: chunked
Connection: keep-alive
Server: nginx/1.8.0
Date: Fri, 24 Nov 2017 20:07:58 GMT
X-Powered-By: PHP/5.6.14
X-Kong-Upstream-Latency: 270
X-Kong-Proxy-Latency: 208
Via: kong/0.11.1

<html>
<head>
	<title>Hello world!</title>
	<link href='http://fonts.googleapis.com/css?family=Open+Sans:400,700' rel='stylesheet' type='text/css'>
	<style>
	body {
		background-color: white;
		text-align: center;
		padding: 50px;
		font-family: "Open Sans","Helvetica Neue",Helvetica,Arial,sans-serif;
	}

	#logo {
		margin-bottom: 40px;
	}
	</style>
</head>
<body>
	<img id="logo" src="logo.png" />
	<h1>Hello world!</h1>
	<h3>My hostname is 4bcfc5ecb1c4</h3>	</body>
</html>
~~~~

* For more commands
~~~~
$ make help
~~~~
