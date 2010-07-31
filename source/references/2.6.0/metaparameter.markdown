% Metaparameter Reference
% 
% 

**This page is autogenerated; any changes will get overwritten**
*(last generated on Mon Jul 19 21:53:39 -0700 2010)*


* * * * *

Creates an alias for the object. Puppet uses this internally when
you provide a symbolic name:

    file { sshdconfig:
      path => $operatingsystem ? {
        solaris => "/usr/local/etc/ssh/sshd_config",
        default => "/etc/ssh/sshd_config"
      },
      source => "..."
    }
    
    service { sshd:
      subscribe => file[sshdconfig]
    }

When you use this feature, the parser sets `sshdconfig` as the
name, and the library sets that as an alias for the file so the
dependency lookup for `sshd` works. You can use this parameter
yourself, but note that only the library can use these aliases; for
instance, the following code will not work:

    file { "/etc/ssh/sshd_config":
      owner => root,
      group => root,
      alias => sshdconfig
    }
    
    file { sshdconfig:
      mode => 644
    }

There's no way here for the Puppet parser to know that these two
stanzas should be affecting the same file.

See the language tutorial for more information.

# audit

Audit specified attributes of resources over time, and report if
any have changed. This attribute can be used to track changes to
any resource over time, and can provide an audit trail of every
change that happens on any given machine.

Note that you cannot both audit and manage an attribute - managing
it guarantees the value, and any changes already get logged.

# before

This parameter is the opposite of **require** -- it guarantees that
the specified object is applied later than the specifying object:

    file { "/var/nagios/configuration":
      source  => "...",
      recurse => true,
      before => Exec["nagios-rebuid"]
    }
    
    exec { "nagios-rebuild":
      command => "/usr/bin/make",
      cwd => "/var/nagios/configuration"
    }

This will make sure all of the files are up to date before the make
command is run.

# check

Audit specified attributes of resources over time, and report if
any have changed. This parameter has been deprecated in favor of
'audit'.

# loglevel

Sets the level that information will be logged. The log levels have
the biggest impact when logs are sent to syslog (which is currently
the default). Valid values are `debug`, `info`, `notice`,
`warning`, `err`, `alert`, `emerg`, `crit`, `verbose`.

# noop

Boolean flag indicating whether work should actually be done. Valid
values are `true`, `false`.

# notify

This parameter is the opposite of **subscribe** -- it sends events
to the specified object:

    file { "/etc/sshd_config":
      source => "....",
      notify => Service[sshd]
    }
    
    service { sshd:
      ensure => running
    }

This will restart the sshd service if the sshd config file
changes.

# require

One or more objects that this object depends on. This is used
purely for guaranteeing that changes to required objects happen
before the dependent object. For instance:

    # Create the destination directory before you copy things down
    file { "/usr/local/scripts":
      ensure => directory
    }
    
    file { "/usr/local/scripts/myscript":
      source => "puppet://server/module/myscript",
      mode => 755,
      require => File["/usr/local/scripts"]
    }

Multiple dependencies can be specified by providing a
comma-seperated list of resources, enclosed in square brackets:

    require => [ File["/usr/local"], File["/usr/local/scripts"] ]

Note that Puppet will autorequire everything that it can, and there
are hooks in place so that it's easy for resources to add new ways
to autorequire objects, so if you think Puppet could be smarter
here, let us know.

In fact, the above code was redundant -- Puppet will autorequire
any parent directories that are being managed; it will
automatically realize that the parent directory should be created
before the script is pulled down.

Currently, exec resources will autorequire their CWD (if it is
specified) plus any fully qualified paths that appear in the
command. For instance, if you had an `exec` command that ran the
`myscript` mentioned above, the above code that pulls the file down
would be automatically listed as a requirement to the `exec` code,
so that you would always be running againts the most recent
version.

# schedule

On what schedule the object should be managed. You must create a
schedule object, and then reference the name of that object to use
that for your schedule:

    schedule { daily:
      period => daily,
      range => "2-4"
    }
    
    exec { "/usr/bin/apt-get update":
      schedule => daily
    }

The creation of the schedule object does not need to appear in the
configuration before objects that use it.

# stage

Which run stage a given resource should reside in. This just
creates a dependency on or from the named milestone. For instance,
saying that this is in the 'bootstrap' stage creates a dependency
on the 'bootstrap' milestone.

By default, all classes get directly added to the 'main' stage. You
can create new stages as resources:

> stage { [pre, post]: }

To order stages, use standard relationships:

> stage { pre: before =\> Stage[main] }

Or use the new relationship syntax:

> Stage[pre] -\> Stage[main] -\> Stage[post]

Then use the new class parameters to specify a stage:

> class { foo: stage =\> pre }

Stages can only be set on classes, not individual resources. This
will fail:

    file { '/foo': stage => pre, ensure => file }

# subscribe

One or more objects that this object depends on. Changes in the
subscribed to objects result in the dependent objects being
refreshed (e.g., a service will get restarted). For instance:

    class nagios {
      file { "/etc/nagios/nagios.conf":
        source => "puppet://server/module/nagios.conf",
        alias => nagconf # just to make things easier for me
      }
      service { nagios:
        ensure => running,
        subscribe => File[nagconf]
      }
    }

Currently the `exec`, `mount` and `service` type support
refreshing.

# tag

Add the specified tags to the associated resource. While all
resources are automatically tagged with as much information as
possible (e.g., each class and definition containing the resource),
it can be useful to add your own tags to a given resource.

Tags are currently useful for things like applying a subset of a
host's configuration:

    puppet agent --test --tags mytag

This way, when you're testing a configuration you can run just the
portion you're testing.


* * * * *

*This page autogenerated on Mon Jul 19 21:53:41 -0700 2010*


