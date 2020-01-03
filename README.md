# travis-helper-cpan-pm
Install CPAN modules on travis-ci using CPAN.pm, not cpanm

CPAN.pm has at least two advantages over cpanm:
* distroprefs support
* automatic installation of dependent system packages using the CPAN::Plugin::Sysdeps plugin

Use the plugin like this in your `.travis.yml`:

```
before_install:
  - git clone https://github.com/eserte/travis-helper-cpan-pm.git
  - ./travis-helper-cpan-pm/travis-helper-cpan-pm --distroprefs=https://github.com/eserte/srezic-cpan-distroprefs --enable-sysdeps
install:
script:
  - cpan -t .
```

## Options
* --distroprefs=clone-url: specify a clonable git url to a distroprefs directory. Currently the repository should be flat i.e.
there should be no intermediate directory to the .yml files
* --enable-sysdeps: enable automatic installation of system packages
* --dry-run: just show the steps, don't execute anything
* --no-sudo: don't use sudo when installing
* --mirrorurl=url: use another CPAN mirror URL. May be specified multiple times.

## Limitations
CPAN::Plugin::Sysdeps has a static mapping of CPAN modules to system packages.
This mapping is maintained manually and currently has only limited support for
the Ubuntu distributions used at travis-ci.

## Other uses
This helper script may also work elsewhere, for example on appveyor using cygwin's perl:
```
  - if %perl%==cygwinperl c:\cygwin\bin\sh -c "PATH=/usr/bin:/bin; (cd /tmp && git clone https://github.com/eserte/travis-helper-cpan-pm.git && ./travis-helper-cpan-pm/travis-helper-cpan-pm --no-sudo && cpan Foo::Bar ...)"
```

Or within a Dockerfile:
```
RUN apt-get -y install git perl-modules ca-certificates
RUN git clone --depth=1 https://github.com/eserte/travis-helper-cpan-pm.git /tmp/travis-helper-cpan-pm
RUN /tmp/travis-helper-cpan-pm/travis-helper-cpan-pm --distroprefs=https://github.com/eserte/srezic-cpan-distroprefs --enable-sysdeps --no-sudo
```

Or in Github Actions (see also .github/workflows/test.yml):
```
    ...
    steps:
    - uses: actions/checkout@v1
    - name: before_install
      run: |
        ./travis-helper-cpan-pm --distroprefs=https://github.com/eserte/srezic-cpan-distroprefs --enable-sysdeps --sysdeps-branch=master
    ...
```
