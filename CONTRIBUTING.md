# Contributing to check_pgbouncer

## Adding a new service

### Get check_pgbouncer know about your new service

At the beginning of the check_pgbouncer, the %services hash describes every
available services.

The hash is defined this way :
```
my %services = (
    # 'service_name' => {
    #    'sub'     => sub reference to call to run this service
    #    'desc'    => 'a desctiption of the service'
    # }

...
    'example' => {
        'sub'  => \&check_example,
        'desc' => 'Check number of connections (example service).'
    }
```

First, add the service_name and values for the sub and desc entries.

That is enough to declare a new service. You can now see your new service
listed when you call check_pgactivity with the --list argument.

### Implement your service

Now, define a new check_servicename function before the mark "End of SERVICE
section in pod doc".

To know about arguments provided to the service support function, see the %args
hash.

Get some inspiration from other service functions to see how to handle an new
one, for example check_is_paused is a good starter as it is really simple. Here
are however some guidelines.

Your service has to identify itself with a variable $me. This variable will be
used when calling the output functions. For example :
```
sub check_example {
    my $me       = 'POSTGRES_EXAMPLE';
```

Several other variables are defined :

* `@rs`: array to store the result set of the monitoring query
* `@perfdata`: array to store the returned perdata
* `@msg`: array to store the returned messages
* `@hosts`: array to know the host(s) to query
* `%args`: hash containing service arguments

Consider using these variables names as a convention.

Also populate your own %args hash from the first argument :

```
    my %args     = %{ $_[0] };
```

Now the interesting stuff comes. You can declare a simple query to monitor
something in your pgBouncer server. Here we declare a query that may work with
any pgBouncer version, for example :

```
my $query  = qq{ SHOW state };
```

If you must provide multiple queries for multiple pgBouncer versions, please
refer to the section "Supporting multiple pgBouncer versions".

If your service has to react according some user-thresholds, verify that the
user has given the thresholds as arguments :

```
defined $args{'warning'} and defined $args{'critical'}
```

Use pod2usage to output the error message.

It is recommended to validate the format of the threshold using a regexp :

```
$args{'warning'}  =~ m/^([0-9.]+)/
```

The parse_hosts function will populate the @hosts array :

```
@hosts = @{ parse_hosts %args };
```

Query the database using the query function :

```
@rs = @{ query ( $hosts[0], $query ) };
```

In our example, we can directly get the result :

```
$is_paused = $rs[0][0];
```

Populate the @perfdata array with the resulting metrics :

```
push @perfdata => [ "connections_number", $num_connections, undef ];
```

You must provide some mandatory data in the @perfdata array :

* perfdata name
* the data itself, in numerical form
* the unit used:'B' for bytes, 's' for secondes or undef for raw numbers

The following data are optional :

* warning threshold
* critical threshold
* minimum value
* maximum value

Your service can return "ok" by calling the function of the same name :

```
return ok( $me, \@msg, \@perfdata );
```

check_pgbouncer provides 4 functions for the 4 given A Nagios-compatible service
states :

* ok
* warning
* critical
* unknown

### Document your new service

check_pgbouncer's documentation is handled in its source file, in POD format, as
Plain Old Documentation format. Refer to the perlpod documentation for further
informations.

See also the releasing.md file to see how to regenerate the documentation.

### Test your new service

Test your service in several conditions, verify that it returns a OK, a warning
or critical alert by simulating each conditions.

Test your service upon several pgBouncer versions. Verify that the service
returns an error for unsupported versions, and that every other versions work
well.

### Submit your patch

The best way to submit your patch is to send a pull request in github.

## Debugging check_pgbouncer

Use the --debug option to enable the debug output.

Use dprint() function to output some specific debugging messages to the
developer or the user.

