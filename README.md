# WebScaleSQL RPM file builder

See: http://blog.wl0.org/2014/04/webscalesql-rpms-for-centos-6/
which describes the build process I started with to build
WebScaleSQL rpms for CentOS 6.

The build script on this page attempts to simplify the procedure
so that rpms can be built when any changes get applied to the
webscalesql-5.6 git repo which is assumed to be on the same server.

**Note:**

webscalesql requires a new GCC so install the new toolset:

## devtoolset-1.1 (GCC 4.7)

```
$ sudo wget http://people.centos.org/tru/devtools-1.1/devtools-1.1.repo -O /etc/yum.repos.d/devtools-1.1.repo
$ sudo yum install devtoolset-1.1
```

or

## devtoolset-2 (GCC 4.8)

```
$ sudo wget http://people.centos.org/tru/devtools-2/devtools-2.repo -O /etc/yum.repos.d/devtools-2.repo
$ sudo yum install devtoolset-2-binutils devtoolset-2-gcc devtoolset-2-gcc-c++ devtoolset-2-libstdc++-devel devtoolset-2-runtime 

```

## Check it looks good.

```
$ scl enable devtoolset-1.1 bash  # notice this just changes the path.
$ gcc -v                          # show that the new gcc is visible
Using built-in specs.
COLLECT_GCC=gcc
COLLECT_LTO_WRAPPER=/opt/centos/devtoolset-1.1/root/usr/libexec/gcc/x86_64-redhat-linux/4.7.2/lto-wrapper
Target: x86_64-redhat-linux
Configured with: ../configure --prefix=/opt/centos/devtoolset-1.1/root/usr --mandir=/opt/centos/devtoolset-1.1/root/usr/share/man --infodir=/opt/centos/devtoolset-1.1/root/usr/share/info --with-bugurl=http://bugzilla.redhat.com/bugzilla --enable-bootstrap --enable-shared --enable-threads=posix --enable-checking=release --disable-build-with-cxx --disable-build-poststage1-with-cxx --with-system-zlib --enable-__cxa_atexit --disable-libunwind-exceptions --enable-gnu-unique-object --enable-linker-build-id --enable-languages=c,c++,fortran,lto --enable-plugin --with-linker-hash-style=gnu --enable-initfini-array --disable-libgcj --with-ppl --with-cloog --with-mpc=/home/centos/rpm/BUILD/gcc-4.7.2-20121015/obj-x86_64-redhat-linux/mpc-install --with-tune=generic --with-arch_32=i686 --build=x86_64-redhat-linux
Thread model: posix
gcc version 4.7.2 20121015 (Red Hat 4.7.2-5) (GCC) 
```
- Extract spec file with rpm -ivh MySQL-5.6.14-1.el6.src.rpm
- Get my repo: git clone https://github.com/sjmudd/webscalesql-rpm.git
The build script has been adjusted to find the right locations for where
to put the different files needed to build a new rpm.
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
not be).

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

Feedback welcome to Simon J Mudd <sjmudd@pobox.com>.
