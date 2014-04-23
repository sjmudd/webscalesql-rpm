# WebScaleSQL RPM file builder

See: http://blog.wl0.org/2014/04/webscalesql-rpms-for-centos-6/
which describes the build process I started with to build
WebScaleSQL rpms for CentOS 6.

The build script on this page attempts to simplify the procedure
so that rpms can be built when any changes get applied to the
webscalesql-5.6 git repo which is assumed to be on the same server.

**Build Procedure**

- Get my WebScaleSQL build scripts:
```
$ git clone https://github.com/sjmudd/webscalesql-rpm.git
```
- Get the WebScaleSQL source:
```
$ cd /some/where/else
$ git clone https://github.com/webscalesql/webscalesql-5.6.git
```
- Install the build requirements  
WebScaleSQL requires a newer GCC than that provided on CentOS 6,
so one of the following toolsets needs to be installed: devtoolset-1.1
(GCC 4.7) or devtoolset-2 (GCC 4.8).  By default the required packages
in devtoolset-2 are installed.

To simplify the devtoolset installation simply run:

```
$ ./install-build-rquirements
```

and this will install this and a few other required packages needed to
build the rpms. If you really want to install devtoolset-1.1 then do
the following:

```
$ ./install-build-rquirements 1.1
```

- Build the rpm
```
$ sh build [/path/to/local/webscalesql.git/repo]
```
This will take a while and leave a log file in `build.log.<timestamp>.gz`.

The path will be remembered so you only need to add that once. Subsequent
runs can just call `build` on its own.

- If you want to build a new rpm after pulling updates on the webscalesql repo
just run `build` again. It will patch `webscalesql.spec` and run the build with the
new version.
- Package naming. I have now modified the package build procedure to version the
package name as suggested by Steaphan Greene's in a comment on 11th April 2014.
See: https://www.facebook.com/groups/webscalesql/?fref=ts.
Given `rpm` does not like to have hyphens in the version number I have
replaced this with a period, so for example the last rpm I've built now
identifies itself as being version 5.6.17.68.  There may be a one-off
version mismatch in the last digit, but I haven't had time to double
check. I'm sure someone will correct this if this is wrong.

- performance_schema: This build currently does not include
performance_schema (default build behaviour). If you have
performance_schema tables from a previous MySQL-server install, the table
names will be visible, but if you try to select from them you'll get an
error like this:
```
root@myserver [performance_schema]> select * from users;
ERROR 1286 (42000): Unknown storage engine 'PERFORMANCE_SCHEMA'
```
I will try to see if it is possible to build with performance_schema
enabled, or make this configurable.

- NOTE on RPMS for CentOS 5
I had a quick go to build webscalesql rpms for CentOS 5 and found a
couple of issues. If anyone wants to provide me patches to make this
work I'll happily incorporate them.

Feedback welcome to Simon J Mudd <sjmudd@pobox.com>.
