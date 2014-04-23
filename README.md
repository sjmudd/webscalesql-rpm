# WebScaleSQL RPM file builder

See: http://blog.wl0.org/2014/04/webscalesql-rpms-for-centos-6/
which describes the build process I started with to build
WebScaleSQL rpms for CentOS 6.

The build script on this page attempts to simplify the procedure
so that rpms can be built when any changes get applied to the
webscalesql-5.6 git repo which is assumed to be on the same server.

**Build Procedure**

- Get my webscalesql-rpm.git repo:
```
$ git clone https://github.com/sjmudd/webscalesql-rpm.git
```
- Install Build requirements
WebScaleSQL requires a new GCC so one of the following toolsets
needs to be installed: devtoolset-1.1 (GCC 4.7) or 
devtoolset-2 (GCC 4.8).

To simplify the devtoolset installation simply run:

```
$ ./install-build-rquirements
```

and this will install this and a few other required packages needed to
build the rpms.

- Build the rpm
```
$ sh build [/path/to/webscalesql.git/repo]
```
This will take a while and leave a log file in build.log.<timestamp>.gz

The path will be remembered so you only need to add that once. Subsequent
runs can just call build on its own.

- If you want to build a new rpm after pulling updates on the webscalesql repo
just run build again. It should patch the spec file and run the build with the
new version.  Note: the generated version is based on the time of the last
git commit to the webscalesql-5.6.git repo (timezone ignored though it should
not be).  I also need to modify the package naming to include the MySQL
base version and the number of commits that have been applied. See
Steaphan Greene's comments on 11th April 2014 on https://www.facebook.com/groups/webscalesql/?fref=ts

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
