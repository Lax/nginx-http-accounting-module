# About

Monitor the incoming and outgoing traffic metrics in realtime for NGINX stream subsystem.

Realtime traffic and status code monitor solution for NGINX, need less memory and cpu than realtime log analyzing.
Useful for http traffic accounting based on NGINX config logic ( by-location or by-server or by-user-defined-variable ).

# Installation
[![FOSSA Status](https://app.fossa.io/api/projects/git%2Bgithub.com%2FLax%2Fnginx-http-accounting-module.svg?type=shield)](https://app.fossa.io/projects/git%2Bgithub.com%2FLax%2Fnginx-http-accounting-module?ref=badge_shield)


Configure `nginx-http-accounting-module` as NGINX module with ```--add-module``` when build NGINX.

    cd /path/to/nginx-src/
    git clone https://github.com/Lax/nginx-http-accounting-module.git
    ./configure --add-module=nginx-http-accounting-module
    make && make install

# Configuration

Edit your nginx.conf.

Example:

```nginx
http{
    # turn on accounting function
    http_accounting  on;
    ...
    server {
        server_name example.com;

        # set accounting_id based on server, use variable
        http_accounting_id  $http_host;
        ...
        location / {
            # set accounting_id based on location
            http_accounting_id  accounting_id_str;
            ...
        }

        location /api {
            # for pc
            http_accounting_id  accounting_id_pc;
            # for mobile
            if ($http_user_agent ~* '(Android|webOS|iPhone|iPod|BlackBerry)') {
                http_accounting_id  accounting_id_mobile;
            }
            ...
        }
    }

}
```

# Directives

http_accounting
--------------------
**syntax:** *http_accounting on | off*

**default:** *http_accounting off*

**context:** *http*

http_accounting_log
--------------------
**syntax:** *http_accounting_log \</path/to/log/file>*

**default:** *-*

**context:** *http*

http_accounting_id
--------------------
**syntax:** *http_accounting_id \<accounting_id>*

**default:** *http_accounting_id default*

**context:** *server, location, if in location*

Specifies current request belongs to which accounting_id.

This directive was first introduced in the v0.1 release, and can use variable in v1.0 and above.

http_accounting_interval
------------------------
**syntax:** *http_accounting_interval \<seconds>*

**default:** *http_accounting_interval 60*

**context:** *http*

Specifies the reporting interval.  Defaults to 60 seconds.

http_accounting_perturb
------------------------
**syntax:** *http_accounting_perturb on | off*

**default:** *http_accounting_perturb off*

**context:** *http*

Randomly staggers the reporting interval by 20% from the usual time.

# Usage

This module writes statistics to syslog. You should edit your syslog configuration.

For sample configuration / utils, see: [Lax/ngx_http_accounting_module-utils](http://github.com/Lax/ngx_http_accounting_module-utils)

## Sample syslog output

    Apr  8 11:19:46 localhost NgxAccounting: pid:8555|from:1428463159|to:1428463186|accounting_id:default|requests:10|bytes_in:1400|bytes_out:223062|latency_ms:1873|upstream_latency_ms:1873|200:9|302:1

The output contains a list of k/v for the accounting metrics, in the sequence of:

|  key             |  meaning |
|------------------|----------|
| `pid`           | pid of nginx worker process |
| `from` / `to`   | metric was collected between these timestamps |
| `accounting_id` | identify for the accounting unit, you name it with `http_accounting_id` directive |
| `requests`      | count of total requests processed |
| `bytes_in`      | total bytes receiverd by the server |
| `bytes_out`     | total bytes send out by the server |
| `latency_ms`    | a sum of `$request_time`, in `millisecond` |
| `upstream_latency_ms`  | a sum of `$upstream_response_time`, in `millisecond` |
| `200` / `302` / `400` / `404` / `500` ... | count of requests which response is with http code `200`/`302`/`400`/`404`/`500`, etc |

# Branches

* master : new feathers
* v2-freeze-20110526 : works with nginx version(0.7.xx, 0.8.xx), nginx 0.9 is not tested. didn't work with nginx above 1.0.x.

# Contributing

1. Fork it ( https://github.com/Lax/nginx-http-accounting-module/fork )
2. Create your feature branch (`git checkout -b my-new-feature`)
3. Commit your changes (`git commit -am 'Add some feature'`)
4. Push to the branch (`git push origin my-new-feature`)
5. Create a new Pull Request

[Known issues](https://github.com/Lax/nginx-http-accounting-module/issues?q=)

# Author

Liu Lantao [Github@Lax](https://github.com/Lax)

[Contributors](https://github.com/Lax/nginx-http-accounting-module/graphs/contributors)

# License

[GPLv3](LICENSE)


[![FOSSA Status](https://app.fossa.io/api/projects/git%2Bgithub.com%2FLax%2Fnginx-http-accounting-module.svg?type=large)](https://app.fossa.io/projects/git%2Bgithub.com%2FLax%2Fnginx-http-accounting-module?ref=badge_large)
