# apache-scalp
Scalp! is a log analyzer for the Apache web server that aims to look for security problems. The main idea is to look through huge log files and extract the possible attacks that have been sent through HTTP/GET (By default, Apache does not log the HTTP/POST variable).

## How it works
Scalp is basically using the regular expression from the [PHP-IDS](https://github.com/PHPIDS/PHPIDS) project and matches the lines from the Apache access log file. These regexp has been chosen because of their quality and the top activity of the team maintaining that project.

You will then need this file [default_filter.xml](https://raw.github.com/PHPIDS/PHPIDS/master/lib/IDS/default_filter.xml) or let Scalp! download it for you.

### How to use
Scalp has a couple of options that may be useful in order to save time when scalping a huge log file or in order to perform a full examination; the default options are almost okay for log files of hundreds of MB.

Edit the config file `config.py.example` with your email server data and save as `config.py`

Current options:

- exhaustive: Won't stop at the first pattern matched, but will test all the patterns
- tough: Will decode a part of potential attacks (this is done to use better the regexp from PHP-IDS in order to decrease the false-negative rate)
- period: Specify a time-frame to look at, all the rest will be ignored
- sample: Does a random sampling of the log lines in order to look at a certain percentage, this is useful when the user doesn't want to do a full scan of all the log, but just ping it to see if there is some problem...
- attack: Specify what classes of vulnerabilities the tool will look at (eg, look only for XSS, SQL Injection, etc.)

Example of utilization:

```
./scalp.py -l /var/log/httpd_log -f ./default_filter.xml -o ./scalp-output --html
```

## Help

```
./scalp.py --help
Scalp the apache log! by Romain Gaucher - http://rgaucher.info
usage:  ./scalp.py [--log|-l log_file] [--filters|-f filter_file] 
                   [--period time-frame] [OPTIONS] [--attack a1,a2,..,an]
                   [--sample|-s 4.2]
   --log       |-l:  the apache log file './access_log' by default
   --filters   |-f:  the filter file     './default_filter.xml' by default
   --exhaustive|-e:  will report all type of attacks detected and not stop
                     at the first found
   --tough     |-u:  try to decode the potential attack vectors (may increase
                     the examination time)
   --period    |-p:  the period must be specified in the same format as in
                     the Apache logs using * as wild-card
                     ex: 04/Apr/2008:15:45;*/Mai/2008
                     if not specified at the end, the max or min are taken
   --html      |-h:  generate an HTML output
   --email     |-m:  generate HTML output and send via email
   --xml       |-x:  generate an XML output
   --text      |-t:  generate a simple text output (default)
   --except    |-c:  generate a file that contains the non examined logs due to the
                     main regular expression; ill-formed Apache log etc.
   --attack    |-a:  specify the list of attacks to look for
                     list: xss, sqli, csrf, dos, dt, spam, id, ref, lfi
                     the list of attacks should not contains spaces and comma separated
                     ex: xss,sqli,lfi,ref
   --ignore-ip|-i:   specify the list of IP Addresses to look exclude")
                     the list of IP Addresses should be comma separated and not contain spaces")
                     This option can be used in conjunction with --ignore-ip")
   --ignore-subnet|-n:  specify the list of Subnets to look exclude")
                     the list of Subnets should be comma separated and not contain spaces")
                     This option can be used in conjunction with --ignore-subnet")
   --output    |-o:  specifying the output directory; by default, scalp will try to write
                     in the same directory as the log file
   --sample    |-s:  use a random sample of the lines, the number (float in [0,100]) is
                     the percentage, ex: --sample 0.1 for 1/1000
```

## Features
Beside the speed of this software, a couple of points are important:

- output in many formats (TEXT, XML, HTML)
- options in order to let the user do a pre-selection (mainly with a range of dates)
- configuration of the format of the Apache log may come later...

Run or setup the following as a cron task:

    python ./scalp.py -l /var/log/httpd_log -f ./default_filter.xml -o ./scalp-output --email
